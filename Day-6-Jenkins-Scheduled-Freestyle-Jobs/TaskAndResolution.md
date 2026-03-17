# TaskAndResolution.md

# Jenkins Automation – Copy Apache Logs Between Servers (With Authentication Setup)

---

# 📌 Task Objective

Create a Jenkins job **copy-logs** that:

1. Runs every **6 minutes**
2. Copies Apache logs:
   - `/var/log/httpd/access_log`
   - `/var/log/httpd/error_log`
3. From **App Server 3 (stapp03)** → user: `banner`
4. To **Storage Server (ststor01)** → user: `natasha`
5. Destination path:
   ```
   /usr/src/itadmin
   ```
6. Build job once and verify logs

---

# 🏗 Environment Details

| Component | Value |
|----------|------|
| Jenkins Server | jenkins.stratos.xfusioncorp.com |
| App Server 3 | stapp03.stratos.xfusioncorp.com |
| Storage Server | ststor01.stratos.xfusioncorp.com |
| App User | banner |
| Storage User | natasha |
| Jenkins User | jenkins |

---

# 🔹 Step 1 – Verify Jenkins Service

```bash
systemctl status jenkins
```

### Output
```
Active: active (running)
```

### Explanation
Confirms Jenkins service is up and running.

---

# 🔹 Step 2 – Create Jenkins Job

Navigate in UI:

```
Dashboard → New Item → copy-logs → Freestyle Project
```

---

# 🔹 Step 3 – Configure Build Trigger

Enable:

```
Build periodically
```

Cron:

```bash
*/6 * * * *
```

### Explanation
Runs the job every 6 minutes.

---

# 🔐 Step 4 – SSH Authentication Setup (IMPORTANT)

Jenkins runs jobs as **jenkins user**, so SSH must work without password.

---

## 🔹 4.1 Switch to Jenkins User

```bash
sudo su - jenkins
```

---

## 🔹 4.2 Generate SSH Key

```bash
ssh-keygen -t ed25519
```

Press Enter for all prompts.

### Output
```
~/.ssh/id_ed25519
~/.ssh/id_ed25519.pub
```

### Explanation
Creates private and public key for secure authentication.

---

## 🔹 4.3 Verify Key

```bash
ls -l ~/.ssh
```

### Output
```
id_ed25519
id_ed25519.pub
```

---

## 🔹 4.4 Copy Key to App Server (stapp03)

```bash
ssh-copy-id banner@stapp03.stratos.xfusioncorp.com
```

### Explanation
Copies public key to App Server for passwordless login.

---

## 🔹 4.5 Copy Key to Storage Server (ststor01)

```bash
ssh-copy-id natasha@ststor01.stratos.xfusioncorp.com
```

---

## 🔹 4.6 Test Passwordless SSH

```bash
ssh banner@stapp03.stratos.xfusioncorp.com
ssh natasha@ststor01.stratos.xfusioncorp.com
```

### Expected
✔ No password prompt

---

# 🔹 Step 5 – Fix Host Key Verification

First-time SSH requires trust.

```bash
ssh banner@stapp03.stratos.xfusioncorp.com
ssh natasha@ststor01.stratos.xfusioncorp.com
```

Type:

```
yes
```

### Explanation
Adds entries to:

```bash
~/.ssh/known_hosts
```

---

# 🔹 Step 6 – Prepare Destination Directory

On Storage Server:

```bash
ssh natasha@ststor01.stratos.xfusioncorp.com
```

```bash
mkdir -p /usr/src/itadmin
chmod 755 /usr/src/itadmin
```

### Explanation
Ensures directory exists and is writable.

---

# 🔹 Step 7 – Add Build Step in Jenkins

Add:

```
Execute Shell
```

---

## 🔹 Script Used

```bash
#!/bin/bash

scp banner@stapp03.stratos.xfusioncorp.com:/var/log/httpd/access_log \
natasha@ststor01.stratos.xfusioncorp.com:/usr/src/itadmin/

scp banner@stapp03.stratos.xfusioncorp.com:/var/log/httpd/error_log \
natasha@ststor01.stratos.xfusioncorp.com:/usr/src/itadmin/
```

---

# 🔹 Step 8 – First Failure & Debugging

### Error 1
```
Host key verification failed
```

### Fix
Accepted SSH fingerprint → added to known_hosts

---

### Error 2
```
scp: dest open "/usr/src/itadmin/": Failure
```

### Fix
Created destination directory.

---

# 🔹 Step 9 – Test SCP Manually

```bash
scp banner@stapp03.stratos.xfusioncorp.com:/var/log/httpd/access_log \
natasha@ststor01.stratos.xfusioncorp.com:/usr/src/itadmin/
```

✔ Successful transfer

---

# 🔹 Step 10 – Final Jenkins Build

### Output
```
Finished: SUCCESS
```

---

# 🔎 Final Verification

```bash
ssh natasha@ststor01.stratos.xfusioncorp.com
ls -l /usr/src/itadmin
```

### Output
```
access_log
error_log
```

---

# 🎯 Final Status

✔ Jenkins job created  
✔ Cron configured  
✔ SSH authentication configured  
✔ Host key verified  
✔ Logs copied successfully  

---

# 🧠 Scenario-Based Interview Q&A

---

# 🔹 L1 – Junior

### Q1: What is Jenkins?
Automation server for CI/CD pipelines.

### Q2: What is SCP?
Secure file transfer over SSH.

---

# 🔹 L2 – Mid-Level

### Q1: Why passwordless SSH required?
Automation tools cannot provide passwords interactively.

### Q2: What is known_hosts?
Stores trusted SSH server fingerprints.

---

# 🔹 L3 – Senior

### Q1: How to improve this setup?
- Use rsync instead of scp
- Add logging and alerting
- Retry mechanism

---

# 🔹 L4 – Architect

### Q1: Design enterprise log solution
- ELK Stack
- CloudWatch Logs
- Centralized logging pipeline

---

# ☁ AWS Scenario Mapping

---

## Scenario 1: Log Aggregation

- EC2 → CloudWatch Logs → S3 → Athena

---

## Scenario 2: Automation

- Lambda replaces Jenkins cron job
- S3 lifecycle manages logs

---

# 🔥 Real AWS Incident RCA

## Incident:
Jenkins exposed publicly → crypto mining

### Root Cause:
Security group open

### Fix:
- Restrict access
- Use IAM roles
- Enable monitoring

---

# 🧪 Full DevOps Mock Interview

---

### Q1: Explain CI/CD
CI → build/test  
CD → deploy

---

### Q2: SCP vs rsync

| SCP | rsync |
|-----|------|
| Full copy | Delta copy |
| Slower | Faster |

---

### Q3: Secure Jenkins

- HTTPS
- RBAC
- Backup
- Disable anonymous

---

# 🏁 Conclusion

✔ Real-world automation implemented  
✔ Authentication handled correctly  
✔ Troubleshooting completed  
✔ Production concepts understood  

---

# 🚀 End of File
