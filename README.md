# AWS Route 53 - Complete Notes, Commands, Use Cases & Project

## What is Route 53?

Amazon Web Services Route 53 is AWS's highly available and scalable **DNS (Domain Name System)** web service.

### Why Route 53?

* Register domains
* Manage DNS records
* Route internet traffic
* Health checking
* Load balancing
* Failover configuration
* Hybrid cloud DNS

---

# Route 53 Full Form

**Route 53**

* Route = Traffic Routing
* 53 = DNS uses Port 53 (TCP/UDP)

---

# Real-World Flow

```text
User → www.company.com
          ↓
      Route 53
          ↓
      ALB / EC2
          ↓
     Web Application
```

When users enter a domain name:

```text
www.netflix.com
www.amazon.com
www.google.com
```

Route 53 translates it into an IP address.

---

# Components of Route 53

## 1. Hosted Zone

A container for DNS records.

Example:

```text
Domain:
cloudnautic.in

Hosted Zone:
cloudnautic.in
```

Contains:

```text
A Record
AAAA Record
CNAME Record
MX Record
TXT Record
NS Record
```

---

## 2. DNS Records

### A Record

Maps domain to IPv4.

```text
www.cloudnautic.in
→ 54.221.100.20
```

---

### AAAA Record

Maps domain to IPv6.

```text
www.cloudnautic.in
→ 2001:db8::1
```

---

### CNAME

Maps one name to another.

```text
blog.cloudnautic.in
→ www.cloudnautic.in
```

---

### MX Record

Mail server record.

```text
cloudnautic.in
→ Google Workspace
→ Microsoft 365
```

---

### TXT Record

Verification records.

Examples:

```text
SPF
DKIM
DMARC
Domain Verification
```

---

### NS Record

Name Servers.

```text
ns-123.awsdns.com
ns-456.awsdns.net
```

---

# Routing Policies

## 1. Simple Routing

Single resource.

```text
www.cloudnautic.in
→ EC2
```

Use Case:

Small website

---

## 2. Weighted Routing

Traffic distribution.

```text
70% → Server A
30% → Server B
```

Use Case:

Blue-Green Deployment

---

## 3. Latency Routing

Direct users to nearest region.

```text
India User
→ Mumbai

USA User
→ Virginia
```

Use Case:

Global Applications

---

## 4. Failover Routing

Primary and backup setup.

```text
Primary → EC2

If Down

Secondary → EC2
```

Use Case:

Disaster Recovery

---

## 5. Geolocation Routing

Based on country.

```text
India → Indian Website

US → US Website
```

---

## 6. Geoproximity Routing

Traffic based on geographic distance.

---

## 7. Multi-Value Routing

Returns multiple healthy IPs.

```text
Server1
Server2
Server3
```

---

# Route 53 Health Checks

Monitor:

* EC2
* ALB
* NLB
* External Website

Example:

```text
http://website.com/health
```

Health check every:

```text
30 sec
10 sec
```

If unhealthy:

```text
Failover Triggered
```

---

# Route 53 Pricing Factors

Charged for:

* Hosted Zones
* Queries
* Health Checks
* Domain Registration

---

# AWS CLI Commands

## List Hosted Zones

```bash
aws route53 list-hosted-zones
```

---

## Create Hosted Zone

```bash
aws route53 create-hosted-zone \
--name cloudnautic.in \
--caller-reference 12345
```

---

## Get Hosted Zone Details

```bash
aws route53 get-hosted-zone \
--id Z123456789
```

---

## List DNS Records

```bash
aws route53 list-resource-record-sets \
--hosted-zone-id Z123456789
```

---

## Delete Hosted Zone

```bash
aws route53 delete-hosted-zone \
--id Z123456789
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
--health-check-id xxxxx
```

---

# Hands-On Practice

## Lab 1: Domain → EC2

### Create

* EC2 Instance
* Install Apache

```bash
sudo yum install httpd -y
sudo systemctl start httpd
sudo systemctl enable httpd
```

Create page:

```bash
echo "Cloudnautic Route53 Demo" \
| sudo tee /var/www/html/index.html
```

Get:

```bash
EC2 Public IP
```

Create:

```text
A Record
```

Browse:

```text
www.domain.com
```

---

## Lab 2: Route 53 + ALB

Architecture:

```text
Internet
    ↓
Route53
    ↓
ALB
 ↓     ↓
EC2   EC2
```

Practice:

* Create 2 EC2
* Install Apache
* Create ALB
* Register targets
* Create Route53 Alias Record

Access:

```text
www.cloudnautic.in
```

---

## Lab 3: Route 53 Failover

Architecture:

```text
Primary EC2
      ↓
 Health Check
      ↓
Route53
      ↓
Backup EC2
```

Steps:

1. Launch 2 EC2
2. Create health check
3. Configure failover routing
4. Stop primary instance
5. Verify traffic shifts to backup

---

# Mini Project

## Highly Available Website Using Route 53

### Architecture

```text
Users
  ↓
Route53
  ↓
ALB
  ↓
Auto Scaling Group
  ↓
EC2 Instances
```

### Services Used

* Amazon Web Services Route 53
* Amazon Web Services EC2
* Amazon Web Services Auto Scaling
* Amazon Web Services Application Load Balancer
* Amazon Web Services CloudWatch

### Benefits

* High Availability
* Automatic Scaling
* Fault Tolerance
* Global Access
* Disaster Recovery Ready

---

# Important Interview Questions

### Why is Route 53 called Route 53?

DNS operates on Port 53 (TCP/UDP).

### Difference Between Route 53 and ALB?

| Route 53          | ALB                     |
| ----------------- | ----------------------- |
| DNS Service       | Load Balancer           |
| Global Service    | Regional Service        |
| Routes Requests   | Distributes Traffic     |
| Supports Failover | Supports Load Balancing |

### What is Alias Record?

AWS-specific record that points directly to:

* ALB
* CloudFront
* S3 Static Website
* API Gateway

No IP address required.

### What is TTL?

Time DNS records remain cached.

Example:

```text
TTL = 300 seconds
```

### What Routing Policy is Used for Disaster Recovery?

**Failover Routing Policy**

---

# Points to Remember

✅ Route 53 is a DNS service

✅ DNS uses Port 53

✅ Hosted Zone stores DNS records

✅ Alias Record is AWS-specific

✅ Health Checks enable failover

✅ Route 53 is Global

✅ Supports Domain Registration

✅ Common routing policies:

* Simple
* Weighted
* Latency
* Failover
* Geolocation
* Geoproximity
* Multi-Value

For AWS interviews and hands-on practice, focus heavily on **Hosted Zones, A/CNAME/Alias Records, Health Checks, Failover Routing, Route 53 + ALB, and Route 53 + Auto Scaling** as these are the most frequently used real-world scenarios.
