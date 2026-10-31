# AWS Security by Design

Build, break and secure a production application on AWS, and learn to defend every security decision in a system design interview.

This is not a certification course. There are no exam dumps and no service-by-service tour. We take one real application, put it on AWS in the most insecure way possible, and then fix it step by step. Every AWS service you learn appears only because our architecture has a problem it solves.

---

## Who this course is for

- DevOps, Cloud and Backend engineers who know basic AWS and want to move into Cloud Security or Cloud Architect roles
- Engineers preparing for system design interviews where security questions come up
- Anyone who has learned AWS security services one by one, but cannot yet design a secure architecture end to end

---

## How every module works

Every module follows the same three steps.

1. Context: our application has a real problem
2. Concept: the security idea and the AWS service that solves it
3. Lab: we build it, break it, and fix it in a real AWS account

Each module ends with interview questions, so you can explain what you built the way an interviewer expects.

---

## The five questions

The whole course is built around five questions. By the end, you should be able to walk any architecture through all five.

1. Who can reach it?
2. Who can act?
3. What is exposed if they get in?
4. Who is knocking at the door?
5. How would you know?

---

## The application: NovaBank

We use one application for the entire course. NovaBank is a fictional retail bank.

| Component | What it does |
| --- | --- |
| web | Frontend, static files on S3 |
| api | Python FastAPI service for accounts, transfers and document upload |
| db | PostgreSQL on RDS with customer and transaction data |
| docs | S3 bucket for customer KYC documents |
| worker | Background job that calls an external credit score API |

The architecture evolves module by module. We start with a version that has every common mistake, and we end with a design you can defend in an interview.

---

## Course content

### Module 1: Threat Model First, Then Secure the Account

- Context: the bank wants NovaBank live. Before deploying anything, what can go wrong?
- Concept: assets, threats, CIA triad, authentication vs authorization vs audit, shared responsibility model, blast radius
- Lab: deploy the deliberately insecure version (public EC2, public database, access keys in code, SSH open to the world), then secure the account itself: root MFA, IAM Identity Center, budget alert, CloudTrail
- Interview: How do you threat model a new application? What are the first five things you do in a new AWS account?

### Module 2: The VPC as a Security Boundary

- Context: the database is reachable from the internet
- Concept: CIDR planning, public, private and isolated subnets, route tables, Internet Gateway, NAT Gateway, Security Groups vs NACLs, Security Group referencing
- Lab: build a three tier VPC, place the ALB, API and database in the right tiers, chain Security Groups, add a NACL rule
- Interview: What actually makes a subnet public? Security Group or NACL to block one bad IP, and why?

### Module 3: Private Access and Controlled Egress

- Context: a compromised server could copy our data anywhere on the internet
- Concept: gateway and interface VPC endpoints, endpoint policies, SSM Session Manager instead of bastion hosts, Route 53 Resolver DNS Firewall, data exfiltration
- Lab: add VPC endpoints for S3 and Secrets Manager, remove SSH completely, allow only the credit score API domain, then try to exfiltrate data and watch it fail
- Interview: How do you stop a compromised EC2 from sending data to the attacker's own S3 bucket?

### Module 4: The Front Door - CloudFront, WAF and TLS

- Context: login floods, SQL injection attempts, and someone is hitting the load balancer directly
- Concept: CloudFront as the only entry point, AWS WAF managed rules and rate limiting, Shield, ACM and TLS, locking the origin to CloudFront only
- Lab: put the frontend and API behind CloudFront, attach WAF, send attack traffic and watch it get blocked, prove the ALB can no longer be reached directly
- Interview: How do you stop attackers bypassing CloudFront? How do you roll out WAF without breaking real users?

### Module 5: IAM - Who Can Act?

- Context: the API runs as administrator and developers have long lived access keys
- Concept: IAM roles for workloads, policy evaluation logic, identity vs resource policies, permission boundaries, PassRole privilege escalation, cross account roles and the confused deputy, IAM Access Analyzer, Organizations and SCPs
- Lab: replace access keys with a role, generate a least privilege policy, show a privilege escalation and close it with a permission boundary, apply an SCP that blocks disabling CloudTrail
- Interview: Explain the IAM policy evaluation order. What is the confused deputy problem? Can an SCP grant permissions?

### Module 6: Data Protection - What Is Exposed If They Get In?

- Context: an auditor asks who can read customer data if a backup or snapshot is stolen
- Concept: envelope encryption, KMS key policies, AWS managed vs customer managed keys, Secrets Manager and rotation, S3 Block Public Access, presigned URLs, Object Lock, RDS encryption
- Lab: encrypt RDS and S3 with customer managed keys, move the database password to Secrets Manager with rotation, upload documents only through presigned URLs, share a snapshot and prove it is useless without the key
- Interview: Explain envelope encryption. Who can decrypt data encrypted with a KMS key? How do you protect backups from ransomware?

### Module 7: Detection and Incident Response - How Would You Know?

- Context: 2 AM, GuardDuty reports the API role being used from an unknown country
- Concept: which log answers which question (CloudTrail, VPC Flow Logs, DNS logs, WAF logs), GuardDuty, Security Hub, AWS Config, EventBridge auto remediation, incident response lifecycle
- Lab: enable centralised logging and threat detection, build an auto remediation that closes an open SSH rule within a minute, then run a game day: investigate a planted incident using only the logs and write the incident report
- Interview: An access key leaked on GitHub. Walk me through the first 30 minutes.

### Module 8: Enterprise Security Design

- Context: the bank grows to many VPCs, many accounts, partner banks and an on premises data centre
- Concept: VPC peering vs Transit Gateway vs PrivateLink, centralised inspection with AWS Network Firewall, multi account strategy (Security, Log Archive, Prod, Dev), data perimeter, Site to Site VPN vs Direct Connect, where containers and EKS change the picture
- Lab: expose the NovaBank API to a partner VPC privately with PrivateLink, then whiteboard the full enterprise design
- Interview: A partner needs one API, not your whole network. What do you use? Why use many accounts instead of one?

### Module 9: Capstone - System Design Mock Interview

- A new problem you have not seen before, run like a real senior system design interview
- Design the secure architecture, walk it through the five questions, defend your trade offs
- Answer "why not X?" follow ups and handle a live incident twist on your own design
- Written feedback against a six point rubric: threat model, network, identity, data protection, detection, trade offs

---

## What this course deliberately skips

To keep the course focused on design and interviews, we skip certification depth that rarely comes up in real interviews.

- BGP tuning, Direct Connect configuration, Cloud WAN, multicast
- CloudHSM internals, Nitro Enclaves, Audit Manager details
- Exam only edge cases and service trivia

---

## Repository structure

```
aws-security-by-design/
  README.md
  app/                  NovaBank application code
  terraform/            One folder per module, so you can rebuild fast
  runbooks/             Step by step lab guide for every module
  diagrams/             draw.io architecture for every module
  interview/            Interview questions and model answers
  capstone/             Capstone problems and the assessment rubric
```

---

## Prerequisites

- Your own AWS account (do not use a work or client account)
- Basic AWS knowledge: EC2, S3, IAM users and roles
- AWS CLI and Terraform installed
- Basic Linux and Git

---

## Before you start: cost and safety

- Set an AWS Budgets alert before your first lab
- NAT Gateway, interface endpoints, Network Firewall, ALB and RDS are billed by the hour. Destroy them after every lab with terraform destroy
- GuardDuty, Security Hub and Inspector have free trials. Enable them only when you reach Module 7
- Never use real customer data. NovaBank uses generated test data

---

## Connect with me

Rahul Chaubey

- YouTube: https://www.youtube.com/@BuildAutomateArchitect
- System Design playlist: https://www.youtube.com/playlist?list=PLeAHTLMwFYQ0
- Telegram: https://t.me/BuildAutomateArchitect
- Website: https://rahulchaubeyconsulting.com/
