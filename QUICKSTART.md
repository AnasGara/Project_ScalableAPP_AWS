# Quick Start Guide - Scalable Web Application on AWS

## Prerequisites

- AWS Account with admin access
- AWS CLI installed and configured
- Git installed

## 5-Minute Deployment

### Step 1: Clone and Navigate
```bash
git clone https://github.com/YOUR_USERNAME/scalable-webapp-aws.git
cd scalable-webapp-aws
```

### Step 2: Set Variables
```bash
export AWS_REGION=us-east-1
export ALERT_EMAIL=your-email@example.com
export DB_PASSWORD=YourSecurePassword123!
```

### Step 3: Deploy
```bash
chmod +x scripts/*.sh
./scripts/deploy-all.sh
```

### Step 4: Get Application URL
```bash
ALB_DNS=$(aws cloudformation describe-stacks \
  --stack-name scalable-webapp-app \
  --query 'Stacks[0].Outputs[?OutputKey==`ALBDNSName`].OutputValue' \
  --output text \
  --region $AWS_REGION)

echo "Application URL: http://$ALB_DNS"
```

### Step 5: Open in Browser
Copy the URL and open in your browser!

---

## What Gets Deployed

| Resource | Description |
|----------|-------------|
| VPC | Network with 6 subnets across 2 AZs |
| EC2 | 2 web servers in private subnets |
| ALB | Load balancer distributing traffic |
| RDS | Multi-AZ database |
| CloudFront | CDN for static assets |
| WAF | Web application firewall |
| CloudWatch | Monitoring dashboard |

---

## Cost

**Estimated Monthly Cost**: ~$198 USD (US East-1)

> New AWS accounts may qualify for free tier benefits.

---

## Cleanup

```bash
./scripts/destroy-all.sh
```

---

## Need Help?

- Read `DEPLOYMENT.md` for detailed instructions
- Check `README.md` for full documentation
- Review AWS CloudFormation events for errors
