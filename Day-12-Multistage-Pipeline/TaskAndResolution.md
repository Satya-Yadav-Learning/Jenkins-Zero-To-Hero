# 📄 TaskAndResolution.md

# 🚀 Jenkins Pipeline Deployment using Jenkins Agent (Real DevOps Scenario)

---

# 📌 Project Overview

The development team wanted to automate deployment of a static website using Jenkins Pipeline.

The solution included:

- Updating code in Git repository
- Configuring Jenkins Agent on Application Server
- Deploying application using Jenkins Pipeline
- Validating deployment using automated testing
- Serving application through Load Balancer

---

# 🏗️ Architecture Diagram

```text
Developer → Git Repository
        ↓
Jenkins Controller
        ↓
Jenkins Agent (Application Server)
        ↓
Apache Document Root (/var/www/html)
        ↓
Load Balancer
        ↓
End Users
```

---

# ⚙️ Step 1 — Update Website Content

## 🔹 Login to Application Server

```bash
ssh appuser@app-server
```

### Output

```text
Last login: Tue Apr 15 10:00:00 UTC
[appuser@app-server ~]$
```

---

## 🔹 Navigate to Repository

```bash
cd /var/www/html
```

---

## 🔹 Verify Existing Content

```bash
cat index.html
```

### Output

```text
Welcome to Website
```

---

## 🔹 Update Website Content

```bash
echo "Welcome to Enterprise Web Platform" > index.html
```

---

## 🔹 Verify Updated Content

```bash
cat index.html
```

### Output

```text
Welcome to Enterprise Web Platform
```

---

# ⚙️ Step 2 — Push Changes to Git Repository

## 🔹 Check Git Status

```bash
git status
```

### Output

```text
On branch master
Changes not staged for commit:
modified: index.html
```

---

## 🔹 Add File

```bash
git add index.html
```

---

## 🔹 Commit Changes

```bash
git commit -m "Updated website content"
```

### Output

```text
[master abc1234] Updated website content
1 file changed, 1 insertion(+), 1 deletion(-)
```

---

## 🔹 Push to Origin

```bash
git push origin master
```

### Output

```text
Enumerating objects: 5, done.
Writing objects: 100%
To http://git-server/repository.git
```

---

# ⚙️ Step 3 — Install Java on Application Server

## 🔹 Verify Java

```bash
java -version
```

### Output

```text
openjdk version "17"
```

---

## 🔹 Install Java if Missing

```bash
sudo yum install -y java-17-openjdk
```

### Output

```text
Installed:
java-17-openjdk
Complete!
```

---

# ⚙️ Step 4 — Create Jenkins Agent Directory

```bash
mkdir -p /home/appuser/jenkins_agent
```

### Output

```text
(no output = success)
```

---

# ⚙️ Step 5 — Configure Jenkins Agent Node

## 🔹 Jenkins Node Configuration

| Parameter | Value |
|---|---|
| Node Name | Application Server |
| Label | app-node |
| Remote Root Directory | /home/appuser/jenkins_agent |
| Launch Method | Launch agents via SSH |
| Host | app-server |

---

## 🔹 Credentials Added

| Username | Password |
|---|---|
| appuser | ******** |

---

## 🔹 Expected Node Status

```text
Connected
```

---

# ⚙️ Step 6 — Install Required Jenkins Plugins

## 🔹 Plugins Installed

| Plugin |
|---|
| Pipeline |
| SSH Build Agents |

---

# ⚙️ Step 7 — Create Jenkins Pipeline Job

## 🔹 Job Details

| Parameter | Value |
|---|---|
| Job Name | deploy-job |
| Job Type | Pipeline |

---

# ⚙️ Step 8 — Configure Jenkins Pipeline

## 🔹 Pipeline Script

```groovy
pipeline {
    agent {
        label 'app-node'
    }

    stages {

        stage('Deploy') {
            steps {
                sh '''
                cd /var/www/html

                echo "Deploying latest code..."

                git pull origin master
                '''
            }
        }

        stage('Test') {
            steps {
                sh '''
                echo "Testing application..."

                curl -f http://load-balancer-url
                '''
            }
        }
    }
}
```

---

# ⚙️ Step 9 — Build Pipeline

## 🔹 Trigger Build

```text
Build Now → deploy-job
```

---

## 🔹 Console Output

```text
[Pipeline] stage
[Pipeline] { (Deploy)

Deploying latest code...

Already up to date.

[Pipeline] stage
[Pipeline] { (Test)

Testing application...

HTTP/1.1 200 OK

Finished: SUCCESS
```

---

# 🌐 Step 10 — Validate Application

## 🔹 Open URL

```text
http://load-balancer-url
```

---

## 🔹 Expected Output

```text
Welcome to Enterprise Web Platform
```

---

# 🧠 Core DevOps Concepts Used

## 🔹 Jenkins Controller vs Agent

| Controller | Agent |
|---|---|
| Orchestrates jobs | Executes jobs |
| Central management | Distributed execution |

---

## 🔹 Why Jenkins Agent?

- Distributed builds
- Better scalability
- Reduced load on controller

---

## 🔹 Why Pipeline?

- Pipeline as Code
- Version controlled CI/CD
- Better automation

---

## 🔹 Why curl in Test Stage?

```bash
curl -f http://load-balancer-url
```

Purpose:
- Validate website availability
- Fail pipeline automatically if app is inaccessible

---

# 🚨 Common Issues and Resolutions

| Issue | Root Cause | Resolution |
|---|---|---|
| Agent Offline | Java missing | Install Java |
| SSH Failure | Wrong credentials | Update credentials |
| Pipeline Option Missing | Plugin missing | Install Pipeline Plugin |
| curl failed | Apache down | Restart Apache |
| Permission denied | Git ownership issue | Fix permissions |

---

# 🎯 CI/CD Pipeline Interview Questions (L1 → L4)

# 🟢 L1 — Junior Level

## Q1. What is CI/CD?

### Answer
CI/CD stands for:
- Continuous Integration
- Continuous Deployment

Purpose:
- Automate software delivery process

---

## Q2. What is Jenkins?

### Answer
Jenkins is an automation server used for:
- Build automation
- Testing
- Deployment

---

## Q3. What is a Jenkins Pipeline?

### Answer
Pipeline is:
- Pipeline as Code
- Written in Groovy syntax
- Automates stages like build/test/deploy

---

# 🟡 L2 — Intermediate Level

## Q4. Why use Jenkins Agents?

### Answer
Agents help:
- Distribute workloads
- Run builds on remote servers
- Scale Jenkins infrastructure

---

## Q5. Why use labels in Jenkins?

### Answer

```groovy
label 'app-node'
```

Purpose:
- Run pipeline on specific node

---

## Q6. Why use curl in testing?

### Answer

```bash
curl -f http://load-balancer-url
```

Benefits:
- Validates application availability
- Fails pipeline automatically

---

# 🔵 L3 — Senior Level

## Q7. How would you improve this pipeline?

### Answer
- Add rollback strategy
- Add artifact repository
- Add monitoring
- Add automated testing

---

## Q8. How to secure Jenkins Agents?

### Answer
- SSH key authentication
- Restricted users
- Separate networks
- RBAC

---

## Q9. Difference between Freestyle and Pipeline?

| Freestyle | Pipeline |
|---|---|
| UI based | Code based |
| Hard to scale | Scalable |
| Hard to version | Git versioned |

---

# 🔴 L4 — Architect Level

## Q10. How would you design enterprise-grade CI/CD?

### Answer
Architecture:
- Jenkins HA
- Kubernetes Agents
- Artifact Repository
- GitOps
- Monitoring stack
- Canary deployment

---

## Q11. How to achieve zero downtime deployment?

### Answer
Strategies:
- Blue-Green deployment
- Canary deployment
- Rolling updates

---

## Q12. How to scale Jenkins in cloud?

### Answer
- Kubernetes dynamic agents
- Auto-scaling nodes
- Distributed executors

---

# ☁️ AWS Mapping

| Current Setup | AWS Equivalent |
|---|---|
| Jenkins | AWS CodePipeline |
| Jenkins Agent | EC2 Build Agent |
| Git Repository | CodeCommit |
| Apache Server | EC2 |
| Load Balancer | ALB |
| curl validation | Route53 Health Check |

---

# ☁️ AWS Scenario-Based Questions

## Q1. How would you replace Jenkins with AWS Native Services?

### Answer

| Jenkins Function | AWS Service |
|---|---|
| Pipeline | CodePipeline |
| Build | CodeBuild |
| Deployment | CodeDeploy |

---

## Q2. How to deploy static website in AWS?

### Answer
Options:
- S3 Static Website Hosting
- CloudFront CDN
- Route53

---

## Q3. How to secure AWS CI/CD?

### Answer
- IAM Roles
- KMS encryption
- Private VPC
- Secrets Manager

---

# 🚨 Real AWS Incident RCA

# Incident: Deployment Success but Application Down

## Problem

Pipeline completed successfully but:
- Application unavailable
- Load Balancer returning 503

---

## Root Cause

Apache service failed after deployment.

---

## Impact

- Users unable to access website
- Production outage

---

## Resolution

```bash
sudo systemctl restart httpd
```

---

## Preventive Actions

- Health checks
- Auto rollback
- Monitoring alerts

---

# 🐳 Docker Mapping

## Dockerfile Equivalent

```dockerfile
FROM httpd:latest

COPY . /usr/local/apache2/htdocs/
```

---

## Build Docker Image

```bash
docker build -t website .
```

---

## Run Container

```bash
docker run -d -p 80:80 website
```

---

# 🔁 GitHub Actions Equivalent

```yaml
name: Deploy Website

on:
  push:
    branches:
      - master

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:

      - name: Deploy
        run: |
          ssh appuser@app-server "
          cd /var/www/html &&
          git pull origin master
          "

      - name: Test
        run: |
          curl -f http://load-balancer-url
```

---

# 🧪 Full DevOps Mock Interview

## Q1. Explain your project.

### Answer
Implemented:
- Jenkins Pipeline
- Agent-based deployment
- Git integration
- Automated testing
- Load balancer validation

---

## Q2. Biggest challenge faced?

### Answer
Pipeline plugin missing and Jenkins Agent configuration issues.

---

## Q3. Why use Jenkins Agents?

### Answer
To distribute builds and reduce controller load.

---

## Q4. Why use automated validation?

### Answer
To ensure application availability immediately after deployment.

---

## Q5. How would you improve this architecture?

### Answer
- Kubernetes deployment
- Monitoring
- Auto rollback
- Infrastructure as Code

---

# 📌 Final Outcome

✔ Jenkins Agent Configured  
✔ Jenkins Pipeline Created  
✔ Git Integrated Deployment  
✔ Automated Application Validation  
✔ Production-like CI/CD Workflow Implemented  

---

# 🏁 Final Summary

Successfully implemented:

- Jenkins Pipeline CI/CD
- Distributed Jenkins Agent
- Git-based deployment
- Automated validation testing
- Load Balancer accessibility testing
- Real-world DevOps workflow
