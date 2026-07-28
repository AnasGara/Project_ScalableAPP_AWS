# Scalable Web Application with ALB and Auto Scaling

**[AWS Solutions Implementation](https://aws.amazon.com/solutions/implementations/)** | **[🚧 Feature Request](https://github.com/AnasGara/Project_ScalableAPP_AWS/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=)** | **[🐛 Bug Report](https://github.com/AnasGara/Project_ScalableAPP_AWS/issues/new?assignees=&labels=bug&template=bug_report.md&title=)** | **[❓ General Question](https://github.com/AnasGara/Project_ScalableAPP_AWS/issues/new?assignees=&labels=question&template=general_question.md&title=)**

**Note**: This is a production-grade web application deployment on AWS using EC2 instances with a highly available and scalable architecture.

## Table of Content

- [Solution Overview](#solution-overview)
- [Architecture Diagram](#architecture-diagram)
- [Key AWS Services](#key-aws-services)
- [Prerequisites for Deployment](#prerequisites-for-deployment)
  - [1. Clone the Repository](#1-clone-the-repository)
  - [2. Configure AWS CLI](#2-configure-aws-cli)
  - [3. Deploy the Infrastructure](#3-deploy-the-infrastructure)
  - [4. Testing and Validation](#4-testing-and-validation)
- [Security Considerations](#security-considerations)
- [Monitoring and Alerts](#monitoring-and-alerts)
- [License](#license)

## Solution Overview

This solution deploys a production-grade web application on AWS using EC2 instances inside a properly architected VPC with public and private subnets across two Availability Zones. The architecture achieves high availability and scalability with Application Load Balancer (ALB), Auto Scaling Group (ASG), and CloudFront distribution for caching static assets. A Multi-AZ RDS instance serves as the database backend, with all compute resources placed in private subnets for enhanced security.

The implementation follows AWS Well-Architected Framework best practices, ensuring security, reliability, performance efficiency, cost optimization, and operational excellence. By leveraging AWS Systems Manager Session Manager, the solution eliminates the need for bastion hosts, providing secure instance access without exposing SSH ports to the internet.

## Solution Architecture Diagram

![Solution Architecture](https://raw.githubusercontent.com/AnasGara/Project_ScalableAPP_AWS/main/Solution.png)

*Figure 1: High-level architecture of the Scalable Web Application with ALB and Auto Scaling*

## Key AWS Services

The solution integrates the following AWS services to build a robust, scalable, and secure web application infrastructure:

- **VPC**: Public & private subnets across two Availability Zones, NAT Gateway, Security Groups, and Network ACLs for network segmentation and traffic control
- **EC2 + ASG**: Launch Templates with Auto Scaling Groups using target tracking scaling policies for dynamic capacity management
- **ALB + WAF**: Application Load Balancer with Layer 7 routing, integrated with WAF rules for OWASP Top 10 security protections
- **CloudFront**: Global content delivery network for caching static assets, reducing latency and improving user experience
- **RDS Multi-AZ**: MySQL/PostgreSQL database with automated failover capability for high availability
- **Route 53**: DNS management with alias records pointing to ALB and health checks for service monitoring
- **Systems Manager**: Session Manager for secure instance access without bastion hosts
- **CloudWatch + SNS**: Comprehensive monitoring with dashboards, alarms, and notification system

## Prerequisites for Deployment

### 1. Clone the Repository

```bash
git clone https://github.com/AnasGara/Project_ScalableAPP_AWS
cd ScalableWebAPP_Project
export MAIN_DIRECTORY=$PWD
```
### 2. Configure AWS CLI

Ensure you have AWS CLI installed and configured with appropriate credentials:

bash

```
aws configure
```
# Enter your Access Key ID, Secret Access Key, Default region, and output format
```
### 3. Deploy the Infrastructure
```
bash

cd $MAIN_DIRECTORY/infrastructure
npm install
npm run build
```

# Bootstrap CDK (if not already done)
npx cdk bootstrap --profile <PROFILE_NAME>
# Deploy the stack
```

npx cdk deploy WebAppStack \
  --parameters VpcCIDR=10.0.0.0/16 \
  --parameters KeyPairName=<YOUR_KEY_PAIR> \
  --parameters DBUsername=<DB_USERNAME> \
  --parameters DBPassword=<DB_PASSWORD> \
  --profile <PROFILE_NAME>
```

_Note:_

- **PROFILE_NAME**: Name of an AWS CLI profile with appropriate permissions
    
- **KeyPairName**: Existing EC2 Key Pair for instance access
    
- **DBUsername**: Master username for RDS database
    
- **DBPassword**: Master password for RDS database (minimum 8 characters)
    

### 4. Testing and Validation

After deployment, validate your infrastructure:
```
bash

# Get the CloudFront distribution URL
aws cloudfront list-distributions --profile <PROFILE_NAME> | grep DomainName
# Test the application endpoint
curl -I https://<cloudfront-domain>/health
# Verify Auto Scaling group status
aws autoscaling describe-auto-scaling-groups --profile <PROFILE_NAME> | grep DesiredCapacity
```
## Security Considerations

This solution implements multiple layers of security:

- **Network Security**: All compute resources are deployed in private subnets
    
- **Application Security**: WAF rules protect against OWASP Top 10 vulnerabilities
    
- **Database Security**: RDS is isolated in private subnets with no public access
    
- **Access Control**: Security Groups and NACLs provide fine-grained traffic control
    
- **Instance Access**: Systems Manager Session Manager enables secure, audited instance access
    
- **Encryption**: Data in transit using TLS/SSL; data at rest using AWS managed or customer-managed KMS keys
    

## Monitoring and Alerts

The solution includes comprehensive monitoring capabilities:

- **CloudWatch Dashboards**: Visual representation of key metrics
    
- **Alarms**: Automated alerts for CPU utilization, error rates, and health checks
    
- **SNS Notifications**: Email alerts for critical events
    
- **Logging**: CloudWatch Logs for application and system logs
    
- **Tracing**: Integration with AWS X-Ray for request tracing
    

## License

Copyright [Amazon.com](https://amazon.com/), Inc. or its affiliates. All Rights Reserved.  
SPDX-License-Identifier: Apache-2.0

---

## Contributing

We welcome contributions! Please see our [Contributing Guidelines](https://contributing.md/) for more information on how to submit pull requests, report bugs, or request features.

