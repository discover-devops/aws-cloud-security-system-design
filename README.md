# AWS Cloud Security Architect

## Design, Build & Defend Production-Grade AWS Architectures

### Program Overview

This program teaches AWS cloud security from a practical system-design
perspective. Instead of studying AWS services in isolation, we will use
a single evolving application scenario to understand security
requirements, design controls, implement them, test failures, and make
production-level decisions.

The focus is on **cloud security, network security, identity and access,
data protection, threat detection, incident response, and secure
production architecture**.

### Learning Approach

Each topic is approached through:

1.  **Context** -- Understand the business or security problem.
2.  **Concept** -- Learn the security principle and relevant AWS
    capabilities.
3.  **Architecture** -- Understand where the control fits and why.
4.  **Hands-on Lab** -- Implement selected controls in AWS.
5.  **Validate and Troubleshoot** -- Test access, introduce controlled
    failures, and fix issues.
6.  **Production Thinking** -- Discuss design choices, trade-offs, and
    operational considerations.
7.  **Interview Preparation** -- Practise explaining and defending
    architecture decisions.

### Central Application Scenario

Throughout the program, we will use an illustrative online banking
application. It includes:

-   A customer-facing web application and APIs.
-   Application services running on AWS.
-   A database containing customer and transaction information.
-   Customer authentication and authorization.
-   Sensitive data, application secrets, and financial transactions.
-   Logging, monitoring, threat detection, and incident response
    requirements.

The architecture will evolve as we introduce security requirements and
AWS controls. The scenario is for learning and does not represent an
actual banking deployment.

------------------------------------------------------------------------

# Course Curriculum

## Phase 1 -- Understanding the Security Problem

### Module 0 -- The Production Application and Its Security Risks

-   Understand the application, its components, and the information it
    handles.
-   Identify assets, threats, vulnerabilities, risks, attack surfaces,
    and trust boundaries.
-   Understand the CIA Triad: Confidentiality, Integrity, and
    Availability.
-   Distinguish preventive, detective, and corrective security controls.
-   Introduction to defense in depth and blast radius.
-   Create an initial threat model for the application.

### Module 1 -- AWS Shared Responsibility Model

-   Security **of** the Cloud vs. security **in** the Cloud.
-   AWS responsibilities and customer responsibilities.
-   How responsibilities differ across EC2, S3, RDS, containers, and
    managed services.
-   Common responsibility gaps and real-world misconfiguration examples.
-   Build a responsibility matrix for the application.

## Phase 2 -- Building and Securing the AWS Network

### Module 2 -- VPC and Network Architecture

-   AWS Regions, Availability Zones, VPCs, and CIDR ranges.
-   Public, private, and database subnets.
-   Route tables, Internet Gateways, and NAT Gateways.
-   Public and private IP addressing.
-   Network traffic flow and multi-AZ design.
-   Build the application's initial VPC network.

### Module 3 -- Network Security Controls

-   Security Groups and Network ACLs.
-   Stateful vs. stateless filtering.
-   Inbound and outbound traffic rules.
-   Least-privilege network access.
-   Network segmentation and isolation.
-   North-south and east-west traffic.
-   Validate permitted and denied network paths.

### Module 4 -- Deploying the Application on AWS

-   EC2 and Application Load Balancer.
-   Public-facing and private application tiers.
-   Load balancer listeners and health checks.
-   Application-to-database connectivity.
-   Availability across multiple Availability Zones.
-   Identify and remediate insecure deployment choices.

## Phase 3 -- Identity and Access Security

### Module 5 -- IAM and Least Privilege

-   Authentication vs. authorization.
-   IAM users, groups, roles, and policies.
-   Identity-based and resource-based policies.
-   Trust policies and role assumption.
-   EC2 instance profiles and workload identities.
-   Policy evaluation, explicit deny, and least privilege.
-   MFA, root-user protection, temporary credentials, and cross-account
    access.
-   Test permissions and troubleshoot AccessDenied errors.

### Module 6 -- Secrets and Sensitive Configuration

-   Risks of hardcoded credentials and secrets.
-   AWS Secrets Manager and Systems Manager Parameter Store.
-   Secure application configuration.
-   IAM-based access to secrets.
-   Secret rotation and lifecycle management.
-   Retrieve secrets securely from an application.

## Phase 4 -- Encryption and Application Protection

### Module 7 -- Encryption, KMS, and TLS

-   Encryption at rest and in transit.
-   Symmetric and asymmetric encryption.
-   AWS Key Management Service (KMS).
-   AWS-managed and customer-managed keys.
-   Envelope encryption, key policies, and rotation.
-   TLS, HTTPS, certificates, and certificate authorities.
-   AWS Certificate Manager (ACM).
-   Configure encryption and HTTPS for application components.

### Module 8 -- Internet-Facing Application Security

-   CloudFront, Application Load Balancer, AWS WAF, and AWS Shield.
-   Web application attack patterns: SQL injection and cross-site
    scripting.
-   WAF rules, managed rule groups, IP filtering, and rate limiting.
-   DDoS protection and the difference between network-layer and
    application-layer attacks.
-   Protect and validate the internet-facing application.

## Phase 5 -- Advanced Network Security and IDS/IPS

### Module 9 -- Advanced AWS Network Security

-   VPC Gateway Endpoints and Interface Endpoints.
-   AWS PrivateLink and VPC Peering.
-   AWS Transit Gateway concepts.
-   Private access to AWS services.
-   Egress traffic control and network isolation.
-   Systems Manager Session Manager vs. bastion hosts.
-   Reduce unnecessary public internet dependencies.

### Module 10 -- IDS, IPS, and AWS Network Firewall

-   Intrusion Detection Systems (IDS) and Intrusion Prevention Systems
    (IPS).
-   Network-based and host-based detection concepts.
-   Signature-based and anomaly-based detection.
-   Stateful inspection and deep packet inspection concepts.
-   AWS Network Firewall and firewall policies.
-   Suricata-compatible rule concepts.
-   Gateway Load Balancer and third-party network security appliances.
-   Understand detection, inline prevention, and automated response as
    different capabilities.

## Phase 6 -- Logging, Monitoring, and Threat Detection

### Module 11 -- AWS Logging and Auditing

-   AWS CloudTrail and audit events.
-   VPC Flow Logs and network traffic visibility.
-   Amazon CloudWatch Logs.
-   Management events and data events.
-   Centralized logging, retention, and log protection.
-   Investigate access and network activity.

### Module 12 -- Monitoring, Threat Detection, and Security Posture

-   CloudWatch metrics, logs, and alarms.
-   Amazon GuardDuty for threat detection.
-   AWS Security Hub for security findings and posture visibility.
-   Amazon Inspector for vulnerability management.
-   Amazon Macie for sensitive data discovery.
-   Amazon Detective for investigation.
-   AWS Config for configuration and compliance monitoring.
-   Understand how findings and security services work together.

## Phase 7 -- Data and Database Security

### Module 13 -- Amazon S3 Security

-   S3 Block Public Access.
-   Bucket policies, IAM policies, and access control.
-   Object Ownership and secure permissions.
-   Encryption, versioning, and lifecycle policies.
-   Presigned URLs and controlled sharing.
-   Object Lock, recovery, and data protection.
-   Identify and remediate insecure bucket configurations.

### Module 14 -- Database Security

-   Private database architecture.
-   Database access through Security Groups.
-   Authentication and secrets management.
-   Encryption with KMS.
-   Backups, recovery, and Multi-AZ considerations.
-   Database auditing and monitoring.
-   Restrict database access to authorized application components.

## Phase 8 -- Enterprise AWS Security

### Module 15 -- Enterprise IAM and Multi-Account Security

-   AWS Organizations and organizational units.
-   Multi-account architecture and account separation.
-   Production, non-production, security, and log-archive accounts.
-   IAM Identity Center and federated access.
-   Service Control Policies (SCPs).
-   Cross-account access and separation of duties.
-   Centralized logging and security visibility.
-   Break-glass access and AWS Control Tower concepts.
-   AWS Firewall Manager concepts.

## Phase 9 -- Security Testing and Incident Response

### Module 16 -- Attack the Application: Security Testing and Remediation

-   Vulnerabilities, threats, and attack surfaces.
-   Security testing and validation.
-   Network and web application testing concepts.
-   Vulnerability scanning and configuration assessment.
-   Controlled scenarios involving public databases, open Security
    Groups, excessive IAM permissions, exposed secrets, public S3
    buckets, vulnerable dependencies, and insecure containers.
-   Identify, validate, remediate, and retest security weaknesses.

### Module 17 -- Incident Response and Security Operations

-   Incident response lifecycle: detection, triage, containment,
    investigation, eradication, and recovery.
-   Investigate suspicious activity and security findings.
-   Respond to compromised credentials and affected resources.
-   Evidence collection and forensic fundamentals.
-   Event-driven response using EventBridge, Lambda, and Systems
    Manager.
-   Root-cause analysis and post-incident review.

## Phase 10 -- DevSecOps and Container Security

### Module 18 -- DevSecOps and Security Automation

-   Shift-left security and secure CI/CD.
-   Secret scanning, SAST, DAST, dependency scanning, and IaC scanning.
-   Container image scanning and pipeline security gates.
-   Policy as code and software supply-chain security.
-   Software Bill of Materials (SBOM).
-   Introduction to tools such as Gitleaks, Semgrep, Trivy, Checkov,
    OWASP ZAP, and Prowler.

### Module 19 -- Secure Containerized Applications

-   Docker image security and container hardening.
-   Amazon ECR and image scanning.
-   ECS and EKS security concepts.
-   Workload identity and IAM roles.
-   Secure secrets and container networking.
-   Image signing, supply-chain controls, and runtime security.
-   Deploy and validate a secured containerized application.

## Phase 11 -- Final Production System Design

### Module 20 -- Secure AWS Architecture Capstone

Design and validate a secure, highly available architecture for the
illustrative banking application.

The design will consider:

-   Internet entry through Route 53, CloudFront, WAF, and an Application
    Load Balancer.
-   Private application workloads across Availability Zones.
-   Private and encrypted database services.
-   IAM, workload identity, and secrets management.
-   KMS-based encryption and TLS.
-   Network segmentation, firewalling, and IDS/IPS patterns.
-   Centralized logging, monitoring, and threat detection.
-   Vulnerability management and security posture.
-   Incident response and automated remediation.
-   Secure CI/CD and container supply-chain controls.

**Final deliverable:** A production-oriented AWS security architecture,
implementation notes, validation results, and a consolidated
architecture runbook.

------------------------------------------------------------------------

# Expected Learning Outcomes

By the end of the program, learners should be able to:

-   Analyse security requirements and identify risks in an application
    architecture.
-   Design secure AWS network and VPC architectures.
-   Apply IAM and least-privilege access principles.
-   Protect application secrets and sensitive data.
-   Design encryption and TLS controls.
-   Secure internet-facing applications and network traffic.
-   Understand and apply IDS/IPS and firewall architecture patterns.
-   Configure logging, monitoring, threat detection, and security
    posture controls.
-   Secure S3, databases, and containerized workloads.
-   Assess vulnerabilities, investigate findings, and respond to
    incidents.
-   Explain security architecture decisions, trade-offs, and failure
    scenarios.

# Program Deliverables

-   Module-wise learning notes and architecture explanations.
-   Hands-on lab runbooks and validation steps.
-   Troubleshooting and controlled security-failure exercises.
-   Module-wise interview questions.
-   Final AWS security architecture runbook.
-   Consolidated scenario-based interview question bank.

------------------------------------------------------------------------

**Important:** This is a practical architecture and implementation
program. AWS services and tools are introduced in the context of
security problems. The objective is to develop the ability to design,
implement, validate, troubleshoot, and explain production-oriented AWS
security architectures---not simply memorise service definitions.
