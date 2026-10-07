For your setup:

**`atulkamble.in → Route 53 → ALB → ASG → EC2`**

### Architecture

```text
User
  │
  ▼
atulkamble.in
  │
  ▼
Route 53
  │
  │ A / Alias Record
  ▼
Application Load Balancer
  │
  ▼
Target Group
  │
  ▼
Auto Scaling Group
  │
  ├── EC2
  ├── EC2
  └── EC2
```

### Short Steps

1. **Create Launch Template**
   - AMI: Amazon Linux
   - SG: Allow HTTP `80` from **ALB SG**
   - Add web-server User Data.

```bash
#!/bin/bash
dnf install -y httpd
systemctl enable --now httpd
echo "<h1>Welcome to atulkamble.in</h1>" > /var/www/html/index.html
```

2. **Create Target Group**
   - Target: Instances
   - Protocol: HTTP
   - Port: `80`
   - Health check: `/`

3. **Create Application Load Balancer**
   - Scheme: Internet-facing
   - Select 2 public subnets
   - ALB SG: Allow `HTTP 80` from `0.0.0.0/0`
   - Listener: `HTTP : 80 → Target Group`

4. **Create Auto Scaling Group**
   - Use Launch Template
   - Select at least 2 subnets
   - Attach existing Target Group
   - Example:

```text
Minimum: 2
Desired: 2
Maximum: 4
```

5. **Test ALB first**

```bash
curl http://<ALB-DNS-NAME>
```

Get ALB DNS:

```bash
aws elbv2 describe-load-balancers \
  --query "LoadBalancers[*].[LoadBalancerName,DNSName]" \
  --output table
```

6. **Route 53 → Hosted Zone → `atulkamble.in`**

Create:

```text
Record name: atulkamble.in
Type: A
Alias: Yes
Route traffic to:
  Alias to Application Load Balancer
Select your ALB
```

For `www`:

```text
www.atulkamble.in
Type: A
Alias: Yes
Target: Same ALB
```

7. **Verify DNS**

```bash
dig atulkamble.in

nslookup atulkamble.in

curl http://atulkamble.in
```

### Important SG Flow

```text
Internet
   │ HTTP 80
   ▼
ALB Security Group
   │
   │ HTTP 80
   ▼
EC2 Security Group
```

**ALB SG**

```text
Inbound:
HTTP | TCP | 80 | 0.0.0.0/0
```

**EC2 SG**

```text
Inbound:
HTTP | TCP | 80 | <ALB-Security-Group>
```

### Final Flow

```text
atulkamble.in
      ↓
Route 53 A-Alias
      ↓
ALB :80
      ↓
Target Group :80
      ↓
ASG
      ↓
EC2 Apache :80
```

For production, the next step is **ACM certificate → ALB HTTPS 443 → Route 53**, giving `https://atulkamble.in`.
