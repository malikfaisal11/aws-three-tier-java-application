# AWS Three-Tier Java Application Architecture

A cloud infrastructure project demonstrating the design and deployment of a Java web application using AWS three-tier architecture, with a focus on scalability, high availability, security, and operational reliability.

## Project Overview

This project explores deploying a Java web application on AWS using a three-tier architecture that separates the presentation, application, and database layers.

The architecture is designed to provide:
- High availability across multiple Availability Zones.
- Scalability through Auto Scaling Groups.
- Secure communication between infrastructure layers.
- Centralized monitoring and logging.
- Reliable database storage and recovery.

## Architecture

The infrastructure consists of three primary layers.

### 1. Presentation Tier

Responsible for handling incoming user requests and delivering application content.

Components:
- Nginx web servers.
- Network Load Balancer.
- Amazon CloudFront.
- EC2 Auto Scaling Group.

### 2. Application Tier

Responsible for application processing and business logic.

Components:
- Java application.
- Apache Tomcat.
- Internal Network Load Balancer.
- EC2 Auto Scaling Group.
- Amazon ElastiCache for session management.

### 3. Database Tier

Responsible for persistent application data storage.

Components:
- Amazon RDS MySQL.
- Multi-AZ database configuration.
- Automated backups.
- Read replicas.

## AWS Infrastructure and Networking

The proposed network architecture includes:

- Amazon VPC.
- Public and private subnets.
- Multiple Availability Zones.
- Internet Gateway.
- NAT Gateway.
- Transit Gateway for inter-VPC communication.
- Security Groups and IAM policies.

Two VPC networks are included in the architecture:

- Primary VPC: `192.168.0.0/16`
- Secondary VPC: `172.32.0.0/16`

## Technology Stack

| Category | Technologies |
|---|---|
| Cloud Platform | Amazon Web Services |
| Compute | Amazon EC2 |
| Networking | VPC, Subnets, Transit Gateway |
| Load Balancing | Network Load Balancer |
| Web Server | Nginx |
| Application Server | Apache Tomcat |
| Database | Amazon RDS MySQL |
| Caching | Amazon ElastiCache |
| CDN | Amazon CloudFront |
| Monitoring | Amazon CloudWatch |
| Security | IAM, Security Groups, AWS WAF |
| Build Tool | Maven |
| Programming | Java |
| Version Control | Git |

## Project Implementation Areas

### Infrastructure Setup
- VPC creation and network configuration.
- Public and private subnet design.
- Internet and NAT Gateway configuration.
- Security Group configuration.
- IAM roles and policies.

### Application Deployment
- Java application build using Maven.
- Apache Tomcat configuration.
- Application deployment and service management.
- Nginx reverse proxy configuration.

### Scalability and Availability
- EC2 Launch Templates.
- Auto Scaling Groups.
- Load Balancer integration.
- Multi-AZ infrastructure design.

### Monitoring and Maintenance
- CloudWatch metrics.
- Application log collection.
- Infrastructure monitoring.
- Troubleshooting connectivity and performance issues.

### Security
- Network access restrictions.
- IAM-based permissions.
- Encryption considerations.
- AWS security services.
- Backup and recovery strategies.

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

## Prerequisites

- AWS account.
- AWS CLI v2.
- Java Development Kit.
- Apache Maven.
- Git.
- Basic understanding of AWS networking and infrastructure.

## Getting Started

Clone the repository:

```bash
git clone https://github.com/malikfaisal11/aws-three-tier-java-application.git
```

Navigate to the project:

```bash
cd aws-three-tier-java-application
```

Explore the application and infrastructure directories to review the source code and deployment configurations.

## Learning Outcomes

This project provides practical exposure to:

- Designing AWS three-tier architecture.
- Understanding cloud networking and VPC communication.
- Deploying Java applications using Apache Tomcat.
- Configuring load balancing and Auto Scaling.
- Implementing cloud monitoring and security practices.
- Understanding highly available cloud infrastructure.

## Author

**Faisal Malik**

DevOps Engineer | Jenkins | CI/CD | AWS | Docker

GitHub: [malikfaisal11](https://github.com/malikfaisal11)

Portfolio: [DevOps-Portfolio](https://github.com/malikfaisal11/DevOps-Portfolio)
