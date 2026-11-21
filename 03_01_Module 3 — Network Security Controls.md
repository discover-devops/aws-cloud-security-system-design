# Module 3 — Network Security Controls
## Designing, Implementing and Validating Network Security on AWS

**Programme:** AWS Cloud Security Architect — Design, Build & Defend  
**Reference Application:** Production Online Banking Application  
**Focus:** Security Groups, Network ACLs, Traffic Filtering, Network Segmentation and Network Path Validation

---

# 1. Learning Objectives

By the end of this module, you should be able to:

- Explain the role of Security Groups and Network ACLs.
- Understand stateful and stateless traffic filtering.
- Design inbound and outbound network rules.
- Apply least-privilege network access.
- Implement network segmentation and isolation.
- Understand North-South and East-West traffic.
- Troubleshoot permitted and denied network paths.
- Make and defend network security architecture decisions.

# PART 1 — CONTEXT

## 2. The Production Problem: Who Can Talk to Whom?

Imagine that we have deployed our online banking application on AWS.

Our infrastructure contains:

- An Application Load Balancer exposed to the internet.
- EC2 application servers running the banking application.
- An Amazon RDS database storing customer information.
- An administrative access mechanism for engineers.

Our VPC and subnets are already created.

We have also installed Apache on an Ubuntu EC2 instance and successfully accessed its webpage from the internet.

Now consider a few questions.

1. Should anyone on the internet be able to access our EC2 application server directly?
2. Should the database accept connections from the internet?
3. Should an application server be allowed to communicate with every other server in the VPC?
4. Should an engineer be able to connect directly to the database?
5. What happens if an attacker compromises our web server?

These are not just networking questions. They are security architecture decisions.

A VPC provides network isolation, but it does not automatically guarantee that every communication path inside it is secure.

We need additional controls to decide:

- Who can initiate a connection?
- Which resource can receive the connection?
- Which port and protocol are permitted?
- Which traffic should be rejected?
- How do we limit the impact of a compromised resource?

This is where Security Groups and Network ACLs become important.

## 3. Our Reference Architecture

The online banking application follows a three-tier architecture.

```text
                         INTERNET
                            |
                            |
                    Application Users
                            |
                            v
                  Application Load Balancer
                            |
                     Public Subnet
                            |
              +-------------+-------------+
              |                           |
              v                           v
        Private App Subnet A        Private App Subnet B
              |                           |
           EC2 App                    EC2 App
              |                           |
              +-------------+-------------+
                            |
                            v
                    Private DB Subnets
                            |
                       Amazon RDS
```

Our network security objective is straightforward:

- Customers should access the application through the load balancer.
- Application servers should accept application traffic only from the load balancer.
- The database should accept database connections only from authorized application servers.
- Administrative access should follow a controlled management path.
- A compromised application server should not automatically gain access to every resource in the VPC.

**Architecture principle:** Every tier should communicate only with the resources it genuinely needs.

---

# PART 2 — CONCEPT

## 4. Security Groups

### 4.1 What is a Security Group?

A Security Group is a virtual firewall associated with AWS resources, such as EC2 instances and network interfaces.

It controls inbound and outbound traffic.

We can define:

- Which traffic is allowed to reach a resource.
- Which traffic is allowed to leave a resource.

Security Groups are stateful.

This means that when a connection is permitted, the response traffic for that connection is automatically allowed, regardless of the outbound or inbound rules that would otherwise apply to that response.

Security Groups support allow rules. They do not support explicit deny rules.

### 4.2 Example: Our Banking Application

Consider the following Security Groups:

| Security Group | Attached resource | Purpose |
|---|---|---|
| SG-ALB | Application Load Balancer | Accept customer web traffic |
| SG-APP | EC2 application servers | Accept traffic from ALB |
| SG-DB | Amazon RDS | Accept database traffic from application servers |
| SG-ADMIN | Management resources, if used | Controlled administration |

Now let's define the traffic rules.

### 4.3 Security Group Rules

**SG-ALB — Load Balancer**

Inbound rules:

| Type | Port | Source | Action |
|---|---:|---|---|
| HTTP | 80 | 0.0.0.0/0 | Allow |
| HTTPS | 443 | 0.0.0.0/0 | Allow |

In production, HTTPS should normally be the customer-facing entry point. HTTP can be redirected to HTTPS where required.

Outbound rules:

| Type | Port | Destination | Action |
|---|---:|---|---|
| Custom TCP | 8080 | SG-APP | Allow |

The application port is an example. Use the actual port configured for the application and its target group.

**SG-APP — Application Servers**

Inbound rules:

| Type | Port | Source | Action |
|---|---:|---|---|
| Custom TCP | 8080 | SG-ALB | Allow |

Outbound rules:

| Type | Port | Destination | Action |
|---|---:|---|---|
| Database TCP | 5432 | SG-DB | Allow |
| HTTPS | 443 | Approved destinations | Allow |

Port 5432 is an example for PostgreSQL. For Amazon RDS running MySQL, the usual database port is 3306.

**SG-DB — Database**

Inbound rules:

| Type | Port | Source | Action |
|---|---:|---|---|
| PostgreSQL | 5432 | SG-APP | Allow |

Outbound rules:

Keep only the outbound access required by the database workload and its operational requirements.

Notice something important:

We are not allowing the entire VPC CIDR to access the database.

We are allowing access from the application Security Group.

This is a more precise security boundary.

### 4.4 Why Use Security Group References?

Instead of specifying an individual EC2 IP address as the source, we can reference another Security Group.

For example:

```text
SG-DB inbound rule:

Protocol: TCP
Port: 5432
Source: SG-APP
```

This means that network interfaces associated with SG-APP can communicate with the database on the specified port, subject to the other network controls.

If an application server is replaced or its private IP changes, we do not need to update the database rule just because the IP changed.

**Architect's takeaway:** Prefer Security Group references for communication between application tiers when they express the intended trust relationship.

---

## 5. Network ACLs (NACLs)

### 5.1 What is a Network ACL?

A Network Access Control List is a network-level firewall associated with a subnet.

It controls traffic entering and leaving that subnet.

Unlike Security Groups, Network ACLs are stateless.

This means inbound and outbound traffic must be evaluated independently.

A Network ACL supports both:

- Allow rules.
- Deny rules.

### 5.2 Where Does a NACL Apply?

A Network ACL is associated with a subnet.

All network interfaces in that subnet are subject to its rules.

For example:

```text
VPC
 |
 +-- Public Subnet
 |       |
 |       +-- NACL-PUBLIC
 |
 +-- Private App Subnet
 |       |
 |       +-- NACL-APP
 |
 +-- Private DB Subnet
         |
         +-- NACL-DB
```

A subnet has one associated Network ACL at a time. The same Network ACL can be associated with multiple subnets.

### 5.3 NACL Rule Evaluation

Network ACL rules are evaluated in ascending rule-number order.

The first matching rule determines the result.

Example:

| Rule number | Type | Port | Source | Action |
|---:|---|---:|---|---|
| 100 | TCP | 22 | 203.0.113.10/32 | Allow |
| 110 | TCP | 22 | 0.0.0.0/0 | Deny |
| 120 | All traffic | All | 0.0.0.0/0 | Allow |
| * | All traffic | All | All | Deny |

The first matching rule wins.

The final `*` rule is the implicit deny.

**Important:** Rule order matters. A broad allow rule placed before a more specific deny rule can change the outcome.

---

## 6. Security Groups vs Network ACLs

This is one of the most important concepts in AWS network security.

| Feature | Security Group | Network ACL |
|---|---|---|
| Scope | Resource / network interface | Subnet |
| Stateful or stateless | Stateful | Stateless |
| Rules supported | Allow | Allow and Deny |
| Rule evaluation | All applicable rules are evaluated | Lowest rule number first |
| Return traffic | Automatically allowed for tracked connections | Must be explicitly allowed |
| Default behavior | Default SG allows inbound from itself and outbound traffic | Default NACL allows all inbound and outbound traffic |
| Common use | Resource-level access control | Subnet-level traffic filtering |

### 6.1 Understand the Difference Through an Example

An application server sends a request to a database.

```text
Application Server
        |
        | TCP 5432
        v
      Database
        |
        | Response traffic
        v
Application Server
```

With Security Groups:

- The database Security Group permits inbound TCP 5432 from SG-APP.
- The connection is tracked.
- Response traffic is automatically permitted by the stateful Security Group.

With Network ACLs:

- The database subnet NACL must allow inbound TCP 5432.
- The application subnet NACL must allow the return traffic.
- The NACLs must independently permit the relevant traffic in both directions.

This distinction becomes especially important when troubleshooting connectivity.

---

## 7. Stateful vs Stateless Filtering

### Stateful Filtering

A stateful firewall remembers the connection state.

Example:

An application server initiates a connection to a database.

```text
APP SERVER  ---------- TCP 5432 ----------> DATABASE
APP SERVER  <--------- RESPONSE ---------- DATABASE
```

If the connection is permitted, the response traffic is automatically allowed by the stateful Security Group.

### Stateless Filtering

A stateless firewall does not automatically allow the response traffic.

The inbound and outbound rules must independently permit the required packets.

For example:

```text
APP SERVER  ---------- TCP 5432 ----------> DATABASE
APP SERVER  <--------- RESPONSE ---------- DATABASE
```

A NACL must allow both directions.

For return traffic, ephemeral ports are important.

### 7.1 What Are Ephemeral Ports?

When a client initiates a TCP connection, it typically uses a temporary source port.

These temporary ports are called ephemeral ports.

For example:

```text
Client: 10.0.2.10:49152
             |
             | TCP connection to port 5432
             v
Database: 10.0.3.10:5432
```

The response is sent back to the client's temporary port.

A stateless NACL must allow the necessary return traffic to that ephemeral port range.

The exact range depends on the operating system and client implementation. Linux commonly uses a range such as 32768–60999, but other systems can use different ranges.

Do not blindly open every ephemeral port to the internet. Scope the source and destination to the required subnet or workload paths.

---

# PART 3 — NETWORK TRAFFIC AND SEGMENTATION

## 8. Inbound and Outbound Traffic

Inbound traffic is traffic entering a resource or network boundary.

Outbound traffic is traffic leaving a resource or network boundary.

For our banking application:

| Traffic | Direction | Example |
|---|---|---|
| Customer to ALB | Inbound | HTTPS 443 |
| ALB to application | Inbound to app | TCP 8080 |
| Application to database | Outbound from app | TCP 5432 |
| Application to database | Inbound to DB | TCP 5432 |
| Application to approved external API | Outbound | HTTPS 443 |

The same connection is inbound from the receiver's perspective and outbound from the sender's perspective.

Security Architects must evaluate both perspectives.

---

## 9. Least-Privilege Network Access

### 9.1 What Is Least Privilege?

Least privilege means granting only the minimum access necessary to perform a task.

This applies to IAM permissions, but it also applies to network access.

Consider this database rule:

```text
Type: All traffic
Source: 10.0.0.0/16
```

This permits a much wider set of communication paths than our banking application needs.

A better rule is:

```text
Type: PostgreSQL
Protocol: TCP
Port: 5432
Source: SG-APP
```

Why is this better?

- Only the application tier is authorized.
- Only the required protocol is allowed.
- Only the database port is allowed.
- Unrelated resources cannot use this rule to reach the database.

### 9.2 Four Questions Before Creating a Rule

Before adding a network rule, ask:

1. Who needs to communicate?
2. What is the destination?
3. Which protocol and port are required?
4. Is the access required permanently or only temporarily?

A Security Architect should be able to justify every rule.

---

## 10. Network Segmentation and Isolation

Network segmentation means dividing a network into smaller security zones.

Why do we do this?

Because not every workload should have the same level of access.

For our banking application:

| Zone | Workloads | Trust level |
|---|---|---|
| Public zone | Internet-facing ALB | Internet-facing |
| Application zone | EC2 application servers | Restricted |
| Database zone | RDS | Highly restricted |
| Management zone | Administrative access resources | Controlled |

### 10.1 What Happens Without Segmentation?

Imagine that all components are deployed in one subnet and broadly allow traffic from the entire VPC.

If an attacker compromises an application server, the attacker may have more opportunities to reach other systems.

This increases the attack surface and potential blast radius.

### 10.2 What Happens With Segmentation?

We create separate network zones and restrict communication between them.

```text
PUBLIC ZONE
    |
    | HTTPS
    v
APPLICATION LOAD BALANCER
    |
    | TCP 8080
    v
APPLICATION ZONE
    |
    | TCP 5432
    v
DATABASE ZONE
```

The application tier can access the database.

The public internet cannot directly access the database.

Administrative access follows a separate controlled path.

**Architect's takeaway:** Segmentation limits lateral movement and reduces the blast radius of a compromised workload.

---

## 11. North-South vs East-West Traffic

These terms describe two important traffic patterns.

### 11.1 North-South Traffic

North-South traffic flows into or out of the environment.

Examples:

- Customer accessing the banking website.
- Application server accessing an approved external API.
- Engineer connecting through an approved management path.

```text
INTERNET
    |
    v
 AWS VPC
```

Security controls can include:

- Internet-facing load balancers.
- Security Groups.
- Network ACLs.
- AWS WAF.
- Network Firewall and egress controls where required.

### 11.2 East-West Traffic

East-West traffic flows between resources inside the environment.

Examples:

- ALB communicating with application servers.
- Application servers communicating with databases.
- One internal service communicating with another service.

```text
EC2 APPLICATION
       |
       v
   AMAZON RDS

SERVICE A
       |
       v
   SERVICE B
```

East-West traffic is particularly important because attackers may attempt lateral movement after compromising one workload.

Security controls can include:

- Security Group references.
- Subnet segmentation.
- Network ACLs.
- AWS Network Firewall where appropriate.
- Application-level authentication and authorization.

**Important:** Being inside the VPC does not mean a workload should automatically be trusted.

---

# PART 4 — REAL-WORLD SECURITY SCENARIOS AND SOLUTIONS

## Scenario 1 — Public Database Exposure

### Situation

A developer creates an RDS database and configures its Security Group as follows:

```text
Inbound:
Protocol: TCP
Port: 5432
Source: 0.0.0.0/0
```

The database is publicly accessible.

### Security Risk

Anyone who can reach the database endpoint can attempt to establish a connection.

This creates unnecessary exposure and increases the risk of unauthorized access.

### Architect's Solution

1. Disable public accessibility for the database unless a specific, justified requirement exists.
2. Place the database in private database subnets.
3. Create a dedicated database Security Group.
4. Allow inbound TCP 5432 only from the application Security Group.
5. Use appropriate database authentication and encryption.
6. Review database connectivity and access logs.

### Expected Rule

```text
SG-DB

Inbound:
TCP 5432
Source: SG-APP
```

### Validation

- Attempt database connectivity from the authorized application tier.
- Attempt connectivity from an unauthorized EC2 instance.
- Confirm that the unauthorized connection is rejected.

**Architect's lesson:** A private database should not depend only on application authentication. Network-level restrictions are also necessary.

---

## Scenario 2 — Application Server Is Directly Accessible From the Internet

### Situation

An organization deploys an EC2 application server with:

```text
Inbound:
TCP 22
Source: 0.0.0.0/0

TCP 8080
Source: 0.0.0.0/0
```

### Security Risk

- SSH is exposed to the entire internet.
- The application server can be accessed directly, bypassing the intended load balancer.
- Attackers have additional entry points to target.

### Architect's Solution

1. Remove public inbound access to the application server.
2. Place the instance in a private application subnet.
3. Allow application traffic only from the ALB Security Group.
4. Use AWS Systems Manager Session Manager for administrative access where feasible.
5. Keep the application server's outbound access restricted to required destinations.

### Expected Rule

```text
SG-APP

Inbound:
TCP 8080
Source: SG-ALB
```

### Validation

- Access the application through the ALB.
- Attempt direct internet access to the EC2 instance.
- Confirm that direct access is unavailable.

**Architect's lesson:** The load balancer should be the intended customer entry point, not an optional layer that can be bypassed.

---

## Scenario 3 — Security Group Allows Traffic, But the Application Is Not Reachable

### Situation

An EC2 instance has:

```text
Security Group:
Inbound TCP 80
Source 0.0.0.0/0
```

However, the webpage is not accessible.

### Possible Causes

- Apache is not running.
- The instance has no public IPv4 address.
- The subnet does not have a route to the Internet Gateway.
- The subnet NACL blocks traffic.
- The operating system firewall blocks port 80.
- The application is listening on a different port.

### Architect's Troubleshooting Approach

Check each layer independently.

| Layer | What to verify |
|---|---|
| Internet connectivity | Public IPv4 address |
| Route table | 0.0.0.0/0 → IGW |
| Security Group | Inbound TCP 80 |
| NACL | Inbound TCP 80 and required return traffic |
| OS firewall | Port 80 permitted |
| Application | Apache active and listening |

Useful Linux commands:

```bash
sudo systemctl status apache2
```

```bash
sudo ss -tlnp
```

```bash
curl http://localhost
```

### Validation

If localhost works but external access fails, investigate the AWS network path and firewall controls.

**Architect's lesson:** A Security Group rule alone does not guarantee end-to-end connectivity.

---

## Scenario 4 — NACL Blocks Database Return Traffic

### Situation

The application server initiates a database connection.

The database NACL allows inbound TCP 5432.

However, the database connection times out.

### Security Problem

The NACL is stateless.

The return traffic must also be allowed by the relevant NACL rules.

### Architect's Solution

Review the NACLs on both the application and database subnets.

Ensure that:

- Database subnet NACL allows the required inbound database port.
- Database subnet NACL allows the necessary outbound response traffic.
- Application subnet NACL permits the return traffic to the client's ephemeral port.
- Application subnet NACL permits the outbound database connection.

The rules should be scoped to the relevant subnet CIDRs and required port ranges.

### Validation

Test the database connection again and inspect VPC Flow Logs for rejected traffic where available.

**Architect's lesson:** Stateful Security Groups and stateless NACLs must be considered together.

---

## Scenario 5 — Compromised Application Server and Lateral Movement

### Situation

An attacker compromises an application server.

The server has broad network access to multiple internal resources.

The attacker attempts to reach:

- Other EC2 instances.
- Internal services.
- Database systems.
- Administrative endpoints.

### Security Risk

The attacker may move laterally through the environment.

A single compromised server can become an entry point to other systems.

### Architect's Solution

1. Segment application and database workloads.
2. Use dedicated Security Groups for different workload roles.
3. Restrict application-to-database access to the required database port.
4. Avoid broad VPC-wide inbound rules.
5. Restrict administrative access to approved management paths.
6. Review east-west traffic.
7. Use monitoring and threat detection to identify suspicious communication.

### Validation

- Attempt unauthorized connections between application instances.
- Attempt access to the database from an unrelated instance.
- Confirm that only intended paths are permitted.

**Architect's lesson:** Network segmentation is not just about organization. It is a containment strategy.

---

## Scenario 6 — A Developer Requests Temporary SSH Access

### Situation

A developer asks:

"Please open port 22 to the entire internet. I need to troubleshoot the production EC2 instance."

### Security Architect's Decision

Do not immediately approve the request.

Ask:

- Why is SSH access required?
- Can Session Manager be used instead?
- Which engineer requires access?
- For how long?
- Is the access auditable?

### Preferred Solution

Use AWS Systems Manager Session Manager, provided the instance has the required IAM permissions, SSM Agent configuration, and network connectivity to Systems Manager.

If SSH is genuinely required:

- Restrict source to the engineer's trusted IP.
- Use approved keys and identity controls.
- Set a temporary access window.
- Remove the rule after troubleshooting.
- Record and review the change.

**Architect's lesson:** Access should be based on business need, not convenience.

---

# PART 5 — HANDS-ON LAB AND VALIDATION

## 12. Lab Objective

Use our existing Banking Production VPC and Ubuntu Apache EC2 instance to demonstrate how network security controls permit and deny traffic.

### Lab 1 — Verify Public Web Access

Confirm that the Apache website is accessible from the internet.

Required configuration:

```text
Inbound:
HTTP TCP 80
Source: 0.0.0.0/0
```

Test:

```text
http://YOUR_PUBLIC_IP
```

Expected result: Apache webpage loads.

### Lab 2 — Restrict SSH Access

Review the SSH inbound rule.

If SSH is required, change the source from:

```text
0.0.0.0/0
```

to:

```text
YOUR_PUBLIC_IP/32
```

Expected result: Only the approved source can initiate SSH access.

### Lab 3 — Test Security Group Denial

1. Remove the HTTP inbound rule temporarily.
2. Try opening the webpage again.
3. Observe the failure.
4. Restore the HTTP rule.

Expected result: External HTTP access is denied while the rule is absent.

### Lab 4 — Understand Network ACL Behavior

Review the NACL associated with the public subnet.

Identify:

- Inbound rules.
- Outbound rules.
- Rule numbers.
- Allow and deny actions.
- The implicit deny rule.

Do not modify production NACL rules without a planned test and recovery path.

### Lab 5 — Validate Network Paths

Create a simple traffic validation matrix.

| Source | Destination | Port | Expected result |
|---|---|---:|---|
| Internet | Public web server | 80 | Allow |
| Internet | EC2 SSH | 22 | Allow only from approved IP |
| Internet | Private database | 5432 | Deny |
| Application tier | Database | 5432 | Allow |
| Unrelated EC2 | Database | 5432 | Deny |

The result depends on the actual routes, Security Groups, NACLs, host firewalls and service configuration. Validate the entire path rather than assuming a single rule determines connectivity.

---

# PART 6 — PRODUCTION THINKING

## 13. How a Security Architect Reviews Network Rules

Before approving a network security design, review the following:

### Network Access Review

- Is every internet-facing entry point documented?
- Are application workloads isolated from direct internet access?
- Are databases in private subnets?
- Are inbound and outbound rules justified?
- Are broad CIDR ranges avoided where possible?
- Are Security Group references used appropriately?
- Are NACL rules ordered correctly?
- Are ephemeral return ports considered?
- Is administrative access controlled?

### Segmentation Review

- Are public, application and database tiers separated?
- Can a compromised application server access unrelated resources?
- Are east-west communication paths restricted?
- Is the blast radius limited?

### Operational Review

- Are network changes approved and documented?
- Can denied traffic be investigated?
- Are unnecessary rules periodically removed?
- Is there a rollback plan?
- Are connectivity tests included after changes?

## 14. Important AWS Troubleshooting Tools

| Tool | Purpose |
|---|---|
| VPC Reachability Analyzer | Analyzes whether a network path is reachable between supported AWS resources |
| VPC Flow Logs | Captures information about IP traffic to and from network interfaces |
| EC2 Network Interfaces | Helps identify attached Security Groups and network configuration |
| Security Group console | Reviews resource-level firewall rules |
| Network ACL console | Reviews subnet-level filtering rules |
| Linux `ss` | Checks listening ports and sockets |
| Linux `curl` | Tests HTTP connectivity |
| Linux `nc` | Tests TCP connectivity |

**Important distinction:** Reachability Analyzer evaluates network configuration paths. It does not prove that the application is healthy or that the application protocol is functioning correctly.

---

# PART 7 — INTERVIEW QUESTIONS AND ANSWERS

## Basic Level

### Q1. What is a Security Group in AWS?

A Security Group is a stateful virtual firewall associated with an AWS resource or network interface. It controls inbound and outbound traffic using allow rules.

### Q2. What is a Network ACL?

A Network ACL is a stateless firewall associated with a subnet. It supports both allow and deny rules and evaluates rules in ascending rule-number order.

### Q3. What is the main difference between a Security Group and a NACL?

A Security Group controls traffic at the resource or network interface level and is stateful. A NACL controls traffic at the subnet level and is stateless.

### Q4. What does stateful filtering mean?

Stateful filtering tracks connections. When a connection is allowed, response traffic is automatically permitted by the stateful control.

### Q5. What does stateless filtering mean?

Stateless filtering evaluates each direction independently. Both the request and response traffic must be allowed by the relevant rules.

### Q6. Can a Security Group deny traffic?

No. Security Groups support allow rules only. Traffic that is not permitted by the applicable rules is not allowed.

### Q7. Can a NACL explicitly deny traffic?

Yes. Network ACLs support both allow and deny rules.

### Q8. What is the purpose of ephemeral ports?

Ephemeral ports are temporary client-side ports used during network connections. They are important when configuring stateless firewall rules to permit return traffic.

## Intermediate Level

### Q9. Why should an RDS database not be publicly accessible?

A database contains sensitive information and generally does not need direct internet access. Keeping it in private subnets and allowing access only from authorized application workloads reduces exposure.

### Q10. Why should we reference a Security Group instead of using an IP address?

Security Group references express workload-level trust relationships. They avoid depending on individual instance IP addresses when resources change.

### Q11. Can a Security Group and NACL be used together?

Yes. They provide different layers of network filtering. Security Groups control access at the resource level, while NACLs provide subnet-level filtering.

### Q12. If a Security Group allows HTTP traffic but the webpage is unavailable, what would you check?

I would check the public IP, route table, Internet Gateway, NACL, operating system firewall, Apache service and listening port.

### Q13. What is network segmentation?

Network segmentation divides a network into smaller security zones and restricts communication between them based on business and security requirements.

### Q14. What is the difference between North-South and East-West traffic?

North-South traffic enters or leaves an environment. East-West traffic moves between internal resources or workloads.

### Q15. Why is unrestricted outbound access also a security concern?

A compromised workload could communicate with unauthorized external destinations, potentially enabling data exfiltration, command-and-control communication or other malicious activity.

## Advanced / Scenario-Based Questions

### Q16. Your application server can connect to the database, but the database response never reaches the application. What could be wrong?

I would inspect both subnet NACLs because they are stateless. I would verify that the database response traffic is permitted to the application's ephemeral port range. I would also inspect Security Groups, routes and VPC Flow Logs.

### Q17. An EC2 instance is compromised. How would you prevent lateral movement?

I would isolate the affected instance, restrict its Security Group access, review its network paths, segment workloads and restrict east-west traffic. I would also investigate suspicious connections and follow the incident response process.

### Q18. A developer requests a rule allowing all traffic from the entire VPC CIDR. Would you approve it?

Not without justification. I would identify the required source, destination, protocol and port. I would replace broad access with a least-privilege rule and document the exception if broader access were genuinely necessary.

### Q19. How would you design network security for a three-tier application?

I would place the internet-facing load balancer in public subnets, application workloads in private subnets, and databases in private database subnets. I would use separate Security Groups and permit only the required communication between tiers.

### Q20. How would you validate that the database cannot be accessed from the internet?

I would check database public accessibility, subnet placement, route tables, Security Groups and NACLs. I would then perform authorized connectivity tests from appropriate external and internal test locations.

### Q21. What would you do if a business application needs outbound internet access but must not accept direct inbound internet connections?

I would place it in private subnets and use a NAT Gateway for the required outbound connectivity. I would restrict inbound access to approved internal sources and ensure there is no direct public route to the instance.

### Q22. How do you reduce the blast radius of a compromised application server?

I would use workload isolation, separate Security Groups, least-privilege network rules, private database access, controlled administrative paths and monitoring of east-west traffic.

### Q23. How would you troubleshoot an unexpected network connection failure in AWS?

I would follow the path layer by layer:

1. Source and destination IP addresses.
2. Subnet and route tables.
3. Security Groups.
4. Network ACLs.
5. Network interfaces.
6. Operating system firewall.
7. Application listening port.
8. VPC Flow Logs and Reachability Analyzer where applicable.

I would avoid changing multiple controls simultaneously because that makes root-cause analysis difficult.

---

# PART 8 — ARCHITECT'S DESIGN CHALLENGE

## 15. Design a Secure Network for an Online Banking Application

### Business Requirement

A bank wants to deploy a customer-facing online banking application on AWS.

Requirements:

1. Customers must access the application over HTTPS.
2. Application servers must not be directly exposed to the internet.
3. The database must remain private.
4. Only authorized application servers can communicate with the database.
5. Administrators need secure access to EC2 instances.
6. The architecture must limit lateral movement.
7. The design must support troubleshooting and network visibility.

### Your Assignment

Prepare a network security design that includes:

- Public, application and database subnets.
- Security Group design for ALB, EC2 and RDS.
- NACL strategy.
- Inbound and outbound traffic matrix.
- Administrative access approach.
- North-South and East-West traffic flows.
- Validation and troubleshooting strategy.

### Expected Architecture Decisions

| Component | Security decision |
|---|---|
| ALB | Public entry point with HTTPS |
| EC2 | Private application subnets |
| RDS | Private database subnets |
| SG-ALB | Accept customer HTTPS traffic |
| SG-APP | Accept application traffic only from SG-ALB |
| SG-DB | Accept database traffic only from SG-APP |
| NACL | Apply subnet-level filtering where justified |
| Administration | Prefer Systems Manager Session Manager |
| Monitoring | Use VPC Flow Logs and appropriate monitoring |

### Final Interview Exercise

Imagine you are presenting this architecture to a security review board.

They ask:

**"How can you prove that our database is protected from unauthorized network access?"**

Your answer should cover:

1. Database subnet placement.
2. Public accessibility configuration.
3. Database Security Group rules.
4. NACL configuration.
5. Route-table review.
6. Authorized and unauthorized connectivity tests.
7. Logging and monitoring evidence.

A strong Security Architect does not simply say, "We have configured a Security Group."

They explain the complete security boundary, demonstrate the permitted and denied paths, and provide evidence that the controls work.

---

# 16. Module Summary

In this module, we learned that AWS network security requires multiple layers of control.

| Concept | Key takeaway |
|---|---|
| Security Groups | Stateful, resource-level traffic filtering |
| NACLs | Stateless, subnet-level traffic filtering |
| Inbound rules | Control incoming traffic |
| Outbound rules | Control outgoing traffic |
| Least privilege | Allow only necessary communication |
| Segmentation | Separate workloads into security zones |
| North-South | Traffic entering or leaving the environment |
| East-West | Traffic between internal workloads |
| Validation | Prove which network paths are allowed or denied |

## The Architect's Mindset

When designing network security, never begin by asking:

"What ports should I open?"

Begin by asking:

- Which systems need to communicate?
- Why do they need to communicate?
- What is the minimum access required?
- What happens if one of these systems is compromised?
- How can we prove that unauthorized communication is blocked?

**The goal is not simply to make the application reachable. The goal is to make it reachable only through the intended and controlled paths.**

## What Comes Next?

**Module 4 — Deploy the Application on AWS**

We will build on our VPC and network security foundation and examine how application components are deployed, how the ALB distributes traffic, how application servers communicate with the database, and how deployment architecture affects security.

---
