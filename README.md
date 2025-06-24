# 🌐 AWS Capstone Project – Workforce Manager

## 🚀 Project Title  
**Workforce Manager** – Multi-Region Employee Management Application with CI/CD, Route 53 Failover, and IaC (CloudFormation + Terraform)

---

## 🧾 1. Introduction

This project showcases a **multi-region, production-grade 3-tier web application** deployed on AWS using two infrastructure-as-code tools (CloudFormation and Terraform). It leverages:

- **Amazon EKS** – Container orchestration
- **Amazon RDS (MySQL)** – Data persistence
- **Amazon ECR** – Docker image storage
- **Route 53** – DNS-based global traffic failover

It includes automated CI/CD pipelines using AWS CodePipeline and CodeBuild, with monitoring via CloudWatch and code quality analysis using SonarQube.

---

## 📱 2. Application Overview

| Component   | Tech Stack               |
|------------|---------------------------|
| Frontend   | Angular (TypeScript)      |
| Backend    | Spring Boot (Java)        |
| Database   | Amazon RDS (MySQL)        |
| Orchestration | Amazon EKS             |
| IaC Tools  | CloudFormation (us-west-1), Terraform (us-west-2) |
| DNS        | Route 53                  |
| Monitoring | AWS CloudWatch            |
| Code Quality | SonarQube               |

---

## 🛠️ 3. Infrastructure Design Principles

| Goal               | Strategy                                            |
|--------------------|-----------------------------------------------------|
| High Availability  | Multi-AZ, Auto-scaling node groups, Route 53 failover |
| Fault Tolerance    | Redundant infrastructure across two AWS regions    |
| Scalability        | Horizontal scaling via EKS node groups             |
| Disaster Recovery  | Active-passive design with Route 53 failover       |
| Automation         | CI/CD pipelines for both frontend and backend      |
| Observability      | Logs and alarms via CloudWatch                     |

---

## 🏗️ 4. Overall Architecture

┌────────────┐ ┌────────────┐ ┌──────────────┐
│ GitHub │ ───▶ │ CodeBuild │ ───▶ │ EKS (Pods) │
└────────────┘ └────────────┘ └──────────────┘
▲ │ │
└───── Trigger on Push ─┴── Apply YAML ───────┘

- Source control via GitHub  
- Continuous integration via CodeBuild  
- Deployment to EKS using `kubectl` and Helm  

---

## 🌩️ 5. CloudFormation Deployment – Region 1 (`us-west-1`)

### 🔍 Key Resources

- **Networking**: VPC, public/private subnets, route tables, IGW, NAT
- **Compute**: Amazon EKS cluster and node groups
- **Database**: RDS MySQL (Multi-AZ) in private subnets
- **Monitoring**: CloudWatch alarms and SNS notifications
- **Security**: IAM roles, Security Groups

### 🛠️ Deployment Options

- **Manual**: Upload and deploy CloudFormation stack via AWS Console  
- **Automated**:
  - Push templates to GitHub  
  - Setup AWS CodePipeline:
    - Source: GitHub  
    - Deploy: CloudFormation Stack  

---

## ⚙️ 6. Terraform Deployment – Region 2 (`us-west-2`)

### 🔧 Key Modules and Resources

- `vpc`: VPC, subnets, and routing
- `eks`: EKS cluster and node groups
- `rds`: MySQL RDS (standby)

### 🛠️ Deployment Commands

cd infra/terraform/
terraform init
terraform validate
terraform plan
terraform apply


🌐 7. Global Traffic Management (Route 53)
🧠 Failover Logic
Record Type	Region	Role	Health Check
A (Alias)	us-west-1	PRIMARY	Enabled
A (Alias)	us-west-2	SECONDARY	N/A

If the primary ALB in us-west-1 becomes unhealthy, traffic automatically fails over to us-west-2.

🔁 8. CI/CD Pipeline Setup
📦 Tools Used
Service	Purpose
GitHub	Source code repository
CodePipeline	Orchestrates CI/CD process
CodeBuild	Builds and deploys Docker images
Amazon ECR	Stores containerized application
Amazon EKS	Runs application workloads
Route 53	DNS failover and routing
SonarQube	Code quality analysis
CloudWatch	Logs and performance monitoring

🛠️ Setup Instructions
Create CodePipeline with:

Source Stage: GitHub

Build Stage: CodeBuild (includes SonarQube scan and kubectl deploy)

Deploy Stage: Kubernetes via CodeBuild or CodeDeploy

Push Docker images to Amazon ECR

Use IAM roles with scoped EKS/CodeBuild access

Monitor build logs and metrics in CloudWatch

📊 9. Monitoring & Alerting
🔔 CloudWatch Alarms + SNS
Metric	Threshold	Action
RDS CPUUtilization	> 75%	SNS Email
Pod CrashLoop	Detected	Notification

Logs from EKS applications and node metrics are streamed to CloudWatch.

Alarms trigger email alerts via configured SNS topics.

🔐 10. Security Best Practices
✅ Private RDS – Database not publicly accessible

✅ IAM Roles – Scoped permissions for EKS, CI/CD, and DB access

✅ Security Groups & NACLs – Strict inbound/outbound traffic control

✅ Ingress Rules – Restrict application access

✅ Secrets Management – Use AWS Secrets Manager or Kubernetes Secrets

🧰 11. Useful Commands

# Connect kubectl to EKS
aws eks update-kubeconfig --region <region> --name <cluster-name>

# Install Helm
sudo apt install helm

# Add Ingress NGINX Helm repo
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

# Create namespace and deploy ingress controller
kubectl create namespace ingress-nginx
helm install ingress-nginx ingress-nginx/ingress-nginx \
  --namespace ingress-nginx \
  --set controller.publishService.enabled=true

# Check services and logs
kubectl get svc ingress-nginx-controller -n ingress-nginx
