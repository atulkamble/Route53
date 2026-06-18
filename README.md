# AWS Route 53 - Complete Master Guide (Interview + Hands-On + Projects)

## 1. What is Route 53?

Route 53 is a highly available, scalable, and fully managed **DNS (Domain Name System)** service provided by Amazon Web Services.

### Main Functions

| Function            | Description                          |
| ------------------- | ------------------------------------ |
| Domain Registration | Purchase and manage domains          |
| DNS Management      | Resolve domain names to IP addresses |
| Traffic Routing     | Route users to applications          |
| Health Monitoring   | Monitor application availability     |
| Failover            | Redirect traffic during failures     |

---

## Why is it Called Route 53?

DNS uses:

| Protocol | Port |
| -------- | ---- |
| TCP      | 53   |
| UDP      | 53   |

Hence AWS named the service Route 53.

---

# 2. What is DNS?

DNS converts human-readable names into machine-readable IP addresses.

### Example

```text
www.cloudnautic.in
        ↓
DNS Query
        ↓
54.210.120.15
```

Without DNS:

```text
http://54.210.120.15
```

With DNS:

```text
www.cloudnautic.in
```

---

# DNS Resolution Process

```text
User Browser
      │
      ▼
Recursive Resolver
      │
      ▼
Root DNS Server
      │
      ▼
TLD Server (.com)
      │
      ▼
Authoritative DNS
(Route 53)
      │
      ▼
IP Address Returned
```

---

# 3. Route 53 Architecture

```text
                    Internet Users
                           │
                           ▼
                     Route 53
                           │
       ┌───────────────────┼───────────────────┐
       │                   │                   │
       ▼                   ▼                   ▼
      EC2                 ALB                 S3
```

---

# 4. Hosted Zones

## Definition

A Hosted Zone is a container for DNS records.

### Types

| Type                | Purpose                 |
| ------------------- | ----------------------- |
| Public Hosted Zone  | Internet-facing domains |
| Private Hosted Zone | Internal VPC domains    |

---

## Public Hosted Zone Example

```text
cloudnautic.in
```

Accessible from anywhere.

---

## Private Hosted Zone Example

```text
internal.cloudnautic.local
```

Accessible only within VPC.

---

# 5. DNS Records

## A Record

Maps hostname to IPv4.

```text
www.cloudnautic.in
↓
54.221.15.10
```

---

## AAAA Record

Maps hostname to IPv6.

```text
www.cloudnautic.in
↓
2001:db8::123
```

---

## CNAME Record

Maps one hostname to another.

```text
blog.cloudnautic.in
↓
www.cloudnautic.in
```

---

## MX Record

Mail server record.

```text
cloudnautic.in
↓
Google Workspace
```

---

## TXT Record

Used for:

* SPF
* DKIM
* DMARC
* Domain Validation

Example:

```text
v=spf1 include:_spf.google.com ~all
```

---

## NS Record

Name Servers responsible for domain.

```text
ns-123.awsdns.com
ns-456.awsdns.net
```

---

## Alias Record

AWS-specific DNS record.

Can point directly to:

* ALB
* NLB
* CloudFront
* S3 Static Website
* API Gateway

---

# A vs CNAME vs Alias

| Feature           | A Record | CNAME    | Alias        |
| ----------------- | -------- | -------- | ------------ |
| Points To         | IP       | Hostname | AWS Resource |
| Root Domain       | Yes      | No       | Yes          |
| AWS Optimized     | No       | No       | Yes          |
| Automatic Updates | No       | No       | Yes          |

---

# 6. Routing Policies

Routing Policy decides where traffic should go.

---

## Simple Routing

Single destination.

```text
www.cloudnautic.in
        │
        ▼
       EC2
```

### Use Cases

* Small website
* Single application

---

## Weighted Routing

Traffic split by percentage.

```text
70% → Server A
30% → Server B
```

### Use Cases

* Blue-Green Deployment
* Canary Release
* A/B Testing

### Example

| Server | Weight |
| ------ | ------ |
| EC2-A  | 70     |
| EC2-B  | 30     |

---

## Latency Routing

Routes users to lowest latency region.

```text
India User
     ↓
Mumbai

US User
     ↓
Virginia
```

### Use Cases

* Global Applications
* Streaming Services

---

## Failover Routing

Primary and Secondary resource.

```text
Primary Server
      ↓
Health Check
      ↓
Failure
      ↓
Backup Server
```

### Use Cases

* Disaster Recovery
* Business Continuity

---

## Geolocation Routing

Based on user location.

```text
India → Indian Site

USA → US Site
```

### Use Cases

* Country-specific content
* Legal Compliance

---

## Geoproximity Routing

Routes based on physical distance.

### Example

```text
User closer to Mumbai
       ↓
Mumbai Region
```

---

## Multi-Value Routing

Returns multiple healthy IPs.

```text
Server1
Server2
Server3
```

### Benefits

* Basic Load Balancing
* Improved Availability

---

# Routing Policy Comparison

| Policy      | HA     | DR  | Global Apps | Cost   |
| ----------- | ------ | --- | ----------- | ------ |
| Simple      | No     | No  | No          | Low    |
| Weighted    | Medium | No  | No          | Low    |
| Latency     | Yes    | No  | Yes         | Medium |
| Failover    | Yes    | Yes | No          | Medium |
| Geolocation | Yes    | No  | Yes         | Medium |
| Multi Value | Yes    | No  | Yes         | Medium |

---

# 7. Health Checks

Health checks monitor resources.

Supported:

* HTTP
* HTTPS
* TCP

---

## Health Check Flow

```text
Route53
     │
     ▼
Health Check
     │
 ┌───┴────┐
 │        │
 ▼        ▼
Healthy  Unhealthy
 │        │
 ▼        ▼
Primary  Backup
```

---

## Common Endpoints

```text
/health

/status

/heartbeat
```

Example:

```text
https://www.cloudnautic.in/health
```

---

# 8. Route 53 Failover Architecture

```text
Users
   │
   ▼
Route53
   │
   ▼
Primary EC2
   │
 Health Check
   │
Failure
   │
   ▼
Backup EC2
```

---

# 9. Route 53 + ALB

Architecture:

```text
Users
   │
   ▼
Route53
   │
 Alias Record
   │
   ▼
ALB
   │
 ┌─┴─┐
 ▼   ▼
EC2 EC2
```

Benefits:

* High Availability
* Load Balancing
* Auto Recovery

---

# 10. Route 53 + Auto Scaling

```text
Users
   │
Route53
   │
ALB
   │
ASG
   │
EC2 Instances
```

Benefits:

* Auto Scale
* Cost Optimization
* Fault Tolerance

---

# 11. Route 53 + CloudFront

```text
Users
   │
Route53
   │
CloudFront
   │
Origin
```

Benefits:

* Global Performance
* Lower Latency
* CDN Acceleration

---

# 12. Route 53 + S3 Static Website

```text
Users
   │
Route53
   │
S3 Website
```

Perfect for:

* Portfolio Website
* Company Website
* Landing Page

---

# 13. AWS CLI Commands

## List Hosted Zones

```bash
aws route53 list-hosted-zones
```

---

## Create Hosted Zone

```bash
aws route53 create-hosted-zone \
--name cloudnautic.in \
--caller-reference 001
```

---

## Get Hosted Zone

```bash
aws route53 get-hosted-zone \
--id Z123456789
```

---

## List Record Sets

```bash
aws route53 list-resource-record-sets \
--hosted-zone-id Z123456789
```

---

## Create Health Check

```bash
aws route53 create-health-check \
--caller-reference HC001 \
--health-check-config \
IPAddress=8.8.8.8,Port=80,Type=HTTP
```

---

## List Health Checks

```bash
aws route53 list-health-checks
```

---

## Delete Health Check

```bash
aws route53 delete-health-check \
--health-check-id abc123
```

---

# 14. Hands-On Practice Projects

## Project 1: Domain to EC2

### Architecture

```text
Domain
  │
Route53
  │
EC2
```

Skills:

* Hosted Zone
* A Record
* Apache Installation
* DNS Mapping

---

## Project 2: Highly Available Website

### Architecture

```text
Users
   │
Route53
   │
ALB
   │
ASG
   │
EC2
```

Skills:

* DNS
* Load Balancer
* Auto Scaling
* High Availability

---

## Project 3: Multi-Region Disaster Recovery

### Architecture

```text
Users
   │
Route53 Failover
   │
 ┌─┴───────────┐
 ▼             ▼
Mumbai      Virginia
Primary      Backup
```

Skills:

* Failover Routing
* Health Checks
* Disaster Recovery

---

## Project 4: Blue-Green Deployment

### Architecture

```text
Users
    │
Route53
    │
Weighted Routing
    │
 ┌──┴──┐
 ▼     ▼
Blue  Green
70%   30%
```

Skills:

* Deployment Strategies
* Zero Downtime Deployment

---

# 15. Real-World Companies Using Similar Concepts

| Company | Route 53 Usage            |
| ------- | ------------------------- |
| Netflix | Global Traffic Routing    |
| Amazon  | Multi-Region Applications |
| Airbnb  | Global DNS                |
| Spotify | Latency-Based Routing     |

---

# Interview Questions

### What is Route 53?

Managed DNS service from AWS.

### Why Route 53?

DNS uses Port 53.

### Difference Between Public and Private Hosted Zone?

| Public              | Private               |
| ------------------- | --------------------- |
| Internet Accessible | VPC Only              |
| Public Websites     | Internal Applications |

### What Routing Policy is Best for DR?

Failover Routing.

### What Routing Policy is Best for Global Applications?

Latency Routing.

### What Routing Policy is Used in Blue-Green Deployments?

Weighted Routing.

### Difference Between Alias and CNAME?

Alias supports AWS resources and root domains.

---

# 16. Points to Remember (Exam & Interview)

### Must Know

✅ Route 53 is a Global Service

✅ DNS uses Port 53

✅ Hosted Zone contains DNS records

✅ Alias Record is AWS-specific

✅ Health Checks support failover

✅ Public Hosted Zone = Internet

✅ Private Hosted Zone = VPC

✅ Failover Routing = Disaster Recovery

✅ Weighted Routing = Blue-Green Deployment

✅ Latency Routing = Global Applications

✅ Multi Value Routing = DNS Load Balancing

✅ Route 53 integrates with:

* EC2
* ALB
* Auto Scaling
* CloudFront
* S3
* API Gateway

### Frequently Asked in Interviews

⭐ Hosted Zones

⭐ A vs CNAME vs Alias

⭐ Routing Policies

⭐ Health Checks

⭐ Failover Architecture

⭐ Route 53 + ALB

⭐ Route 53 + CloudFront

⭐ Route 53 + Auto Scaling

⭐ Disaster Recovery Design

⭐ Blue-Green Deployment using Weighted Routing

These topics cover roughly **80–90% of Route 53 interview questions and practical AWS project scenarios**.
