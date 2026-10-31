# AWS Security by Design: Table of Contents

Build, break and secure a production application on AWS. Every module follows the same flow: Context, Concept, Lab, then interview questions.

---

## Module 1: Threat Model First, Then Secure the Account

- 1.1 Context: NovaBank wants to go live. What can go wrong?
- 1.2 Concept: Assets, threats and the CIA triad
- 1.3 Concept: Authentication vs authorization vs audit
- 1.4 Concept: Shared responsibility model and blast radius
- 1.5 Lab: Deploy the insecure NovaBank (public EC2, public database, keys in code, SSH open)
- 1.6 Lab: Find every security problem in the insecure version
- 1.7 Lab: Secure the account (root MFA, IAM Identity Center, budget alert, CloudTrail)
- 1.8 Interview questions

## Module 2: The VPC as a Security Boundary

- 2.1 Context: The database is reachable from the internet
- 2.2 Concept: CIDR planning and subnet design
- 2.3 Concept: Public, private and isolated subnets, route tables, Internet Gateway and NAT Gateway
- 2.4 Concept: Security Groups vs NACLs, and Security Group referencing
- 2.5 Lab: Build a three tier VPC across two Availability Zones
- 2.6 Lab: Place the ALB, API and database in the right tiers
- 2.7 Lab: Chain Security Groups and add a NACL rule
- 2.8 Interview questions

## Module 3: Private Access and Controlled Egress

- 3.1 Context: A compromised server can send our data anywhere
- 3.2 Concept: Gateway vs interface VPC endpoints, and endpoint policies
- 3.3 Concept: SSM Session Manager instead of bastion hosts
- 3.4 Concept: Route 53 Resolver DNS Firewall and data exfiltration
- 3.5 Lab: Add VPC endpoints for S3 and Secrets Manager
- 3.6 Lab: Remove SSH and connect with Session Manager
- 3.7 Lab: Allow only the credit score API domain
- 3.8 Lab: Attempt data exfiltration and watch it fail
- 3.9 Interview questions

## Module 4: The Front Door: CloudFront, WAF and TLS

- 4.1 Context: Login floods, SQL injection and direct hits on the load balancer
- 4.2 Concept: CloudFront as the only entry point
- 4.3 Concept: AWS WAF managed rules, rate limiting and Shield
- 4.4 Concept: ACM, TLS and locking the origin to CloudFront
- 4.5 Lab: Put the frontend and API behind CloudFront
- 4.6 Lab: Attach WAF and send attack traffic
- 4.7 Lab: Prove the ALB can no longer be reached directly
- 4.8 Interview questions

## Module 5: IAM: Who Can Act?

- 5.1 Context: The API runs as administrator and developers hold long lived keys
- 5.2 Concept: IAM roles for workloads and temporary credentials
- 5.3 Concept: Policy evaluation logic, and identity vs resource policies
- 5.4 Concept: Permission boundaries and PassRole privilege escalation
- 5.5 Concept: Cross account roles and the confused deputy problem
- 5.6 Concept: AWS Organizations and SCPs
- 5.7 Lab: Replace access keys with a role
- 5.8 Lab: Generate a least privilege policy with IAM Access Analyzer
- 5.9 Lab: Show a privilege escalation and close it with a permission boundary
- 5.10 Lab: Apply an SCP that blocks disabling CloudTrail
- 5.11 Interview questions

## Module 6: Data Protection: What Is Exposed If They Get In?

- 6.1 Context: An auditor asks who can read stolen backups
- 6.2 Concept: Envelope encryption and KMS key policies
- 6.3 Concept: AWS managed vs customer managed keys
- 6.4 Concept: Secrets Manager and rotation
- 6.5 Concept: S3 Block Public Access, presigned URLs and Object Lock
- 6.6 Lab: Encrypt RDS and S3 with customer managed keys
- 6.7 Lab: Move the database password to Secrets Manager with rotation
- 6.8 Lab: Upload documents only through presigned URLs
- 6.9 Lab: Share a snapshot and prove it is useless without the key
- 6.10 Interview questions

## Module 7: Detection and Incident Response: How Would You Know?

- 7.1 Context: It is 2 AM and the API role is in use from an unknown country
- 7.2 Concept: Which log answers which question (CloudTrail, VPC Flow Logs, DNS logs, WAF logs)
- 7.3 Concept: GuardDuty, Security Hub and AWS Config
- 7.4 Concept: EventBridge auto remediation
- 7.5 Concept: The incident response lifecycle
- 7.6 Lab: Enable centralised logging and threat detection
- 7.7 Lab: Auto remediate an open SSH rule
- 7.8 Lab: Game day, investigate a planted incident using only the logs
- 7.9 Lab: Write the incident report
- 7.10 Interview questions

## Module 8: Enterprise Security Design

- 8.1 Context: Many VPCs, many accounts, partner banks and an on premises data centre
- 8.2 Concept: VPC peering vs Transit Gateway vs PrivateLink
- 8.3 Concept: Centralised inspection with AWS Network Firewall
- 8.4 Concept: Multi account strategy and the data perimeter
- 8.5 Concept: Site to Site VPN vs Direct Connect
- 8.6 Concept: How containers and EKS change the security picture
- 8.7 Lab: Expose the NovaBank API to a partner VPC with PrivateLink
- 8.8 Whiteboard: The full enterprise security design
- 8.9 Interview questions

## Module 9: Capstone: System Design Mock Interview

- 9.1 A new design problem, run like a real interview
- 9.2 Design the architecture and walk it through the five questions
- 9.3 Why not X? follow up questions
- 9.4 A live incident twist on your own design
- 9.5 Feedback against the six point rubric

---

## Lab Setup Notes

- One AWS account runs almost every lab
- Modules 5.5 and 5.10 need a second member account under AWS Organizations, because SCPs never apply to the management account
- Module 4 uses a subdomain such as novabank.yourdomain.com delegated to Route 53. The ACM certificate for CloudFront must be created in us-east-1
- Destroy hourly billed resources (NAT Gateway, interface endpoints, ALB, RDS) after every lab

---

## Connect with me

Rahul Chaubey

- YouTube: https://www.youtube.com/@BuildAutomateArchitect
- System Design playlist: https://www.youtube.com/playlist?list=PLeAHTLMwFYQ0
- Telegram: https://t.me/BuildAutomateArchitect
- Website: https://rahulchaubeyconsulting.com/
