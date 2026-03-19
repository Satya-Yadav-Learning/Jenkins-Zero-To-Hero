# 📘 TaskAndResolution.md

## Jenkins SSH Agent (Slave Nodes) Setup – DevOps Lab

---

# 📌 TASK OBJECTIVE

Configure Jenkins to use **3 App Servers as SSH Agents (Slaves)** with updated identities:

| Node Name      | Host      | User  | Label     | Remote Directory    |
| -------------- | --------- | ----- | --------- | ------------------- |
| App_node_alpha | appnode01 | bruce | appnode01 | /home/bruce/jenkins |
| App_node_beta  | appnode02 | clark | appnode02 | /home/clark/jenkins |
| App_node_gamma | appnode03 | diana | appnode03 | /home/diana/jenkins |

---

# 🏗️ ARCHITECTURE OVERVIEW

```
Jenkins Master (ci-server)
        |
        | SSH
        |
-----------------------------------------
|           |            |
appnode01   appnode02    appnode03
(bruce)     (clark)      (diana)
```

---

# 🔧 STEP-BY-STEP IMPLEMENTATION

---

## ✅ Step 1: Login to Jenkins

* URL: Jenkins UI
* Username: `admin`
* Password: `Adm!n321`

---

## ✅ Step 2: Install Required Plugin

Navigate:
Manage Jenkins → Plugins → Available Plugins

Install:

* SSH Build Agents Plugin

Action:

* Restart Jenkins after installation

---

## ✅ Step 3: Create SSH Credentials

Navigate:
Manage Jenkins → Credentials → Global → Add Credentials

### 🔹 Credential 1

* Username: bruce
* Password: Batm@n123
* ID: bruce-ssh

### 🔹 Credential 2

* Username: clark
* Password: Sup3rm@n
* ID: clark-ssh

### 🔹 Credential 3

* Username: diana
* Password: W0nderW0man
* ID: diana-ssh

---

## ✅ Step 4: Prepare Remote Directories

### App Node Alpha

```bash
ssh bruce@appnode01
mkdir -p /home/bruce/jenkins
chown -R bruce:bruce /home/bruce/jenkins
chmod 755 /home/bruce/jenkins
```

### App Node Beta

```bash
ssh clark@appnode02
mkdir -p /home/clark/jenkins
chown -R clark:clark /home/clark/jenkins
chmod 755 /home/clark/jenkins
```

### App Node Gamma

```bash
ssh diana@appnode03
mkdir -p /home/diana/jenkins
chown -R diana:diana /home/diana/jenkins
chmod 755 /home/diana/jenkins
```

---

## ✅ Step 5: Install Java 17 (CRITICAL)

### Problem Observed

```
UnsupportedClassVersionError:
class file version 61.0 → Java 17
runtime supports 55.0 → Java 11
```

### Fix

```bash
sudo yum install java-17-openjdk -y
```

### Verify

```bash
java --version
```

Expected Output:

```
openjdk 17.x
```

### Set Default Java

```bash
sudo alternatives --config java
```

---

## ✅ Step 6: Create Nodes in Jenkins

Navigate:
Manage Jenkins → Nodes → New Node

---

### 🔹 Node Alpha

* Name: App_node_alpha
* Remote Dir: /home/bruce/jenkins
* Label: appnode01
* Host: appnode01.example.com
* Credentials: bruce-ssh
* Launch Method: Launch via SSH

---

### 🔹 Node Beta

* Name: App_node_beta
* Remote Dir: /home/clark/jenkins
* Label: appnode02
* Host: appnode02.example.com
* Credentials: clark-ssh

---

### 🔹 Node Gamma

* Name: App_node_gamma
* Remote Dir: /home/diana/jenkins
* Label: appnode03
* Host: appnode03.example.com
* Credentials: diana-ssh

---

## ✅ Step 7: Connection Log (Successful)

```
Starting agent process:
cd "/home/bruce/jenkins" && java -jar remoting.jar

INFO: Using /home/bruce/jenkins/remoting as a work directory
INFO: channel started

Agent successfully connected and online
```

---

# ✅ FINAL RESULT

✔ All nodes ONLINE
✔ SSH working
✔ Java compatibility fixed
✔ Jenkins distributed execution ready

---

# ⚠️ WARNING (NON-BLOCKING)

```
Swap Space: 0 B
```

### Optional Fix

```bash
sudo fallocate -l 1G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
```

---

# 🎯 SCENARIO BASED INTERVIEW Q&A

---

## 🔹 L1 (Junior)

**Q: What is Jenkins Agent?**
A: A node that executes jobs assigned by Jenkins master.

---

## 🔹 L2 (Mid-Level)

**Q: Why did agent fail?**
A: Java mismatch (11 vs 17)

---

## 🔹 L3 (Senior)

**Q: How to debug offline agent?**
A:

1. Check SSH
2. Check Java
3. Check permissions
4. Check logs

---

## 🔹 L4 (Architect)

**Q: Design scalable Jenkins?**
A:

* Kubernetes agents
* Auto-scaling
* Distributed pipelines

---

# ☁️ AWS MAPPING

| Jenkins Concept | AWS Service |
| --------------- | ----------- |
| Agents          | EC2         |
| Storage         | EBS         |
| Logs            | CloudWatch  |
| Access          | IAM         |

---

# 🚨 REAL INCIDENT RCA

---

## Incident: All Agents Down

**Root Cause:** Java upgraded only on master
**Impact:** CI/CD stopped

**Fix:** Upgrade Java on all agents

**Prevention:**

* Version standardization
* Automation via Ansible

---

# 🧪 FULL DEVOPS MOCK INTERVIEW

---

## Q1: Jenkins Architecture

A: Master + Agents

---

## Q2: Job Execution Flow

A:

1. Scheduler assigns node
2. Agent executes
3. Logs returned

---

## Q3: Slow Builds

A:

* CPU check
* Disk IO
* Network

---

## Q4: CI/CD Design

A:

* Jenkins + Kubernetes
* Auto-scaling
* Pipeline as code

---

# 🚀 SAMPLE PIPELINE

```groovy
pipeline {
    agent { label 'appnode01' }

    stages {
        stage('Test') {
            steps {
                sh 'hostname'
            }
        }
    }
}
```

---

# 🏁 FINAL CONCLUSION

✔ Task completed successfully
✔ Real production issue resolved
✔ Fully interview ready
✔ Distributed Jenkins environment operational

---

# 🔥 BONUS

👉 90% Jenkins failures due to:

* Java mismatch
* SSH issues
* Permission issues

---

