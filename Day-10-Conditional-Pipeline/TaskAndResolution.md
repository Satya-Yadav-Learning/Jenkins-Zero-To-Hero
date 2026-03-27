# 🚀 Jenkins CI/CD Deployment using SSH Agent (Task & Resolution)

---

## 📌 Task Overview

- Configure Jenkins with remote agent (App Server 1)
- Use SSH authentication with user `sarah`
- Deploy code from Gitea repo `web_app`
- Use parameterized pipeline (BRANCH: master / feature)
- Deploy to Apache document root `/var/www/html`
- Verify via Load Balancer

---

# 🏗️ Architecture

Gitea → Jenkins → SSH Agent (stapp01.stratos.xfusioncorp.com) → Apache → Load Balancer

---

# ⚙️ Implementation Steps

---

## 🔹 Step 1: Install Plugins

Manage Jenkins → Manage Plugins → Available

Install:
- Pipeline
- Git Plugin
- SSH Build Agents

Restart Jenkins

---

## 🔐 Step 2: SSH Key Setup

### Jenkins Server

```bash
ssh-keygen -t ed25519
cat ~/.ssh/id_ed25519.pub
```

---

### App Server (stapp01)

```bash
ssh sarah@stapp01.stratos.xfusioncorp.com

mkdir -p ~/.ssh
vi ~/.ssh/authorized_keys
```

Paste public key

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

---

### Test SSH

```bash
ssh sarah@stapp01.stratos.xfusioncorp.com
```

✔ No password prompt

---

## 🖥️ Step 3: Configure Jenkins Agent

- Name: App Server 1  
- Label: stapp01  
- Host: stapp01.stratos.xfusioncorp.com  
- Remote Dir: /home/sarah/jenkins_agent  
- Launch: via SSH  

---

## 🔑 Step 4: Add Credentials

- Kind: SSH Username with private key  
- Username: sarah  
- Private Key: Jenkins private key  

---

## ☕ Step 5: Fix Java Issue

### Error

```text
UnsupportedClassVersionError
```

### Fix

```bash
ssh sarah@stapp01.stratos.xfusioncorp.com

sudo yum install -y java-17-openjdk
sudo alternatives --config java
java -version
```

---

## 📦 Step 6: Verify Repo

```bash
ssh sarah@stapp01.stratos.xfusioncorp.com

cd /var/www/html
ls -a
```

✔ Contains `.git`

---

## 🛠️ Step 7: Create Pipeline Job

- Name: nautilus-webapp-job  
- Type: Pipeline  

---

## ⚙️ Step 8: Add Parameter

- Name: BRANCH  
- Default: master  

---

## 📜 Step 9: Pipeline Script

```groovy
pipeline {
    agent { label 'stapp01' }

    parameters {
        string(name: 'BRANCH', defaultValue: 'master', description: 'Branch name (master/feature)')
    }

    stages {
        stage('Deploy') {
            steps {
                script {
                    if (params.BRANCH == 'master' || params.BRANCH == 'feature') {
                        sh """
                        cd /var/www/html
                        git fetch origin
                        git checkout ${params.BRANCH}
                        git pull origin ${params.BRANCH}
                        """
                    } else {
                        error "Invalid branch"
                    }
                }
            }
        }
    }
}
```

---

# 📊 Execution Output

## Master

```text
git checkout master
Already up to date.
Finished: SUCCESS
```

---

## Feature

```text
git checkout feature
Switched to branch 'feature'
Finished: SUCCESS
```

---

# 🌐 Verification

```
https://<LBR-URL>
```

✔ App loads  
✔ No `/web_app`  

---

# ❗ Issues & Fixes

---

## Agent Offline

✔ Fixed SSH setup  

---

## Java Error

✔ Installed Java 17  

---

## Branch Issue

```bash
cd /var/www/html
git fetch origin
git checkout -b feature origin/feature
```

---

# 🎯 Final Outcome

✔ Parameterized deployment  
✔ SSH agent working  
✔ Dynamic branch switching  

---

# 🧠 Scenario-Based Interview Q&A

---

## L1 (Junior)

Q: What is Jenkins agent?  
A: Remote node executing jobs  

---

## L2 (Mid-Level)

Q: Why SSH agent?  
A: Secure remote execution  

---

## L3 (Senior)

Q: Risks?  
- No rollback  
- Direct deployment  

---

## L4 (Architect)

Q: Design production system?  
- CI → Artifact → Deploy  
- Blue-Green  

---

# ☁️ AWS Mapping

| Component | AWS |
|----------|-----|
| Jenkins | EC2 |
| Agent | EC2 |
| Repo | CodeCommit |
| LB | ALB |

---

# 🚨 AWS Incident RCA

---

## Incident: Deployment Failure

Cause:
Wrong branch deployed  

Fix:
Branch validation  

---

## Incident: Agent Failure

Cause:
Java mismatch  

Fix:
Standardize Java version  

---

# 🎤 DevOps Mock Interview

---

Q: Explain your project  
A: Built Jenkins CI/CD pipeline with SSH agent and dynamic branch deployment  

---

Q: Challenge  
A: Java version mismatch  

---

Q: Improvement  
A: Add rollback + CI triggers  

---

# 🧾 Final Summary

```
Built a parameterized Jenkins pipeline using SSH agent (sarah@stapp01) to deploy Git branches dynamically to Apache (/var/www/html).
```

---

✅ TASK COMPLETED SUCCESSFULLY 🎯
