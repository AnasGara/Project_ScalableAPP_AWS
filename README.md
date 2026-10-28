# Scalable Web Application with ALB and Auto Scaling

**[AWS Solutions Implementation](https://aws.amazon.com/solutions/implementations/)** | **[🚧 Feature Request](https://github.com/AnasGara/Project_ScalableAPP_AWS/issues/new?assignees=&labels=enhancement&template=feature_request.md&title=)** | **[🐛 Bug Report](https://github.com/AnasGara/Project_ScalableAPP_AWS/issues/new?assignees=&labels=bug&template=bug_report.md&title=)** | **[❓ General Question](https://github.com/AnasGara/Project_ScalableAPP_AWS/issues/new?assignees=&labels=question&template=general_question.md&title=)**

**Status:** Architecture study with a static application and CloudFormation templates. Deployment has not been verified. Start with the corrected [quick start](QUICKSTART.md) for a local preview and known infrastructure blockers.

## Table of Content

- [Solution Overview](#solution-overview)
- [Architecture Diagram](#solution-architecture-diagram)
- [Key AWS Services](#key-aws-services)
- [Getting started](#getting-started)
- [Security Considerations](#security-considerations)
- [Monitoring and Alerts](#monitoring-and-alerts)
- [License](#license)

## Solution Overview

The intended architecture describes a web application on AWS using EC2 instances inside a properly architected VPC with public and private subnets across two Availability Zones. The architecture achieves high availability and scalability with Application Load Balancer (ALB), Auto Scaling Group (ASG), and CloudFront distribution for caching static assets. A Multi-AZ RDS instance serves as the database backend, with all compute resources placed in private subnets for enhanced security.

The design targets AWS Well-Architected Framework principles, ensuring security, reliability, performance efficiency, cost optimization, and operational excellence. By leveraging AWS Systems Manager Session Manager, the solution eliminates the need for bastion hosts, providing secure instance access without exposing SSH ports to the internet.

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

## Getting started

See [QUICKSTART.md](QUICKSTART.md) for a local static preview and an infrastructure
review checklist. The repository uses CloudFormation templates in `cloudformation/`;
the former CDK instructions referenced files that do not exist.

The service list above describes the intended architecture. It is not evidence of
a live deployment, measured availability, or completed security acceptance.

## Security Considerations

Security controls to verify before deployment:

- **Network Security**: All compute resources are deployed in private subnets
    
- **Application Security**: WAF rules protect against OWASP Top 10 vulnerabilities
    
- **Database Security**: RDS is isolated in private subnets with no public access
    
- **Access Control**: Security Groups and NACLs provide fine-grained traffic control
    
- **Instance Access**: Systems Manager Session Manager enables secure, audited instance access
    
- **Encryption**: Data in transit using TLS/SSL; data at rest using AWS managed or customer-managed KMS keys
    

## Monitoring and Alerts

Monitoring capabilities described by the architecture:

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

We welcome contributions! Please see our [Contributing Guidelines](CONTRIBUTING.md) for more information on how to submit pull requests, report bugs, or request features.
