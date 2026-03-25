# 🚀 Jenkins CI/CD Deployment using SSH Agent (Task & Resolution)

---

## 📌 Task Overview

Configured a Jenkins pipeline to:

- Connect to an application server using SSH  
- Configure it as a Jenkins agent  
- Pull code from a Gitea repository  
- Deploy it to Apache (`/var/www/html`)  
- Make application accessible via Load Balancer  

---

## 🏗️ Architecture

Gitea Repo → Jenkins → SSH Agent (App Server) → Apache → Load Balancer  

---

## ⚙️ Step-by-Step Implementation

### 🔹 Step 1: Install Required Plugins

Manage Jenkins → Manage Plugins → Available  

Install:
- Pipeline  
- SSH Build Agents  
- Git Plugin  
- SSH Agent Plugin (optional)  

Restart Jenkins after installation  

---

### 🔹 Step 2: Setup SSH Authentication

#### On Jenkins Server

```bash
ssh jenkins@jenkins
ssh-keygen -t ed25519
cat ~/.ssh/id_ed25519.pub
```

#### On App Server

```bash
ssh user@app-server
mkdir -p ~/.ssh
vi ~/.ssh/authorized_keys
```

Paste public key  

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

#### Verify SSH

```bash
ssh user@app-server
```

(No password prompt expected)

---

### 🔹 Step 3: Add Jenkins Agent

Manage Jenkins → Manage Nodes → New Node  

Configuration:

- Name: App Server 1  
- Type: Permanent Agent  
- Remote Root: /home/user/jenkins_agent  
- Label: stapp01  
- Launch method: Launch via SSH  

SSH:
- Host: app-server-hostname  
- Username: user  
- Private key: Jenkins private key  

---

### 🔹 Step 4: Verify Agent

Manage Jenkins → Nodes  

✔ Status should be **Connected**

---

### 🔹 Step 5: Verify Application Directory

```bash
ssh user@app-server
cd /var/www/html
ls -a
```

Expected:

```
.git
index.html
```

---

### 🔹 Step 6: Create Pipeline Job

New Item → Pipeline  

Name:  
xfusion-webapp-job  

---

### 🔹 Step 7: Pipeline Script

```groovy
pipeline {
    agent { label 'stapp01' }

    stages {
        stage('Deploy') {
            steps {
                sh '''
                cd /var/www/html
                git pull origin master
                '''
            }
        }
    }
}
```

---

### 🔹 Step 8: Run Job

Click **Build Now**

---

## 📊 Output

```
Running on App Server 1
+ cd /var/www/html
+ git pull origin master
Already up to date.
Finished: SUCCESS
```

---

## 🌐 Final Verification

Open:

https://<LBR-URL>

✔ Application loads  
✔ No `/web_app` in URL  

---

## 🧠 Key Concepts

- Jenkins Controller vs Agent  
- SSH-based deployment  
- Git-based deployment  
- Apache hosting  
- CI/CD fundamentals  

---

## ❗ Troubleshooting

### Java Error

UnsupportedClassVersionError  

Fix:

```bash
sudo yum install -y java-17-openjdk
```

---

### Agent Offline

- Check SSH connectivity  
- Verify credentials  

---

### Git Issues

```bash
git branch -a
```

Use correct branch (`master`)

---

## 🎯 Deployment Model

Agent-Based Direct Deployment  

---

# 🔥 Scenario-Based Interview Q&A

---

## 🟢 L1 (Junior)

Q: What does this pipeline do?  
A: Pulls latest code from Git and deploys to Apache  

Q: Why SSH?  
A: To execute remote commands securely  

---

## 🟡 L2 (Mid-Level)

Q: Why Jenkins agent?  
A: To run jobs on remote systems and improve scalability  

Q: What is label?  
A: It maps jobs to specific nodes  

---

## 🟠 L3 (Senior)

Q: Risks in this setup?  
- No rollback  
- Direct production deployment  
- No versioning  

Q: Improvements?  
- Use artifact repository  
- Add CI/CD stages  
- Implement rollback strategy  

---

## 🔴 L4 (Architect)

Q: Production-grade design?  
- CI → Build artifact → Store → Deploy  
- Blue-Green deployment  
- Monitoring & alerting  

---

# ☁️ AWS Scenario Mapping

---

## Equivalent AWS Architecture

| Component | AWS Service |
|----------|------------|
| Jenkins | EC2 |
| Agent | EC2 Auto Scaling |
| Repo | CodeCommit |
| Apache | EC2 |
| Load Balancer | ALB |

---

## Sample AWS Pipeline

```
CodeCommit → CodeBuild → CodeDeploy → EC2 → ALB
```

---

# 🚨 Real AWS Incident RCA

---

## Incident 1: Production Outage

Cause:  
Direct `git pull` deployed faulty code  

Impact:  
Application downtime  

Resolution:  
- Artifact versioning  
- Blue-Green deployment  
- Health checks  

---

## Incident 2: Jenkins Agent Failure

Cause:  
Java version mismatch  

Error:

```
UnsupportedClassVersionError
```

Fix:

```bash
Install Java 17
```

---

# 🎤 Full DevOps Mock Interview

---

Q1: What is CI/CD?  
CI builds and tests code, CD deploys it automatically  

Q2: What is Jenkins agent?  
A remote node where Jenkins executes jobs  

Q3: What is Load Balancer?  
Distributes incoming traffic  

Q4: What is git pull?  
Fetch + merge latest code  

Q5: Difference between CI and CD?

| CI | CD |
|----|----|
| Build & Test | Deploy |

Q6: How to secure pipeline?  
- SSH keys  
- Secret management  

Q7: How to scale Jenkins?  
- Multiple agents  
- Kubernetes  

Q8: How to rollback?  
- Previous version  
- Blue-Green  

---

# 🏁 Final Summary

You implemented a CI/CD pipeline where:

- Jenkins connects via SSH  
- Pulls code from Git  
- Deploys to Apache  
- Serves via Load Balancer  

---

# 🚀 Next Improvements

- Webhook automation  
- Docker deployment  
- Kubernetes CI/CD  
- Blue-Green deployment  

---

✅ TASK COMPLETED SUCCESSFULLY 🎯
