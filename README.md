
# AWS Capstone Project – Workforce Manager

## 🚀 Project Title  
**Workforce Manager** – Multi-Region Employee Management Application with CI/CD, Route 53 Failover, and IaC (CloudFormation + Terraform)

## 🧾 1. Introduction

This project showcases a **multi-region, production-grade 3-tier web application** deployed on AWS using two infrastructure-as-code tools (CloudFormation and Terraform). It leverages:

- **Amazon EKS** for container orchestration
- **Amazon RDS (MySQL)** for data persistence
- **Amazon ECR** for image storage
- **Route 53** for DNS-based global traffic failover

The application includes CI/CD pipelines built with AWS CodePipeline and CodeBuild, with monitoring via CloudWatch and code quality checks using SonarQube.

---

## 📱 2. Application Overview

| Component | Tech Stack |
|----------|------------|
| Frontend | Angular (TypeScript) |
| Backend | Spring Boot (Java) |
| Database | Amazon RDS (MySQL) |
| Orchestration | Amazon EKS |
| IaC | CloudFormation (us-west-1), Terraform (us-west-2) |
| DNS | Route 53 |
| Monitoring | AWS CloudWatch |
| Code Quality | SonarQube |

---

## 🛠️ 3. Infrastructure Design Principles

| Goal             | Strategy |
|------------------|----------|
| High Availability | Multi-AZ, Auto-scaling node groups, Route 53 failover |
| Fault Tolerance  | Redundant infrastructure in two AWS regions |
| Scalability      | EKS Horizontal scaling via node groups |
| Disaster Recovery| Active-passive design using Route 53 |
| Automation       | CI/CD for frontend and backend deployments |
| Observability    | CloudWatch logs and alarms |

---

4. 🏗️ Overall Architecture
Architecture Diagram

 ┌────────────┐       ┌────────────┐       ┌──────────────┐       
│  GitHub    │ ───▶  │ CodeBuild  │ ───▶ │EKS/Kubernetes│ 
└────────────┘       └────────────┘       └──────────────┘       
     ▲                        │                   │                      
     └─────────────[Triggered on push]────────────┴────[Rolling updates] [webhook Trigers]

---

## 🌩️ 5. CloudFormation Deployment – Region 1 (us-west-1)

### 🔍 Key Resources:
- **Networking**: VPC, public/private subnets, route tables, IGW, NAT
- **Compute**: EKS Cluster + Node Groups
- **Database**: RDS MySQL (Multi-AZ)
- **Monitoring**: CloudWatch alarms + SNS alerts
- **Security**: IAM roles, Security Groups

### 🛠️ Deployment Options
- Manual: Upload CloudFormation stack from AWS Console
- Automated:
  - Push to GitHub
  - Trigger CodePipeline:
    - Source: GitHub
    - Deploy: CloudFormation Stack

---

## ⚙️ 6. Terraform Deployment – Region 2 (us-west-2)

### 🔧 Modules and Resources:
- `vpc`: Custom networking
- `eks`: Managed cluster + node groups
- `rds`: Standby RDS DB for failover

### 🛠️ Steps to Deploy

cd infra/terraform/
terraform init
terraform validate
terraform plan
terraform apply


7. Global Traffic Management (Route 53)
🧠 Failover Logic
Record Type	Region	Failover Role	Health Check
A (Alias)	us-east-1	PRIMARY	Enabled
A (Alias)	us-west-2	SECONDARY	N/A
If primary ALB is unhealthy, traffic fails over to secondary.

8. CI/CD Pipeline Setup
📦 Tools Used
Service	Purpose
GitHub	Source code repository
CodePipeline	Orchestrates CI/CD pipeline
CodeBuild	Builds & deploys Docker containers
Amazon ECR	Stores container images
Amazon EKS	Hosts the application in pods
Route 53	DNS failover
SonarQube	Code quality scanning
CloudWatch	Logs and metrics


🛠 CI/CD Setup Steps
Create CodePipeline with stages: Source (GitHub), Build (CodeBuild), Deploy (CodeBuild or CodeDeploy)
Store Docker image in ECR (if containerized)
Use IAM roles with EKS/CodeBuild permissions
Monitor via CloudWatch Logs for CPU Utilization etc.


10. Monitoring & Alerting
🔔 Alarms
Metric	Threshold	Action
RDS CPUUtilization	>75%	SNS Email
Alerts delivered via SNS Topic (email notification)

11. Security Best Practices
Private RDS: Not publicly accessible
IAM Roles: Least privilege principle
Ingress Rules: Restrict traffic using SGs and NACLs

📝 Useful Commands

aws eks update-kubeconfig --region <region> --name <cluster-name>
sudo apt install helm
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
kubectl create namespace ingress-nginx
helm install ingress-nginx ingress-nginx/ingress-nginx \
  --namespace ingress-nginx \
  --set controller.publishService.enabled=true
kubectl get svc ingress-nginx-controller -n ingress-nginx

