# AWS Route 53 – Complete Master Guide (Interview + Hands-On + Projects)

## Table of Contents

1. Introduction to Route 53
2. DNS Fundamentals
3. DNS Resolution Process
4. Route 53 Architecture
5. Hosted Zones
6. DNS Record Types
7. Alias Records
8. Routing Policies
9. Health Checks
10. Route 53 Integrations
11. Route 53 Architectures & Diagrams
12. AWS CLI Commands
13. Hands-On Labs
14. Real-World Projects
15. Monitoring & Security
16. Pricing
17. Troubleshooting
18. Interview Questions
19. Comparison Tables
20. Learning Roadmap
21. Quick Revision Sheet

---

# 1. Introduction to Route 53

AWS Route 53 is a highly available, scalable, and fully managed Domain Name System (DNS) service provided by AWS.

Route 53 helps users connect to applications running in AWS and outside AWS using domain names instead of IP addresses.

Main Functions:

* Domain Registration
* DNS Resolution
* Traffic Routing
* Health Monitoring
* Disaster Recovery
* Global Traffic Management

Route 53 is a Global Service.

---

## Why is it Called Route 53?

DNS operates on Port 53.

| Protocol | Port |
| -------- | ---- |
| TCP      | 53   |
| UDP      | 53   |

AWS named the service Route 53 because it routes traffic using DNS on Port 53.

---

# 2. DNS Fundamentals

DNS (Domain Name System) converts human-readable names into machine-readable IP addresses.

Example:

[www.cloudnautic.in](http://www.cloudnautic.in)

↓

54.210.120.15

Without DNS:

http://54.210.120.15

With DNS:

[www.cloudnautic.in](http://www.cloudnautic.in)

Benefits:

* Easy to remember
* User friendly
* Supports internet scalability

---

# 3. DNS Resolution Process

User Browser
↓
Recursive Resolver
↓
Root DNS Server
↓
TLD Server (.com/.in)
↓
Authoritative DNS Server
(Route 53)
↓
IP Address Returned
↓
Website Access

Important Terms:

Root Server

Top level DNS servers.

TLD Server

Handles .com, .org, .in etc.

Authoritative Server

Contains actual DNS records.

---

# 4. Route 53 Architecture

Internet Users
│
▼
Route 53
│
├── EC2
├── ALB
├── CloudFront
├── S3
├── API Gateway
└── External Applications

---

# 5. Hosted Zones

Hosted Zone is a container that stores DNS records.

Types:

## Public Hosted Zone

Accessible from Internet.

Examples:

[www.cloudnautic.in](http://www.cloudnautic.in)

Use Cases:

* Websites
* Web Applications
* APIs

---

## Private Hosted Zone

Accessible only inside VPC.

Examples:

db.internal.local

api.internal.local

Use Cases:

* Internal Applications
* Databases
* Microservices

---

# Public vs Private Hosted Zone

| Feature              | Public | Private |
| -------------------- | ------ | ------- |
| Internet Accessible  | Yes    | No      |
| VPC Required         | No     | Yes     |
| Public Website       | Yes    | No      |
| Internal Application | No     | Yes     |

---

# 6. DNS Record Types

## A Record

Maps hostname to IPv4.

Example:

[www.cloudnautic.in](http://www.cloudnautic.in)

↓

54.210.10.20

---

## AAAA Record

Maps hostname to IPv6.

Example:

[www.cloudnautic.in](http://www.cloudnautic.in)

↓

2001:db8::1

---

## CNAME Record

Maps hostname to another hostname.

Example:

blog.cloudnautic.in

↓

[www.cloudnautic.in](http://www.cloudnautic.in)

---

## MX Record

Mail Server Record.

Example:

Google Workspace

Microsoft 365

---

## TXT Record

Used for:

* SPF
* DKIM
* DMARC
* Domain Verification

Example:

v=spf1 include:_spf.google.com ~all

---

## NS Record

Name Server Record.

Example:

ns-123.awsdns.com

ns-456.awsdns.net

---

# 7. Alias Records

Alias Record is AWS-specific.

Can point directly to:

* ALB
* NLB
* CloudFront
* S3 Website
* API Gateway
* Global Accelerator

Benefits:

* Supports Root Domain
* AWS Optimized
* Automatically Updates

---

# A Record vs CNAME vs Alias

| Feature      | A   | CNAME    | Alias        |
| ------------ | --- | -------- | ------------ |
| Points To    | IP  | Hostname | AWS Resource |
| Root Domain  | Yes | No       | Yes          |
| Auto Updates | No  | No       | Yes          |
| AWS Native   | No  | No       | Yes          |

---

# 8. Routing Policies

Routing Policy determines where Route 53 sends traffic.

---

## Simple Routing

Single Resource

User
↓
EC2

Use Cases:

* Personal Website
* Single Application

---

## Weighted Routing

Traffic Distribution

70% → Blue

30% → Green

Use Cases:

* Blue Green Deployment
* Canary Release
* A/B Testing

---

## Latency Routing

Routes traffic to nearest AWS Region.

India User
↓
Mumbai

US User
↓
Virginia

Use Cases:

* Streaming Services
* Global Applications

---

## Failover Routing

Primary Resource
↓
Health Check
↓
Backup Resource

Use Cases:

* Disaster Recovery
* Business Continuity

---

## Geolocation Routing

Routes traffic based on country.

India
↓
India Website

USA
↓
USA Website

Use Cases:

* Regional Content
* Compliance

---

## Geoproximity Routing

Routes users based on physical distance.

Closest AWS Region receives traffic.

---

## Multi Value Routing

Returns multiple healthy IPs.

Server 1

Server 2

Server 3

Benefits:

* Basic Load Balancing
* High Availability

---

# Routing Policy Comparison

| Policy      | HA     | DR  | Global |
| ----------- | ------ | --- | ------ |
| Simple      | No     | No  | No     |
| Weighted    | Medium | No  | No     |
| Latency     | Yes    | No  | Yes    |
| Failover    | Yes    | Yes | No     |
| Geolocation | Yes    | No  | Yes    |
| Multi Value | Yes    | No  | Yes    |

---

# 9. Health Checks

Monitor:

* HTTP
* HTTPS
* TCP

Common URLs:

/health

/status

/heartbeat

Example:

https://app.cloudnautic.in/health

---

Health Check Flow

Route53
↓
Health Check
↓
Healthy
↓
Primary

OR

Unhealthy
↓
Backup

---

# 10. Route 53 Integrations

| AWS Service        | Integration |
| ------------------ | ----------- |
| EC2                | A Record    |
| ALB                | Alias       |
| NLB                | Alias       |
| CloudFront         | Alias       |
| S3 Website         | Alias       |
| API Gateway        | Alias       |
| Global Accelerator | Alias       |
| Elastic Beanstalk  | CNAME       |

---

# 11. Route 53 Architectures

## Route 53 + EC2

Users
↓
Route53
↓
EC2

---

## Route 53 + ALB

Users
↓
Route53
↓
ALB
↓
EC2

---

## Route 53 + Auto Scaling

Users
↓
Route53
↓
ALB
↓
ASG
↓
EC2

---

## Route 53 + CloudFront

Users
↓
Route53
↓
CloudFront
↓
Origin

---

## Route 53 + S3

Users
↓
Route53
↓
S3 Static Website

---

## Multi-Region Disaster Recovery

Users
↓
Route53 Failover
↓
Mumbai Region
↓
Primary

Failure

↓

Virginia Region
↓
Backup

---

# 12. AWS CLI Commands

List Hosted Zones

aws route53 list-hosted-zones

Create Hosted Zone

aws route53 create-hosted-zone 
--name cloudnautic.in 
--caller-reference 001

Get Hosted Zone

aws route53 get-hosted-zone 
--id Z123456789

List Records

aws route53 list-resource-record-sets 
--hosted-zone-id Z123456789

Create Health Check

aws route53 create-health-check 
--caller-reference HC001 
--health-check-config 
IPAddress=8.8.8.8,Port=80,Type=HTTP

List Health Checks

aws route53 list-health-checks

Delete Health Check

aws route53 delete-health-check 
--health-check-id abc123

---

# 13. Hands-On Labs

Lab 1

Route53 + EC2

Skills:

* Hosted Zones
* A Records
* Apache Installation

---

Lab 2

Route53 + ALB

Skills:

* Alias Records
* Load Balancing

---

Lab 3

Route53 Failover

Skills:

* Health Checks
* Disaster Recovery

---

Lab 4

Blue Green Deployment

Skills:

* Weighted Routing
* Zero Downtime Deployment

---

# 14. Real-World Projects

Project 1

Personal Website

Route53 + EC2

---

Project 2

Corporate Website

Route53 + ALB + ASG

---

Project 3

Static Website Hosting

Route53 + S3

---

Project 4

Global Application

Route53 + CloudFront

---

Project 5

Disaster Recovery Platform

Route53 Failover
Mumbai
Virginia

---

# 15. Monitoring & Security

Monitoring Tools:

* CloudWatch
* CloudTrail
* Route53 Health Checks
* SNS

Security Best Practices:

* Enable MFA
* IAM Least Privilege
* CloudTrail Logging
* DNSSEC
* Restrict Route53 Access

---

# 16. Pricing Components

Charges apply for:

* Hosted Zones
* DNS Queries
* Health Checks
* Domain Registration

---

# 17. Troubleshooting Checklist

Website Not Opening?

Check:

□ Domain Registration

□ Hosted Zone

□ Name Servers

□ DNS Records

□ EC2 Status

□ ALB Status

□ Security Group

□ Health Checks

□ TTL

□ DNS Propagation

---

# 18. Interview Questions

What is Route 53?

Managed DNS service from AWS.

Why Route 53?

DNS uses Port 53.

What is Hosted Zone?

Container for DNS records.

What is Alias Record?

AWS-specific DNS record.

Best Routing Policy for DR?

Failover Routing.

Best Routing Policy for Blue-Green?

Weighted Routing.

Best Routing Policy for Global Applications?

Latency Routing.

---

# 19. Route53 Comparison Tables

Route53 vs ALB

| Feature        | Route53   | ALB               |
| -------------- | --------- | ----------------- |
| DNS            | Yes       | No                |
| Global         | Yes       | No                |
| Load Balancing | DNS Level | Application Level |

Route53 vs CloudFront

| Feature | Route53 | CloudFront |
| ------- | ------- | ---------- |
| DNS     | Yes     | No         |
| CDN     | No      | Yes        |
| Caching | No      | Yes        |

---

# 20. Learning Roadmap

Week 1

DNS
Hosted Zones
Records

Week 2

Alias
Routing Policies
Health Checks

Week 3

Route53 + EC2
Route53 + ALB
Route53 + S3

Week 4

Failover
CloudFront
Multi-Region Architecture

---

# 21. Quick Revision Sheet

Route53 = AWS DNS Service

Port = 53

Hosted Zone = DNS Database

Public Hosted Zone = Internet

Private Hosted Zone = VPC

Alias = AWS Resources

Weighted = Blue Green

Latency = Global Apps

Failover = Disaster Recovery

Multi Value = DNS Load Balancing

Most Important Topics:

✓ Hosted Zones

✓ Alias Records

✓ Routing Policies

✓ Health Checks

✓ Failover

✓ Route53 + ALB

✓ Route53 + CloudFront

✓ Route53 + S3

✓ Disaster Recovery

END OF DOCUMENT
