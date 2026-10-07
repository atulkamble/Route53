
1. Create Launch Template 
webserver | Amazon linux | webserver.pem | SG - http, https, ssh
Advanced >> user data 

#!/bin/bash
dnf install -y httpd
systemctl enable --now httpd

echo "<h1>Web Server Running</h1>
<h2>Hostname: $(hostname)</h2>" > /var/www/html/index.html

2. 
Create ASG >> Select Launch Template (webserver) Select AZs & Mapp it
Attach New Load Balancer >> MyLoadBalancer >> Internet Facing 
Create TargetGroup >> MyTargetGroup

3. http://myasg-30000316.us-east-1.elb.amazonaws.com/

4. 

aws elbv2 describe-load-balancers --query "LoadBalancers[*].[LoadBalancerName,DNSName]" --output table

curl myASG-30000316.us-east-1.elb.amazonaws.com

curl -I myASG-30000316.us-east-1.elb.amazonaws.com

ping myASG-30000316.us-east-1.elb.amazonaws.com

nslookup myASG-30000316.us-east-1.elb.amazonaws.com

dig myASG-30000316.us-east-1.elb.amazonaws.com

whois myASG-30000316.us-east-1.elb.amazonaws.com

host myASG-30000316.us-east-1.elb.amazonaws.com

5. Route53 >> Add Record 

Record name: atulkamble.in
Type: A
Alias: Yes
Route traffic to:
  Alias to Application Load Balancer >> Region >> ALB Visible, Select your ALB

6. // Delete Resources  
Delete A Record from Route 53
Unregister instances from Target Group
Delete Load Balancer
Delete AutoScaling Group
Delete Instances
Delete Target Group
