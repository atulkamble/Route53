# Domain & Hosting Basics

## 1. Domain Name

A **domain name** is the human-readable address of a website.

```text
google.com
amazon.com
example.com
```

Instead of remembering:

```text
3.90.245.64
```

we use:

```text
example.com
```

---

## 2. Domain Structure

```text
www.example.com
 │      │     │
 │      │     └── TLD (.com)
 │      └──────── Domain Name
 └────────────── Subdomain
```

| Term | Example |
|---|---|
| Domain | `example.com` |
| TLD | `.com` |
| Subdomain | `www.example.com` |

---

## 3. Domain Registrar

A **registrar** is where a domain is registered/purchased.

```text
Domain Registrar
      ↓
 example.com
```

AWS Route 53 can also register domains.

---

## 4. Hosting

**Hosting** is where the website/application runs.

Examples in AWS:

```text
EC2
S3
Load Balancer
CloudFront
```

Example:

```text
EC2
Public IP: 3.90.245.64
        ↓
     Website
```

---

## 5. DNS

**DNS = Domain Name System**

DNS connects the domain name to the server/resource.

```text
example.com
     ↓
    DNS
     ↓
3.90.245.64
     ↓
   Server
     ↓
  Website
```

---

## 6. Important DNS Records

| Record | Purpose |
|---|---|
| **A** | Domain → IPv4 |
| **AAAA** | Domain → IPv6 |
| **CNAME** | Domain/name → another domain/name |
| **MX** | Email routing |
| **NS** | Name servers |
| **TXT** | Verification/text information |

---

## 7. Name Server

Name servers tell the internet **where the DNS records for a domain are managed**.

```text
Domain
  ↓
Name Servers
  ↓
DNS Records
```

---

## 8. Domain vs DNS vs Hosting

```text
Domain
example.com
    ↓
DNS
Find destination
    ↓
Hosting
EC2 / ALB / S3
    ↓
Website
```

| Component | Purpose |
|---|---|
| **Domain** | Website name |
| **DNS** | Finds/routes to the destination |
| **Hosting** | Runs/stores the website |

## Then Learn Route 53

Once these concepts are clear:

```text
Domain
   ↓
DNS
   ↓
DNS Records
   ↓
Name Servers
   ↓
Hosting
   ↓
Route 53
```
## Who Provides Domain Names?

Domain names are coordinated globally by **ICANN** and domain registries, while users normally purchase/register domains through a **Domain Registrar**.

```text
ICANN
  ↓
Registry
  ↓
Domain Registrar
  ↓
Customer
```

Example registrars include GoDaddy, Namecheap, and AWS Route 53 Domains.

### Simple Example

You want:

```text
atulkamble.in
```

Flow:

```text
You
 │
 │ Search for atulkamble.in
 ▼
Domain Registrar
 │
 │ Checks availability with registry
 ▼
Registry (.in)
 │
 │ Available
 ▼
Registrar
 │
 │ You register/pay
 ▼
You control atulkamble.in
```

## After Buying the Domain

The important technical flow is:

```text
Domain Registrar
      │
      │ Configure Name Servers
      ▼
DNS Provider
e.g. Route 53
      │
      │ DNS Record
      ▼
A / AAAA / CNAME / Alias
      │
      ▼
Hosting
EC2 / ALB / S3 / CloudFront
      │
      ▼
Website
```

For example:

```text
atulkamble.in
      ↓
Route 53 DNS
      ↓
A / Alias Record
      ↓
AWS Load Balancer / EC2
      ↓
Website
```

### Remember

**Registrar → Domain**

**DNS Provider → DNS Records**

**Hosting Provider → Website/Application**

Route 53 can provide both **domain registration** and **DNS hosting**, but these are still separate concepts.

# DNS Records — Core Concepts

A **DNS record** tells DNS where a domain/subdomain should go or what service it uses.

```text
Domain
   ↓
DNS Server
   ↓
DNS Record
   ↓
Destination
```

| Record | Purpose | Example |
|---|---|---|
| **A** | Name → IPv4 | `www.example.com → 3.90.245.64` |
| **AAAA** | Name → IPv6 | `www.example.com → 2001:db8::1` |
| **CNAME** | Name → another hostname | `www.example.com → example.com` |
| **MX** | Specifies mail server | `example.com → mail.example.com` |
| **NS** | Specifies authoritative name servers | `example.com → ns-xxx.awsdns.com` |
| **TXT** | Stores text/verification data | SPF, domain verification |
| **SOA** | Contains DNS zone information | Primary NS, serial, timers |
| **Alias** | AWS name → AWS resource | `example.com → ALB/CloudFront` |

## Most Important for Route 53

Focus first on:

```text
A       → IPv4
AAAA    → IPv6
CNAME   → Another hostname
Alias   → AWS resource
NS      → Name servers
MX      → Email
TXT     → Verification
```

### Website Example

```text
User enters
www.example.com
       ↓
Route 53
       ↓
DNS Record
       ↓
A Record
       ↓
3.90.245.64
       ↓
EC2 Web Server
       ↓
Website
```

### Easy Way to Remember

**A = Address (IPv4)**  
**AAAA = IPv6 Address**  
**CNAME = Another Name**  
**MX = Mail**  
**NS = Name Server**  
**TXT = Text**  
**Alias = AWS Resource**

# Domain & DNS Commands

For macOS/Linux, these are the main commands worth knowing before Route 53.

| Command | Purpose |
|---|---|
| `nslookup example.com` | Basic DNS lookup |
| `dig example.com` | Detailed DNS lookup |
| `dig example.com A` | Check IPv4/A record |
| `dig example.com AAAA` | Check IPv6 record |
| `dig example.com MX` | Check mail records |
| `dig example.com NS` | Check name servers |
| `dig example.com TXT` | Check TXT records |
| `dig www.example.com CNAME` | Check CNAME |
| `whois example.com` | Check domain registration information |
| `host example.com` | Simple domain lookup |
| `ping example.com` | Check name resolution/connectivity |
| `curl -I https://example.com` | Check website HTTP response |

## Most Useful Practice

```bash
# Find IP
nslookup amazon.com

# Check A record
dig amazon.com A

# Check name servers
dig amazon.com NS

# Check mail servers
dig amazon.com MX

# Check TXT records
dig amazon.com TXT

# Domain registration information
whois amazon.com

# Test website
curl -I https://amazon.com
```

### Trace DNS Resolution

```bash
dig +trace example.com
```

This is especially useful for understanding the flow:

```text
Root DNS
   ↓
TLD (.com)
   ↓
Authoritative Name Server
   ↓
DNS Record
   ↓
IP / Destination
```
