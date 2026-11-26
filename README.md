# AWS Three-Tier Java Application Architecture

A cloud infrastructure project focused on designing and deploying a Java web application using AWS three-tier architecture. The project explores infrastructure provisioning, application deployment, networking, scalability, security, monitoring, and operational practices.

## Table of Contents

1. [Project Overview](#project-overview)
2. [Architecture Overview](#architecture-overview)
3. [Prerequisites](#prerequisites)
4. [Infrastructure Setup](#infrastructure-setup)
5. [Application Setup](#application-setup)
6. [Load Balancing and Auto Scaling](#load-balancing-and-auto-scaling)
7. [Monitoring and Maintenance](#monitoring-and-maintenance)
8. [Security Best Practices](#security-best-practices)
9. [Troubleshooting](#troubleshooting)
10. [Repository Structure](#repository-structure)
11. [Learning Outcomes](#learning-outcomes)
12. [Author](#author)

---

## Project Overview

### Introduction

This project demonstrates the design and deployment approach for a Java web application using AWS three-tier architecture.

The architecture separates the application into three independent layers: presentation, application processing, and database storage.

This separation improves maintainability and provides a foundation for scalability, availability, and security.

### Key Features

- **High Availability:** Multi-AZ infrastructure design and database failover capabilities.
- **Scalability:** EC2 Auto Scaling Groups for dynamic resource management.
- **Security:** Network isolation, IAM policies, and security group configurations.
- **Monitoring:** CloudWatch metrics, application logs, and infrastructure monitoring.
- **Database Reliability:** Amazon RDS with backup and recovery capabilities.
- **Application Delivery:** Load balancing and reverse proxy configuration.

## Architecture Overview

### AWS Three-Tier Architecture

![AWS Architecture](https://imgur.com/b9iHwVc.png)

The architecture consists of three primary layers.

### 1. Presentation Tier (Frontend)

Responsible for receiving and handling incoming user requests.

Components:
- Nginx web servers.
- Public-facing Network Load Balancer.
- Amazon CloudFront distribution.
- EC2 Auto Scaling Group.

### 2. Application Tier (Backend)

Responsible for processing application requests and executing business logic.

Components:
- Java application.
- Apache Tomcat application servers.
- Internal Network Load Balancer.
- EC2 Auto Scaling Group.
- Amazon ElastiCache for session management.

### 3. Data Tier

Responsible for persistent application data storage and management.

Components:
- Amazon RDS MySQL.
- Multi-AZ database configuration.
- Automated database backups.
- Read replicas for read-heavy workloads.

### Network Architecture

The proposed network design includes:

- Two separate VPCs.
- Public and private subnets.
- Multiple Availability Zones.
- Internet Gateway.
- NAT Gateway.
- AWS Transit Gateway for inter-VPC communication.

VPC CIDR ranges:

| VPC | CIDR |
|---|---|
| Primary VPC | 192.168.0.0/16 |
| Secondary VPC | 172.32.0.0/16 |

### Architecture Diagram

![Three-Tier Architecture Diagram](https://imgur.com/3XF0tlJ.png)

## Prerequisites

### AWS Account and CLI

An AWS account is required to work with the infrastructure components.

Install AWS CLI v2 on Linux:

```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```

Configure AWS CLI:

```bash
aws configure
```

### Development Tools

Required tools:

- Git
- Java Development Kit
- Apache Maven
- AWS CLI v2

Install Git on Ubuntu:

```bash
sudo apt-get update
sudo apt-get install git -y
```

### CI/CD and Artifact Management

The original project documentation also includes integration with:

**SonarCloud**
- Code quality analysis.
- Project configuration.
- Authentication token management.

**JFrog Artifactory**
- Maven artifact repository.
- Repository configuration.
- Secure credential management.

These integrations should be configured only when implementing the corresponding CI/CD workflow.

## Infrastructure Setup

### 1. VPC Creation

Create the primary VPC:

```bash
aws ec2 create-vpc \
  --cidr-block 192.168.0.0/16 \
  --tag-specifications 'ResourceType=vpc,Tags=[{Key=Name,Value=PrimaryVPC}]' \
  --region us-east-1
```

Create the secondary VPC:

```bash
aws ec2 create-vpc \
  --cidr-block 172.32.0.0/16 \
  --tag-specifications 'ResourceType=vpc,Tags=[{Key=Name,Value=SecondaryVPC}]' \
  --region us-east-1
```

### 2. Subnet Configuration

Create a public subnet:

```bash
aws ec2 create-subnet \
  --vpc-id vpc-xxx \
  --cidr-block 192.168.1.0/24 \
  --availability-zone us-east-1a
```

Create a private subnet:

```bash
aws ec2 create-subnet \
  --vpc-id vpc-xxx \
  --cidr-block 192.168.2.0/24 \
  --availability-zone us-east-1b
```

### 3. Gateway Configuration

Create an Internet Gateway:

```bash
aws ec2 create-internet-gateway
```

Attach it to the VPC:

```bash
aws ec2 attach-internet-gateway \
  --vpc-id vpc-xxx \
  --internet-gateway-id igw-xxx
```

Create a NAT Gateway:

```bash
aws ec2 create-nat-gateway \
  --subnet-id subnet-xxx \
  --allocation-id eipalloc-xxx
```

### 4. Security Configuration

Create a frontend security group:

```bash
aws ec2 create-security-group \
  --group-name FrontendSG \
  --description "Security group for frontend servers" \
  --vpc-id vpc-xxx
```

Allow inbound HTTP traffic:

```bash
aws ec2 authorize-security-group-ingress \
  --group-id sg-xxx \
  --protocol tcp \
  --port 80 \
  --cidr 0.0.0.0/0
```

Allow inbound HTTPS traffic:

```bash
aws ec2 authorize-security-group-ingress \
  --group-id sg-xxx \
  --protocol tcp \
  --port 443 \
  --cidr 0.0.0.0/0
```

Production deployments should restrict access according to the intended network architecture and security requirements.

### 5. IAM Roles and Policies

IAM policies define permissions for AWS services and application components.

Example S3 access policy:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Resource": "arn:aws:s3:::your-bucket/*"
    }
  ]
}
```

Use least-privilege permissions and IAM roles instead of embedding long-term AWS credentials in application code.

### 6. Database Layer

The architecture uses Amazon RDS MySQL.

Example RDS creation command:

```bash
aws rds create-db-instance \
  --db-instance-identifier prod-mysql \
  --db-instance-class db.t3.medium \
  --engine mysql \
  --master-username admin \
  --allocated-storage 20 \
  --multi-az \
  --vpc-security-group-ids sg-xxx \
  --db-subnet-group-name your-db-subnet-group
```

Configure the database password securely through an approved secrets-management mechanism.

Example database initialization:

```sql
CREATE DATABASE javaapp;

USE javaapp;

CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50) NOT NULL UNIQUE,
    password VARCHAR(255) NOT NULL,
    email VARCHAR(100) NOT NULL UNIQUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_username ON users(username);
CREATE INDEX idx_email ON users(email);
```

## Application Setup

### 1. Maven Configuration

The original application documentation references Maven-based Java application development.

Example build commands:

```bash
mvn clean package -DskipTests
```

Run tests:

```bash
mvn test
```

Deploy artifacts to a configured Maven repository:

```bash
mvn deploy
```

### 2. Application Deployment with Tomcat

The application tier uses Apache Tomcat to host Java applications.

Example systemd service configuration:

```ini
[Unit]
Description=Apache Tomcat Web Application Container
After=network.target

[Service]
Type=forking
Environment=JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
Environment=CATALINA_HOME=/opt/tomcat
Environment=CATALINA_BASE=/opt/tomcat

ExecStart=/opt/tomcat/bin/startup.sh
ExecStop=/opt/tomcat/bin/shutdown.sh

User=tomcat
Group=tomcat
Restart=always

[Install]
WantedBy=multi-user.target
```

### 3. Nginx Configuration

Nginx acts as a reverse proxy in the presentation tier.

Example configuration:

```nginx
upstream backend {
    server internal-nlb-xxx.elb.amazonaws.com:8080;
}

server {
    listen 80;
    server_name example.com;

    location / {
        proxy_pass http://backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }

    location /static/ {
        proxy_pass https://your-cloudfront-distribution.cloudfront.net;
    }
}
```

## Load Balancing and Auto Scaling

### 1. Launch Template

Launch Templates define the EC2 configuration used by Auto Scaling Groups.

Example:

```bash
aws ec2 create-launch-template \
  --launch-template-name WebServerTemplate \
  --version-description WebServerVersion1 \
  --launch-template-data '{
    "ImageId": "ami-xxx",
    "InstanceType": "t3.micro",
    "SecurityGroupIds": ["sg-xxx"]
  }'
```

### 2. Auto Scaling Group

Example:

```bash
aws autoscaling create-auto-scaling-group \
  --auto-scaling-group-name WebServerASG \
  --launch-template LaunchTemplateName=WebServerTemplate,Version='$Latest' \
  --min-size 2 \
  --max-size 6 \
  --desired-capacity 2 \
  --vpc-zone-identifier "subnet-xxx,subnet-yyy" \
  --health-check-type ELB \
  --health-check-grace-period 300
```

The Auto Scaling Group manages EC2 capacity according to the configured scaling requirements.

## Monitoring and Maintenance

### 1. Amazon CloudWatch

CloudWatch provides infrastructure metrics and monitoring capabilities.

The original project includes an example for collecting custom memory utilization metrics.

Example:

```bash
MEMORY_USAGE=$(free | grep Mem | awk '{print $3/$2 * 100.0}')

aws cloudwatch put-metric-data \
  --metric-name MemoryUsage \
  --namespace CustomMetrics \
  --value "$MEMORY_USAGE"
```

### 2. Log Management

The CloudWatch Agent can collect application and system logs.

Example log configuration:

```json
{
  "agent": {
    "metrics_collection_interval": 60,
    "run_as_user": "root"
  },
  "logs": {
    "logs_collected": {
      "files": {
        "collect_list": [
          {
            "file_path": "/opt/tomcat/logs/catalina.out",
            "log_group_name": "/aws/tomcat/application",
            "log_stream_name": "{instance_id}",
            "timezone": "UTC"
          }
        ]
      }
    }
  }
}
```

## Security Best Practices

### Network Security
- Implement network ACLs.
- Configure security groups with least-privilege access.
- Enable VPC Flow Logs.
- Configure AWS WAF where applicable.

### Application Security
- Apply regular security patches.
- Use AWS Secrets Manager for sensitive information.
- Consider AWS Shield for DDoS protection.
- Enable Amazon GuardDuty for threat detection.

### Data Security
- Enable encryption at rest.
- Use SSL/TLS for data in transit.
- Implement database backup strategies.
- Conduct regular security reviews.

## Troubleshooting

### 1. Connectivity Issues

Check database connectivity:

```bash
telnet database-endpoint 3306
```

Verify security group configuration:

```bash
aws ec2 describe-security-groups --group-ids sg-xxx
```

Check load balancer target health:

```bash
aws elbv2 describe-target-health \
  --target-group-arn arn:aws:elasticloadbalancing:region:account-id:targetgroup/your-target-group/xxx
```

### 2. Performance Issues

Check CPU utilization:

```bash
top -bn1
```

Check memory usage:

```bash
free -m
```

Check disk usage:

```bash
df -h
```

Check Java process threads:

```bash
ps -eLf | grep java | wc -l
```

## Repository Structure

```text
aws-three-tier-java-application/
│
├── Java-Login-App/
│   └── Java application source code
│
├── infrastructure/
│   └── AWS infrastructure configuration
│
└── README.md
```

## Learning Outcomes

This project provides practical exposure to:

- AWS three-tier architecture design.
- VPC and subnet configuration.
- AWS networking and inter-VPC communication.
- Java application deployment.
- Apache Tomcat and Nginx configuration.
- Load balancing and Auto Scaling.
- Amazon RDS database configuration.
- CloudWatch monitoring and logging.
- AWS security and operational best practices.

## Author

**Faisal Malik**

DevOps Engineer | Jenkins | CI/CD | AWS | Docker

GitHub: [malikfaisal11](https://github.com/malikfaisal11)

Portfolio: [DevOps-Portfolio](https://github.com/malikfaisal11/DevOps-Portfolio)

---

## Project Documentation Note

This repository contains application source code and infrastructure-related project materials. The AWS commands and configuration examples in this documentation serve as implementation references.

Actual deployment status depends on the infrastructure resources provisioned and verified in the AWS environment. Replace example resource IDs, endpoints, and configuration values before executing commands.
