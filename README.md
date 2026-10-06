## AWS Route 53 

### 1. What is Route 53?

Amazon Route 53 is AWS’s managed DNS service.

**Core functions:**
- Domain registration
- DNS management
- Traffic routing
- Health checks
- Failover

**Remember:** Route 53 is a **global service**. DNS primarily uses **UDP/TCP port 53**.

---

## 2. Core DNS Flow

```text
User enters:
www.atulkamble.in
        |
        v
DNS Resolver
        |
        v
Root DNS
        |
        v
.in TLD
        |
        v
Route 53
Authoritative DNS
        |
        v
DNS Record
        |
        +----------> EC2 Public IP
        |
        +----------> ALB
```

### Points to Remember

- DNS converts **domain names → destination information**.
- Route 53 can be the **authoritative DNS service** for a domain.
- Route 53 does **not** host your application.
- Route 53 routes users toward resources such as EC2, ALB, CloudFront, API Gateway, and S3 website endpoints.

---

# 3. Domain Purchase

### Console Steps

```text
AWS Console
   ↓
Route 53
   ↓
Registered domains
   ↓
Register domains
   ↓
Search domain
   ↓
Select domain
   ↓
Enter contact information
   ↓
Complete purchase
```

Example:

```text
atulkamble.in
```

After registration, check:

```text
Route 53
→ Registered domains
→ atulkamble.in
```

### Points to Remember

- Domain registration and hosted-zone DNS hosting are separate concepts.
- Registration is generally renewed annually.
- AWS can automatically create/manage the required name-server configuration when Route 53 is used for DNS.
- If the domain is registered elsewhere, update the registrar's NS records to the Route 53 name servers.

---

# 4. Hosted Zone

A **Hosted Zone** is a container for DNS records for a domain.

```text
Hosted Zone
atulkamble.in
     |
     +-- A
     +-- AAAA
     +-- CNAME
     +-- MX
     +-- TXT
     +-- NS
     +-- SOA
```

### Types

| Hosted Zone | Used For |
|---|---|
| Public | Internet-facing DNS |
| Private | DNS inside associated VPCs |

### CLI

```bash
aws route53 list-hosted-zones
```

Create:

```bash
aws route53 create-hosted-zone \
  --name atulkamble.in \
  --caller-reference "$(date +%s)"
```

List records:

```bash
aws route53 list-resource-record-sets \
  --hosted-zone-id ZONE_ID
```

---

# 5. Important DNS Records

| Record | Purpose | Example |
|---|---|---|
| A | Name → IPv4 | EC2 IPv4 |
| AAAA | Name → IPv6 | IPv6 address |
| CNAME | Name → another DNS name | `blog → app.example.com` |
| Alias | Name → supported AWS resource | ALB |
| MX | Mail routing | Mail server |
| TXT | Verification/security text | SPF/DKIM |
| NS | Authoritative name servers | Route 53 servers |

### Points to Remember

```text
EC2 Public IPv4
      → A Record

ALB
      → Alias Record

Another hostname
      → CNAME
```

**Important:** A standard CNAME cannot normally be used at the zone apex/root domain, such as:

```text
atulkamble.in
```

Route 53 Alias records can support the zone apex for supported AWS targets.

---

# 6. Hands-On Lab 1 — Route 53 → EC2

## Architecture

```text
Laptop
   |
   | https/http request
   v
atulkamble.in
   |
   v
Route 53
   |
   | A Record
   v
EC2 Public IP
   |
   v
Apache Web Server
```

## Step 1 — Create EC2

Create Amazon Linux EC2.

Security Group:

| Type | Port | Source |
|---|---:|---|
| SSH | 22 | My IP |
| HTTP | 80 | 0.0.0.0/0 |

Install Apache:

```bash
sudo dnf install httpd -y

sudo systemctl enable --now httpd
```

Create test page:

```bash
echo "<h1>Route 53 to EC2 Working</h1>" | \
sudo tee /var/www/html/index.html
```

Check:

```bash
curl localhost
```

Test EC2 first:

```text
http://EC2-PUBLIC-IP
```

---

# 7. Create A Record for EC2

Route 53:

```text
Hosted zones
     ↓
atulkamble.in
     ↓
Create record
```

Example:

```text
Record name: www
Record type: A
Value: EC2-PUBLIC-IP
TTL: 300
Routing policy: Simple
```

Result:

```text
www.atulkamble.in
        |
        v
    A Record
        |
        v
EC2 Public IPv4
```

### CLI Record Creation

Create:

```bash
nano ec2-record.json
```

```json
{
  "Comment": "Route www to EC2",
  "Changes": [
    {
      "Action": "UPSERT",
      "ResourceRecordSet": {
        "Name": "www.atulkamble.in",
        "Type": "A",
        "TTL": 300,
        "ResourceRecords": [
          {
            "Value": "EC2_PUBLIC_IP"
          }
        ]
      }
    }
  ]
}
```

Apply:

```bash
aws route53 change-resource-record-sets \
  --hosted-zone-id ZONE_ID \
  --change-batch file://ec2-record.json
```

---

# 8. Verify DNS

Use:

```bash
nslookup www.atulkamble.in
```

Or:

```bash
dig www.atulkamble.in
```

Only IPv4 answer:

```bash
dig A www.atulkamble.in
```

Test website:

```bash
curl http://www.atulkamble.in
```

### Troubleshooting Order

```text
Domain
   ↓
NS
   ↓
Hosted Zone
   ↓
DNS Record
   ↓
EC2 Public IP
   ↓
Security Group
   ↓
Web Server
```

---

# 9. Hands-On Lab 2 — Route 53 → ALB → EC2

This is the more realistic architecture.

```text
                    INTERNET
                        |
                        v
                 atulkamble.in
                        |
                        v
                    Route 53
                        |
                   Alias Record
                        |
                        v
                Application LB
                   /         \
                  /           \
                 v             v
              EC2-1           EC2-2
```

---

# 10. Create Two EC2 Web Servers

EC2-1:

```bash
sudo dnf install httpd -y
sudo systemctl enable --now httpd

echo "<h1>Server 1</h1>" | \
sudo tee /var/www/html/index.html
```

EC2-2:

```bash
sudo dnf install httpd -y
sudo systemctl enable --now httpd

echo "<h1>Server 2</h1>" | \
sudo tee /var/www/html/index.html
```

---

# 11. Create Target Group

Console:

```text
EC2
 ↓
Target Groups
 ↓
Create Target Group
```

Configuration:

```text
Target type: Instances
Protocol: HTTP
Port: 80
Health check: /
```

Register:

```text
EC2-1
EC2-2
```

Wait until:

```text
EC2-1 → Healthy
EC2-2 → Healthy
```

---

# 12. Create ALB

```text
EC2
 ↓
Load Balancers
 ↓
Create
 ↓
Application Load Balancer
```

Configure:

```text
Scheme: Internet-facing
Listener: HTTP : 80
Subnets: At least two AZs
Target Group: Existing target group
```

Test ALB before configuring Route 53:

```bash
curl http://ALB-DNS-NAME
```

Repeated requests should reach healthy targets.

---

# 13. Route 53 → ALB

Create record:

```text
Record name: www

Record type:
A

Alias:
ON

Route traffic to:
Alias to Application and Classic Load Balancer

Region:
Your ALB region

Load Balancer:
Select ALB
```

Architecture:

```text
www.atulkamble.in
       |
       v
   Route 53
       |
    A Alias
       |
       v
      ALB
    /     \
   v       v
EC2-1    EC2-2
```

### Important

Do **not** create an A record containing an ALB's changing IP address.

Use:

```text
A/AAAA Alias → ALB
```

---

# 14. EC2 vs ALB DNS Design

| Scenario | Route 53 Record |
|---|---|
| Domain → EC2 IPv4 | A |
| Domain → EC2 IPv6 | AAAA |
| Domain → ALB | Alias |
| Subdomain → hostname | CNAME |
| Domain → CloudFront | Alias |

### Production Preference

```text
Route 53
   ↓
ALB
   ↓
Target Group
   ↓
EC2 / Auto Scaling
```

is generally preferable to:

```text
Route 53
   ↓
Single EC2
```

because the load-balanced design supports multiple application instances and health-based target routing.

---

# 15. Routing Policies — Must Remember

| Policy | Main Purpose |
|---|---|
| Simple | Basic DNS routing |
| Weighted | Percentage-based traffic |
| Latency | Lowest-latency AWS endpoint |
| Failover | Primary/secondary DR |
| Geolocation | User-location rules |
| Geoproximity | Resource/user geography + bias |
| Multivalue Answer | Multiple healthy DNS answers |

### Memory Trick

```text
Simple      → One/basic destination

Weighted    → 80/20

Latency     → Best latency

Failover    → Primary/Secondary

Geolocation → User location

Geoproximity → Geographic proximity + bias

Multivalue  → Multiple healthy answers
```

---

# 16. Weighted Routing Example

```text
                 Route 53
                    |
             Weighted Routing
               /          \
             80%          20%
              |            |
              v            v
          Version 1    Version 2
```

Useful for:

- Canary deployments
- Blue/green migration
- Gradual traffic shifting

---

# 17. Failover Routing

```text
                  Route 53
                     |
                 Health Check
                     |
            +--------+--------+
            |                 |
          Healthy          Unhealthy
            |                 |
            v                 v
         Primary           Secondary
```

Used for disaster recovery.

---

# 18. Health Check Commands

Create an HTTP health check:

```bash
aws route53 create-health-check \
  --caller-reference "$(date +%s)" \
  --health-check-config \
  IPAddress=PUBLIC_IP,Port=80,Type=HTTP,ResourcePath=/
```

List:

```bash
aws route53 list-health-checks
```

Check status:

```bash
aws route53 get-health-check-status \
  --health-check-id HEALTH_CHECK_ID
```

Delete:

```bash
aws route53 delete-health-check \
  --health-check-id HEALTH_CHECK_ID
```

---

# 19. Essential CLI Commands

### Domains/Hosted Zones

```bash
aws route53 list-hosted-zones
```

```bash
aws route53 get-hosted-zone \
  --id ZONE_ID
```

### Records

```bash
aws route53 list-resource-record-sets \
  --hosted-zone-id ZONE_ID
```

### Health Checks

```bash
aws route53 list-health-checks
```

### DNS Testing

```bash
nslookup atulkamble.in
```

```bash
dig atulkamble.in
```

```bash
dig NS atulkamble.in
```

```bash
dig A www.atulkamble.in
```

```bash
curl -I http://www.atulkamble.in
```

---

# 20. Points to Remember

1. **Route 53 is a global DNS service.**
2. **Hosted Zone contains DNS records.**
3. **Public Hosted Zone = internet DNS.**
4. **Private Hosted Zone = VPC-associated private DNS.**
5. **A = hostname → IPv4.**
6. **AAAA = hostname → IPv6.**
7. **CNAME = hostname → another hostname.**
8. **Alias = Route 53 feature for supported AWS targets.**
9. **Use Alias for ALB rather than hard-coding ALB IP addresses.**
10. **Weighted = percentage-based traffic distribution.**
11. **Latency routing chooses based on AWS-measured latency, not simply geographic distance.**
12. **Failover = primary/secondary DR.**
13. **Geolocation = based on user DNS-query location.**
14. **Multivalue Answer can return multiple healthy records but is not a replacement for ALB.**
15. **TTL controls DNS record caching duration.**
16. **Always test the backend resource before blaming DNS.**
17. **For EC2 directly exposed through DNS, a changing public IPv4 can break the record; an Elastic IP can provide a stable IPv4.**
18. **For ALB, use the ALB DNS target through Route 53 Alias.**
19. **Security Groups/NACLs still control network access; Route 53 does not bypass them.**
20. **DNS propagation/caching can cause old answers to remain temporarily.**

## Final Training Flow

```text
1. Understand DNS
        ↓
2. Register/Use Domain
        ↓
3. Check Hosted Zone
        ↓
4. Understand A/CNAME/Alias
        ↓
5. Route 53 → EC2 Lab
        ↓
6. Verify using dig/nslookup/curl
        ↓
7. Create Target Group
        ↓
8. Create ALB + EC2
        ↓
9. Route 53 Alias → ALB
        ↓
10. Weighted Routing
        ↓
11. Health Checks
        ↓
12. Failover Routing
```

For a focused Route 53 class, these are the highest-value labs: **Domain → EC2**, **Domain → ALB**, **Weighted Routing**, and **Failover + Health Check**.
