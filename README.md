<div align="center">
<h1>🚀 🌐 AWS Route 53 – Complete Guide (Theory + Hands-On + CLI)</h1>
<p><strong>Built with ❤️ by <a href="https://github.com/atulkamble">Atul Kamble</a></strong></p>

<p>
<a href="https://codespaces.new/atulkamble/template.git">
<img src="https://github.com/codespaces/badge.svg" alt="Open in GitHub Codespaces" />
</a>
<a href="https://vscode.dev/github/atulkamble/template">
<img src="https://img.shields.io/badge/Open%20with-VS%20Code-007ACC?logo=visualstudiocode&style=for-the-badge" alt="Open with VS Code" />
</a>
<a href="https://vscode.dev/redirect?url=vscode://ms-vscode-remote.remote-containers/cloneInVolume?url=https://github.com/atulkamble/template">
<img src="https://img.shields.io/badge/Dev%20Containers-Ready-blue?logo=docker&style=for-the-badge" />
</a>
<a href="https://desktop.github.com/">
<img src="https://img.shields.io/badge/GitHub-Desktop-6f42c1?logo=github&style=for-the-badge" />
</a>
</p>

<p>
<a href="https://github.com/atulkamble">
<img src="https://img.shields.io/badge/GitHub-atulkamble-181717?logo=github&style=flat-square" />
</a>
<a href="https://www.linkedin.com/in/atuljkamble/">
<img src="https://img.shields.io/badge/LinkedIn-atuljkamble-0A66C2?logo=linkedin&style=flat-square" />
</a>
<a href="https://x.com/atul_kamble">
<img src="https://img.shields.io/badge/X-@atul_kamble-000000?logo=x&style=flat-square" />
</a>
</p>

<strong>Version 1.0.0</strong> | <strong>Last Updated:</strong> January 2026
</div>

---


![Image](https://d2908q01vomqb2.cloudfront.net/fc074d501302eb2b93e2554793fcaf50b3bf7291/2023/05/10/Figure-1.-Solution-architecture.png)

![Image](https://kodekloud.com/kk-media/image/upload/v1752860904/notes-assets/images/AWS-Certified-SysOps-Administrator-Associate-Route-53-Routing-Policies/latency-routing-policy-route53.jpg)

![Image](https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2022/09/20/fig1.jpg)

![Image](https://disaster-recovery.workshop.aws/images/route53-lab-architecture.png)

![Image](https://media.licdn.com/dms/image/v2/D4D12AQF21kyB-Kdchg/article-cover_image-shrink_720_1280/article-cover_image-shrink_720_1280/0/1700685344098?e=2147483647\&t=ls62fhWFYdgatp7xkM7jzaG3XtWCuwRPzJid_oW4aZo\&v=beta)

---

## 1️⃣ What is AWS Route 53?

**Amazon Route 53** is a **highly available, scalable DNS (Domain Name System)** service provided by **Amazon Web Services**.

### Core Capabilities

| Feature             | Description                 |
| ------------------- | --------------------------- |
| DNS Service         | Domain → IP resolution      |
| Domain Registration | Buy & manage domains        |
| Traffic Routing     | Route users intelligently   |
| Health Checks       | Monitor endpoint health     |
| Failover            | Automatic disaster recovery |

---

## 2️⃣ Route 53 Core Components

| Component         | Purpose              |
| ----------------- | -------------------- |
| Domain            | e.g. `example.com`   |
| Hosted Zone       | DNS record container |
| Record Set        | DNS mappings         |
| Name Servers (NS) | Route DNS queries    |
| Health Check      | Endpoint monitoring  |

---

## 3️⃣ Hosted Zones (VERY IMPORTANT)

| Type                | Description         | Use Case                     |
| ------------------- | ------------------- | ---------------------------- |
| Public Hosted Zone  | Internet-facing DNS | Websites, APIs               |
| Private Hosted Zone | VPC-internal DNS    | Microservices, internal apps |

---

## 4️⃣ DNS Record Types (Exam Favorite)

| Record | Purpose                        |
| ------ | ------------------------------ |
| A      | IPv4 address                   |
| AAAA   | IPv6 address                   |
| CNAME  | Alias to another domain        |
| ALIAS  | AWS-specific (ELB, CloudFront) |
| MX     | Mail server                    |
| TXT    | Verification, SPF              |
| NS     | Name servers                   |
| SOA    | Zone authority                 |

⚠️ **ALIAS vs CNAME**

| ALIAS                | CNAME              |
| -------------------- | ------------------ |
| AWS only             | Standard DNS       |
| Works at root domain | ❌ Root not allowed |
| No extra cost        | Standard           |

---

## 5️⃣ Routing Policies (MOST IMPORTANT)

![Image](https://miro.medium.com/1%2AKTmaVfLyHPQ-r4xn0gp1XA.png)

![Image](https://miro.medium.com/v2/resize%3Afit%3A1400/1%2A7MFbeQdI2I4HfSUCMHIQFQ.png)

![Image](https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2022/09/20/fig1.jpg)

![Image](https://media.licdn.com/dms/image/v2/D4D12AQF21kyB-Kdchg/article-cover_image-shrink_720_1280/article-cover_image-shrink_720_1280/0/1700685344098?e=2147483647\&t=ls62fhWFYdgatp7xkM7jzaG3XtWCuwRPzJid_oW4aZo\&v=beta)

![Image](https://disaster-recovery.workshop.aws/images/route53-lab-architecture.png)

![Image](https://d1tcczg8b21j1t.cloudfront.net/strapi-assets/32_Route_53_health_checks_12_317621ea21.png)

![Image](https://d1tcczg8b21j1t.cloudfront.net/strapi-assets/32_Route_53_health_checks_1_6163d5294d.png)

| Policy       | Use Case              |
| ------------ | --------------------- |
| Simple       | Single endpoint       |
| Weighted     | Blue-Green, Canary    |
| Failover     | DR architecture       |
| Latency      | Nearest region        |
| Geolocation  | Country-based         |
| Geoproximity | Traffic bias          |
| Multi-Value  | Simple load balancing |

---

## 6️⃣ Health Checks

| Type             | Description     |
| ---------------- | --------------- |
| HTTP/HTTPS       | Web endpoint    |
| TCP              | Port check      |
| CloudWatch Alarm | Metric-based    |
| Calculated       | Combined checks |

### Health Check Status

* Healthy
* Unhealthy
* Insufficient data

---

## 7️⃣ Route 53 Architectures

### 🔹 Basic Website

```
User → Route53 → ALB → EC2
```

### 🔹 Highly Available Multi-Region

```
User
 ├─ Route53 (Latency)
 │   ├─ ALB (us-east-1)
 │   └─ ALB (ap-south-1)
```

### 🔹 Failover DR

```
Primary → Health Check ❌
          ↓
Secondary → Auto Redirect
```

---

## 8️⃣ AWS CLI – Route 53 Cheat Sheet

### 🔹 List Hosted Zones

```bash
aws route53 list-hosted-zones
```

### 🔹 Create Hosted Zone

```bash
aws route53 create-hosted-zone \
  --name example.com \
  --caller-reference $(date +%s)
```

### 🔹 List Record Sets

```bash
aws route53 list-resource-record-sets \
  --hosted-zone-id ZXXXXXXXX
```

### 🔹 Create A Record (JSON)

```json
{
  "Changes": [{
    "Action": "CREATE",
    "ResourceRecordSet": {
      "Name": "www.example.com",
      "Type": "A",
      "TTL": 300,
      "ResourceRecords": [{ "Value": "1.2.3.4" }]
    }
  }]
}
```

```bash
aws route53 change-resource-record-sets \
 --hosted-zone-id ZXXXX \
 --change-batch file://record.json
```

---

## 9️⃣ Important Points to Remember (EXAM GOLD)

✅ Route 53 is **global**
✅ Supports **Alias records**
✅ Health checks are **optional but powerful**
✅ Failover requires **health checks**
✅ TTL impacts **DNS caching**
✅ Cannot use CNAME at root domain
✅ Alias works at root domain
✅ Latency routing improves performance
✅ Geolocation ≠ Geoproximity

---

## 🔟 Route 53 vs ELB vs CloudFront

| Feature        | Route 53 | ELB | CloudFront |
| -------------- | -------- | --- | ---------- |
| DNS            | ✅        | ❌   | ❌          |
| Load Balancing | ❌        | ✅   | ✅          |
| Global         | ✅        | ❌   | ✅          |
| Caching        | ❌        | ❌   | ✅          |
| Health Checks  | ✅        | ✅   | ❌          |

---

## 1️⃣1️⃣ Route 53 + Other AWS Services

| Service           | Integration  |
| ----------------- | ------------ |
| EC2               | A / Alias    |
| ALB/NLB           | Alias        |
| CloudFront        | Alias        |
| S3 Static Website | Alias        |
| API Gateway       | Alias        |
| EKS Ingress       | External DNS |

---

## 1️⃣2️⃣ Common Interview Questions

**Q1. Why Route 53 is called Route?**
👉 It routes traffic using intelligent policies.

**Q2. Difference between Latency & Geo?**
👉 Latency = performance based
👉 Geo = user location

**Q3. Can Route 53 replace Load Balancer?**
👉 ❌ No, it’s DNS-level routing.

---

## 1️⃣3️⃣ Real-World Use Cases

✔️ Blue-Green deployment
✔️ Canary release
✔️ Disaster recovery
✔️ Global SaaS routing
✔️ Multi-region apps
✔️ Cost-optimized routing

---

## 1️⃣4️⃣ Sample GitHub Repo Names

* `aws-route53-complete-guide`
* `route53-dns-architectures`
* `aws-route53-cli-cheatsheet`
* `route53-failover-demo`
* `aws-dns-routing-patterns`

---

## 1️⃣5️⃣ Quick One-Line Revision

> **Route 53 is a global DNS service that routes traffic using policies like latency, failover, and geolocation with optional health checks.**

---
