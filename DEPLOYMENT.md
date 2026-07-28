# Deployment Guide - Scalable Web Application

This guide provides step-by-step instructions for deploying the Scalable Web Application on AWS.

## Prerequisites

### AWS Account Setup
1. AWS Account with administrative access
2. AWS CLI installed and configured
3. Sufficient service limits for:
   - EC2 instances (t3.micro)
   - RDS instances (db.t3.micro)
   - VPCs and subnets

### Local Setup
```bash
# Install AWS CLI
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install

# Configure AWS CLI
aws configure
# Enter your AWS Access Key ID
# Enter your AWS Secret Access Key
# Enter your default region (e.g., us-east-1)
# Enter default output format (json)
```

## Quick Deployment

### Option 1: Automated Deployment (Recommended)

```bash
# Clone the repository
git clone https://github.com/AnasGara/Project_ScalableAPP_AWS
cd scalable-webapp-aws

# Set environment variables
export AWS_REGION=us-east-1
export ALERT_EMAIL=your-email@example.com
export DB_PASSWORD=YourSecurePassword123!

# Make scripts executable
chmod +x scripts/*.sh

# Deploy all stacks
./scripts/deploy-all.sh
```

### Option 2: Manual Deployment

#### Step 1: Deploy VPC Stack

```bash
aws cloudformation deploy \
  --template-file cloudformation/vpc-stack.yaml \
  --stack-name scalable-webapp-vpc \
  --capabilities CAPABILITY_IAM \
  --region us-east-1
```

#### Step 2: Deploy Application Stack

```bash
aws cloudformation deploy \
  --template-file cloudformation/app-stack.yaml \
  --stack-name scalable-webapp-app \
  --parameter-overrides \
    VpcStackName=scalable-webapp-vpc \
  --capabilities CAPABILITY_IAM \
  --region us-east-1
```

#### Step 3: Deploy RDS Stack

```bash
aws cloudformation deploy \
  --template-file cloudformation/rds-stack.yaml \
  --stack-name scalable-webapp-rds \
  --parameter-overrides \
    VpcStackName=scalable-webapp-vpc \
    DBPassword=YourSecurePassword123! \
  --capabilities CAPABILITY_IAM \
  --region us-east-1
```

#### Step 4: Deploy Monitoring Stack

```bash
aws cloudformation deploy \
  --template-file cloudformation/monitoring-stack.yaml \
  --stack-name scalable-webapp-monitoring \
  --parameter-overrides \
    AlertEmail=your-email@example.com \
  --capabilities CAPABILITY_IAM \
  --region us-east-1
```

#### Step 5: Deploy CDN/DNS Stack (Optional)

```bash
aws cloudformation deploy \
  --template-file cloudformation/cdn-dns-stack.yaml \
  --stack-name scalable-webapp-cdn \
  --parameter-overrides \
    DomainName=app.yourdomain.com \
    HostedZoneId=Z1234567890 \
  --capabilities CAPABILITY_IAM \
  --region us-east-1
```

## Post-Deployment

### 1. Verify Deployment

```bash
# Check stack status
aws cloudformation list-stacks \
  --stack-status-filter CREATE_COMPLETE UPDATE_COMPLETE \
  --region us-east-1

# Get ALB DNS Name
ALB_DNS=$(aws cloudformation describe-stacks \
  --stack-name scalable-webapp-app \
  --query 'Stacks[0].Outputs[?OutputKey==`ALBDNSName`].OutputValue' \
  --output text \
  --region us-east-1)

echo "ALB DNS Name: $ALB_DNS"
echo "Application URL: http://$ALB_DNS"
```

### 2. Test the Application

1. Open browser and navigate to `http://$ALB_DNS`
2. Verify the web page loads correctly
3. Check health endpoint: `http://$ALB_DNS/health.html`
4. Verify instance information is displayed

### 3. Test Auto Scaling

```bash
# Get Auto Scaling Group name
ASG_NAME=$(aws cloudformation describe-stacks \
  --stack-name scalable-webapp-app \
  --query 'Stacks[0].Outputs[?OutputKey==`AutoScalingGroupName`].OutputValue' \
  --output text \
  --region us-east-1)

# Manually scale out
aws autoscaling set-desired-capacity \
  --auto-scaling-group-name $ASG_NAME \
  --desired-capacity 4 \
  --region us-east-1

# Wait for instances to launch
sleep 300

# Check instance count
aws autoscaling describe-auto-scaling-groups \
  --auto-scaling-group-names $ASG_NAME \
  --query 'AutoScalingGroups[0].Instances[*].[InstanceId,LifeCycleState,HealthStatus]' \
  --output table \
  --region us-east-1

# Scale back in
aws autoscaling set-desired-capacity \
  --auto-scaling-group-name $ASG_NAME \
  --desired-capacity 2 \
  --region us-east-1
```

### 4. Access Instances via Session Manager

```bash
# Get instance IDs
INSTANCE_IDS=$(aws ec2 describe-instances \
  --filters "Name=tag:Name,Values=scalable-webapp-ec2" \
  --query 'Reservations[*].Instances[*].[InstanceId]' \
  --output text \
  --region us-east-1)

# Connect to first instance
INSTANCE_ID=$(echo $INSTANCE_IDS | awk '{print $1}')
aws ssm start-session --target $INSTANCE_ID --region us-east-1
```

## Monitoring

### CloudWatch Dashboard

1. Go to AWS Console → CloudWatch → Dashboards
2. Open `scalable-webapp-dashboard`
3. Monitor:
   - ALB request count and response time
   - EC2 CPU utilization
   - Auto Scaling group capacity
   - RDS metrics

### CloudWatch Alarms

Check alarm status:
```bash
aws cloudwatch describe-alarms \
  --alarm-name-prefix scalable-webapp \
  --query 'MetricAlarms[*].[AlarmName,StateReason]' \
  --output table \
  --region us-east-1
```

## Cleanup

### Destroy All Resources

```bash
# Run the destroy script
./scripts/destroy-all.sh
```

### Manual Cleanup

```bash
# Delete stacks in reverse order
aws cloudformation delete-stack --stack-name scalable-webapp-cdn --region us-east-1
aws cloudformation delete-stack --stack-name scalable-webapp-monitoring --region us-east-1
aws cloudformation delete-stack --stack-name scalable-webapp-rds --region us-east-1
aws cloudformation delete-stack --stack-name scalable-webapp-app --region us-east-1
aws cloudformation delete-stack --stack-name scalable-webapp-vpc --region us-east-1

# Wait for deletion
aws cloudformation wait stack-delete-complete --stack-name scalable-webapp-cdn --region us-east-1
aws cloudformation wait stack-delete-complete --stack-name scalable-webapp-monitoring --region us-east-1
aws cloudformation wait stack-delete-complete --stack-name scalable-webapp-rds --region us-east-1
aws cloudformation wait stack-delete-complete --stack-name scalable-webapp-app --region us-east-1
aws cloudformation wait stack-delete-complete --stack-name scalable-webapp-vpc --region us-east-1
```

## Troubleshooting

### Common Issues

| Issue | Solution |
|-------|----------|
| Stack creation fails | Check CloudFormation events for error messages |
| EC2 instances not healthy | Verify security group rules and subnet routing |
| ALB health checks failing | Ensure web server is running on port 80 |
| Cannot connect via SSH | Use Session Manager instead (no SSH needed) |
| RDS connection refused | Check RDS security group allows traffic from EC2 |

### Useful Commands

```bash
# Check CloudFormation events
aws cloudformation describe-stack-events \
  --stack-name scalable-webapp-app \
  --query 'StackEvents[*].[Timestamp,ResourceStatus,ResourceType,LogicalResourceId]' \
  --output table \
  --region us-east-1

# Check EC2 instances
aws ec2 describe-instances \
  --filters "Name=tag:Name,Values=scalable-webapp-ec2" \
  --query 'Reservations[*].Instances[*].[InstanceId,State.Name,PrivateIpAddress]' \
  --output table \
  --region us-east-1

# Check ALB targets
aws elbv2 describe-target-health \
  --target-group-arn $(aws cloudformation describe-stacks \
    --stack-name scalable-webapp-app \
    --query 'Stacks[0].Outputs[?OutputKey==`TargetGroupArn`].OutputValue' \
    --output text \
    --region us-east-1) \
  --region us-east-1
```

## Cost Management

### Monitor Costs

1. Go to AWS Console → Billing and Cost Management
2. Review costs by service
3. Set up billing alerts

### Cost Optimization Tips

- Use Spot Instances for non-critical workloads
- Right-size instances based on utilization
- Delete unused resources
- Use Reserved Instances for predictable workloads

## Security Best Practices

1. **Never commit credentials** to version control
2. **Use IAM roles** instead of access keys
3. **Enable MFA** on AWS accounts
4. **Rotate credentials** regularly
5. **Monitor CloudTrail** for suspicious activity
6. **Keep software updated** on EC2 instances
