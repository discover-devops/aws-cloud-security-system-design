# COURSE TOC

# AWS Cloud Security — System Design Approach

**Build. Secure. Break. Defend.**

A practical journey through AWS Cloud Security using real-world system design scenarios, hands-on labs, architecture decisions, and production security patterns.

---

## Module 0 — The Production Problem

### 0.1 Designing a Real-World Application

### 0.2 Application Architecture and Security

### 0.3 What Are We Actually Protecting?

### 0.4 Security Threats and Attack Surfaces

### 0.5 Confidentiality, Integrity and Availability

### 0.6 Authentication, Authorization and Auditing

### 0.7 From Application Requirements to Security Requirements

**Architecture Exercise:**
Design the initial architecture for a production banking application.

---

# Module 1 — Cloud Security Fundamentals

### 1.1 What Is Cloud Security?

### 1.2 Traditional Security vs Cloud Security

### 1.3 AWS Shared Responsibility Model

### 1.4 Security of the Cloud vs Security in the Cloud

### 1.5 Identity, Network, Application and Data Security

### 1.6 Defense in Depth

### 1.7 Least Privilege

### 1.8 Zero Trust Fundamentals

---

# Module 2 — Building the AWS Network

### 2.1 AWS Regions and Availability Zones

### 2.2 Amazon VPC

### 2.3 CIDR and IP Address Planning

### 2.4 Public and Private Subnets

### 2.5 Route Tables

### 2.6 Internet Gateway

### 2.7 NAT Gateway

### 2.8 Network Architecture for Production Applications

**Lab:**
Build a production-style VPC with public and private subnets.

---

# Module 3 — Network Security

### 3.1 Security Groups

### 3.2 Stateful Network Security

### 3.3 Network ACLs

### 3.4 Stateless Network Security

### 3.5 Security Groups vs NACLs

### 3.6 Inbound and Outbound Traffic

### 3.7 Least-Privilege Network Access

### 3.8 Network Segmentation

**Lab:**
Secure application and database communication using Security Groups.

---

# Module 4 — Deploying the Application

### 4.1 Deploying an Application on AWS

### 4.2 EC2-Based Application Architecture

### 4.3 Application Tier and Database Tier

### 4.4 Public vs Private Application Components

### 4.5 Application Security Boundaries

### 4.6 Identifying Security Weaknesses in an Initial Architecture

**Lab:**
Deploy a sample application into the AWS environment.

---

# Module 5 — Identity and Access Management

### 5.1 Why Identity Is the New Security Perimeter

### 5.2 AWS IAM

### 5.3 Users, Groups, Roles and Policies

### 5.4 Authentication vs Authorization

### 5.5 IAM Policies

### 5.6 Resource-Based Policies

### 5.7 IAM Roles

### 5.8 Workload Identity

### 5.9 Least-Privilege Access

### 5.10 MFA and Root Account Protection

### 5.11 IAM Access Analysis

**Lab:**
Create secure IAM roles and least-privilege policies for the application.

---

# Module 6 — Secrets Management

### 6.1 The Problem with Hardcoded Credentials

### 6.2 Secrets in Source Code

### 6.3 AWS Secrets Manager

### 6.4 AWS Systems Manager Parameter Store

### 6.5 Secret Encryption

### 6.6 Secret Rotation

### 6.7 Application Access to Secrets

### 6.8 Secrets Management Architecture

**Lab:**
Move application credentials from configuration files into AWS Secrets Manager.

---

# Module 7 — Encryption and Key Management

### 7.1 Why Encryption Matters

### 7.2 Encryption at Rest

### 7.3 Encryption in Transit

### 7.4 AWS Key Management Service

### 7.5 AWS KMS Keys

### 7.6 AWS Managed Keys vs Customer Managed Keys

### 7.7 Envelope Encryption

### 7.8 Encrypting EBS

### 7.9 Encrypting S3

### 7.10 Encrypting Databases

### 7.11 TLS and Certificate Management

### 7.12 AWS Certificate Manager

**Lab:**
Implement encryption at rest and in transit.

---

# Module 8 — Secure Internet-Facing Architecture

### 8.1 Designing an Internet-Facing Application

### 8.2 Application Load Balancer

### 8.3 TLS Termination

### 8.4 AWS Certificate Manager

### 8.5 Amazon CloudFront

### 8.6 AWS WAF

### 8.7 Web Application Attack Surface

### 8.8 SQL Injection

### 8.9 Cross-Site Scripting

### 8.10 Rate Limiting

### 8.11 Managed WAF Rules

### 8.12 Secure Edge Architecture

**Architecture:**

```text
Internet
   |
CloudFront
   |
WAF
   |
ALB
   |
Application
   |
Database
```

**Lab:**
Protect an internet-facing application using CloudFront, WAF, ALB and TLS.

---

# Module 9 — Private Networking and AWS Service Access

### 9.1 Why Private Resources Need AWS Service Access

### 9.2 VPC Endpoints

### 9.3 Gateway Endpoints

### 9.4 Interface Endpoints

### 9.5 Private Access to S3

### 9.6 Private Access to AWS APIs

### 9.7 NAT Gateway vs VPC Endpoint

### 9.8 Reducing Internet Exposure

### 9.9 Secure Private Application Architecture

---

# Module 10 — Logging and Auditing

### 10.1 Security Visibility

### 10.2 VPC Flow Logs

### 10.3 Understanding Network Traffic

### 10.4 Amazon CloudWatch

### 10.5 CloudWatch Logs

### 10.6 CloudTrail

### 10.7 API Activity and Audit Trails

### 10.8 Who Did What and When?

### 10.9 Centralized Logging Architecture

### 10.10 Log Retention and Protection

**Lab:**
Enable VPC Flow Logs and CloudTrail and investigate AWS activity.

---

# Module 11 — Threat Detection and Security Monitoring

### 11.1 Security Monitoring

### 11.2 Detecting Suspicious Activity

### 11.3 Amazon GuardDuty

### 11.4 AWS Security Hub

### 11.5 AWS Config

### 11.6 Security Findings

### 11.7 Security Posture Management

### 11.8 Security Alerts and Notifications

### 11.9 Centralized Security Monitoring

---

# Module 12 — Amazon S3 Security

### 12.1 S3 Security Model

### 12.2 Bucket Policies

### 12.3 IAM Policies and S3

### 12.4 S3 Block Public Access

### 12.5 Object Ownership

### 12.6 S3 Encryption

### 12.7 Versioning

### 12.8 Presigned URLs

### 12.9 Secure Application-to-S3 Architecture

### 12.10 Preventing Accidental Data Exposure

**Lab:**
Secure an S3-based document storage architecture.

---

# Module 13 — Database Security

### 13.1 Designing a Secure Database Tier

### 13.2 Private Database Subnets

### 13.3 Database Security Groups

### 13.4 Database Authentication

### 13.5 Secrets Manager Integration

### 13.6 Database Encryption

### 13.7 Backup and Recovery

### 13.8 Multi-AZ Architecture

### 13.9 Database Monitoring

### 13.10 Protecting Sensitive Data

**Architecture:**

```text
Internet
   |
CloudFront
   |
WAF
   |
ALB
   |
Application
   |
Private Database
```

---

# Module 14 — Enterprise AWS Security Architecture

### 14.1 Why One AWS Account Is Not Enough

### 14.2 AWS Organizations

### 14.3 Multi-Account Architecture

### 14.4 Production and Non-Production Isolation

### 14.5 Security Account

### 14.6 Log Archive Account

### 14.7 Service Control Policies

### 14.8 Centralized Security Controls

### 14.9 Separation of Duties

### 14.10 Break-Glass Access

**Architecture Exercise:**
Design a multi-account AWS security architecture.

---

# Module 15 — Attack the Architecture

### 15.1 Thinking Like an Attacker

### 15.2 Identifying the Attack Surface

### 15.3 Public Database Exposure

### 15.4 Open Security Groups

### 15.5 Over-Permissioned IAM Roles

### 15.6 Hardcoded Credentials

### 15.7 Public S3 Buckets

### 15.8 Unencrypted Data

### 15.9 Missing Logging

### 15.10 Detecting and Remediating Security Issues

**Lab:**
Identify security weaknesses and implement remediation.

---

# Module 16 — Incident Response

### 16.1 A Security Incident Scenario

### 16.2 Detecting the Incident

### 16.3 Investigating the Incident

### 16.4 CloudTrail Investigation

### 16.5 VPC Flow Log Investigation

### 16.6 GuardDuty Findings

### 16.7 Identifying the Compromised Resource

### 16.8 Containment

### 16.9 Credential Rotation

### 16.10 Recovery

### 16.11 Lessons Learned

**Incident Exercise:**
Investigate a simulated AWS security incident.

---

# Module 17 — DevSecOps on AWS

### 17.1 Security Across the Software Lifecycle

### 17.2 Shift-Left Security

### 17.3 Source Code Security

### 17.4 Secret Scanning

### 17.5 Dependency Scanning

### 17.6 Static Application Security Testing

### 17.7 Container Image Security

### 17.8 Infrastructure-as-Code Security

### 17.9 Terraform Security

### 17.10 Security Gates in CI/CD

### 17.11 Runtime Security

**Architecture:**

```text
Developer
    |
    v
Git
    |
    v
CI Pipeline
    |
    +---- SAST
    |
    +---- Dependency Scan
    |
    +---- Secret Scan
    |
    +---- IaC Scan
    |
    v
Build
    |
    v
Deploy
    |
    v
AWS
```

---

# Module 18 — Container and Kubernetes Security

### 18.1 Container Security Fundamentals

### 18.2 Secure Container Images

### 18.3 Image Scanning

### 18.4 IAM for Container Workloads

### 18.5 Secrets in Containerized Applications

### 18.6 Network Security for Containers

### 18.7 Amazon ECS Security

### 18.8 Amazon EKS Security

### 18.9 Workload Identity

### 18.10 Private Cluster Architecture

### 18.11 Kubernetes Security Boundaries

---

# Module 19 — Production Security Architecture

### 19.1 Bringing Everything Together

### 19.2 Defense in Depth

### 19.3 Secure Network Architecture

### 19.4 Secure Identity Architecture

### 19.5 Secure Data Architecture

### 19.6 Application Security

### 19.7 Monitoring and Detection

### 19.8 Incident Response

### 19.9 Availability and Disaster Recovery

### 19.10 Security vs Cost vs Performance

### 19.11 Security Architecture Trade-Offs

---

# Module 20 — Final System Design Challenge

## Design a Secure Banking Application on AWS

### Requirements

Design a highly available and secure banking application supporting:

* Customer authentication
* Account information
* Transactions
* Document storage
* APIs
* Sensitive customer data
* High availability
* Auditability
* Security monitoring

### Architecture Requirements

The final architecture should address:

* Network security
* Identity and access management
* Application security
* Data security
* Encryption
* Secrets management
* Internet protection
* Logging
* Monitoring
* Threat detection
* Incident response
* High availability
* Disaster recovery
* DevSecOps

### Final Architecture

```text
                         INTERNET
                             |
                             v
                       CLOUDFRONT
                             |
                             v
                            WAF
                             |
                             v
                         ALB + TLS
                             |
              +--------------+--------------+
              |                             |
       PRIVATE APP SUBNET             PRIVATE APP SUBNET
              |                             |
          EC2/ECS/EKS                  EC2/ECS/EKS
              |                             |
              +--------------+--------------+
                             |
                       PRIVATE DATABASE
                             |
                            RDS
```

### Security Services

```text
IAM
 |
Secrets Manager
 |
KMS
 |
CloudTrail
 |
VPC Flow Logs
 |
CloudWatch
 |
GuardDuty
 |
Security Hub
 |
AWS Config
```

### Final Design Discussion

* Why is each component present?
* What problem does each security control solve?
* What happens if a component is compromised?
* How is access controlled?
* How is sensitive data protected?
* How is suspicious activity detected?
* How would an incident be investigated?
* How would the architecture scale?
* How would the architecture survive an Availability Zone failure?
* What security trade-offs were made?

---

# Final Learning Outcomes

After completing this course, learners will be able to:

* Understand AWS cloud security fundamentals
* Design secure AWS network architectures
* Apply IAM and least-privilege principles
* Secure internet-facing applications
* Protect applications using WAF and CloudFront
* Implement encryption and key management
* Secure application secrets
* Secure S3 and databases
* Implement logging and auditing
* Monitor AWS security posture
* Detect suspicious activity
* Investigate security incidents
* Apply security principles to CI/CD
* Understand container and Kubernetes security
* Design enterprise AWS security architectures
* Evaluate security trade-offs in system design
* Design and explain a production-ready secure AWS architecture

---

## Course Philosophy

> **Don't memorize AWS security services. Understand the security problem, design the architecture, choose the right control, implement it, and validate that it works.**

**Context → Concept → Architecture → Lab → Production Thinking → System Design**
