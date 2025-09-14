## DevOps Task: CI/CD Pipeline for Node.js App on AWS ECS

This project demonstrates a complete CI/CD pipeline for deploying a containerized Node.js application to AWS ECS using Jenkins, DockerHub, and CloudWatch logging. It automates the build, push, and deployment process with robust monitoring and role-based access.

---

## Tech Stack

- **Frontend/Backend**: Node.js  
- **CI/CD**: Jenkins  
- **Containerization**: Docker  
- **Image Registry**: DockerHub  
- **Cloud Provider**: AWS (ECS Fargate, IAM, CloudWatch)  
- **Version Control**: GitHub  

---

## Pipeline Workflow

1. **Code Push**: Developer pushes code to GitHub  
2. **Jenkins Trigger**: GitHub webhook triggers Jenkins pipeline  
3. **Build & Dockerize**: Jenkins builds Docker image from `Dockerfile`  
4. **Push to DockerHub**: Image is pushed to DockerHub  
5. **Deploy to ECS**: Jenkins triggers ECS service update using AWS CLI  
6. **Logging**: ECS task logs are streamed to CloudWatch  

---

## Project Structure

DevOps_Task/ 
├── Dockerfile 
├── Jenkinsfile 
├── app.js 
├── package.json 
└── README.md

---

## CI/CD Pipeline Setup Guide

---

## Source Code & Version Control

# Fork & Clone 
1. Fork or Clone this repo to your GitHub account and clone it locally.

# Branching Strategy
1. **main**: Stable production-ready code
2. **dev**: Active development and feature testing
3. Use Pull Requests to merge **dev** → **main** with code reviews

---

## Set Up Jenkins

# Launch an EC2 instance (Ubuntu) and install Jenkins
```bash
sudo wget -O /etc/yum.repos.d/jenkins.repo \
    https://pkg.jenkins.io/redhat/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat/jenkins.io-2023.key
sudo yum upgrade
# Add required dependencies for the jenkins package
sudo yum install fontconfig java-21-openjdk
sudo yum install jenkins
sudo systemctl enable jenkins
sudo systemctl start jenkins
sudo systemctl status jenkins
```

# Access Jenkins at http://<your-ec2-ip>:8080 Unlock it using:
```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

# Install plugins:
1. GitHub Integration
2. Docker Pipeline
3. AWS CLI
4. Pipeline

---

## Create Jenkins Pipeline Job

# Create a Pipeline Job in Jenkins
# Add GitHub repo URL and set up webhook:
1. Go to GitHub → Settings → Webhooks → Add webhook
2. Payload URL: http://<jenkins-ip>:8080/github-webhook/
3. Content type: application/json
4. Trigger: push

# Jenkinsfile (Declarative Pipeline)
1. You check File in My Repo
2. Add a Jenkinsfile to your repo root

---

## AWS ECS Setup

# ECS Setup
1. Go to AWS Console → ECS → Create Cluster (Fargate preferred)

# Create a Task Definition:
1. Use DockerHub image
2. Set CPU, memory, and port mappings
3. Enable CloudWatch logging

# Create ECS Service:
1. Attach to cluster
2. Use ALB (Application Load Balancer) for traffic
3. Set desired task count (e.g., 2)

---

## Monitoring & Logging

# CloudWatch Setup
1. Enable logging in ECS Task Definition

# Viewing Logs & Metrics
1. Go to AWS CloudWatch → Log Groups → /ecs/logo-server
2. View CPU, memory, and network metrics under ECS → Cluster → Service → Metrics
