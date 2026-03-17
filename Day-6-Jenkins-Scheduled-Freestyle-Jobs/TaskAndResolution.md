# TaskAndResolution.md

# Jenkins Automation – Copy Apache Logs Between Servers

---

# 📌 Task Objective

Create a Jenkins job **copy-logs** that:

1. Runs every **6 minutes**
2. Copies Apache logs:
   - `/var/log/httpd/access_log`
   - `/var/log/httpd/error_log`
3. From **App Server 3 (stapp03)**
4. To **Storage Server (ststor01)** at:
   ```
   /usr/src/itadmin
   ```
5. Build job once and verify logs

---

# 🏗 Environment Details

| Component | Details |
|----------|--------|
| Jenkins Server | Ubuntu |
| App Server | stapp03 |
| Storage Server | ststor01 |
| User (App Server) | banner |
| User (Storage Server) | natasha |
| Jenkins User | jenkins |
| Tool Used | Jenkins Freestyle Job |

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
Confirms Jenkins service is running and ready.

---

# 🔹 Step 2 – Create Jenkins Job

Jenkins UI:

```
Dashboard → New Item → copy-logs → Freestyle Project
```

---

# 🔹 Step 3 – Configure Build Trigger

Enable:

```
Build periodically
```

Cron Expression:

```bash
*/6 * * * *
```

### Explanation
Runs job every 6 minutes.

---

# 🔹 Step 4 – Add Build Step

Add:

```
Execute Shell
```

### Script Used

```bash
#!/bin/bash

scp banner@stapp03.stratos.xfusioncorp.com:/var/log/httpd/access_log \
natasha@ststor01.stratos.xfusioncorp.com:/usr/src/itadmin/

scp banner@stapp03.stratos.xfusioncorp.com:/var/log/httpd/error_log \
natasha@ststor01.stratos.xfusioncorp.com:/usr/src/itadmin/
```

---

# 🔹 Step 5 – Initial Failure & Debugging

### Error 1

```
Host key verification failed
```

### Fix

```bash
ssh banner@stapp03.stratos.xfusioncorp.com
ssh natasha@ststor01.stratos.xfusioncorp.com
```

Accepted SSH fingerprints → added to:

```bash
~/.ssh/known_hosts
```

---

# 🔹 Step 6 – Passwordless SSH Setup

### Generate Key

```bash
ssh-keygen -t ed25519
```

### Copy Key

```bash
ssh-copy-id banner@stapp03.stratos.xfusioncorp.com
ssh-copy-id natasha@ststor01.stratos.xfusioncorp.com
```

### Verification

```bash
ssh banner@stapp03.stratos.xfusioncorp.com
ssh natasha@ststor01.stratos.xfusioncorp.com
```

✔ No password prompt

---

# 🔹 Step 7 – Second Failure

```
scp: dest open "/usr/src/itadmin/": Failure
```

### Root Cause
Destination directory did not exist.

---

# 🔹 Step 8 – Fix Directory Issue

On Storage Server:

```bash
mkdir -p /usr/src/itadmin
chmod 755 /usr/src/itadmin
```

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
✔ SSH trust established  
✔ Passwordless authentication enabled  
✔ Logs copied successfully  

---

# 🧠 Scenario-Based Interview Questions & Answers

---

# 🔹 L1 – Junior Level

### Q1: What is Jenkins?
Jenkins is an open-source automation server used for CI/CD pipelines.

### Q2: What is cron in Jenkins?
Cron is used to schedule jobs periodically.

---

# 🔹 L2 – Mid-Level

### Q1: Why did SCP fail initially?
Due to:
- Host key verification issue
- Password authentication requirement

### Q2: Why passwordless SSH is required?
Automation tools cannot input passwords during execution.

---

# 🔹 L3 – Senior Level

### Q1: How would you optimize this solution?
- Replace SCP with `rsync`
- Add logging mechanism
- Use retry logic
- Monitor failures

### Q2: How to secure this setup?
- Restrict SSH via IP
- Use SSH keys only
- Disable root login
- Enable audit logs

---

# 🔹 L4 – Architect Level

### Q1: How would you design scalable log collection?

- Use:
  - Fluentd / Logstash
  - Centralized logging (ELK)
  - S3 + Lambda
  - CloudWatch Logs

---

# ☁ AWS Scenario Mapping

---

## Scenario 1: Replace SCP with S3

Instead of SCP:

- Logs pushed to S3
- Lambda processes logs
- CloudWatch monitors

---

## Scenario 2: Central Logging

- EC2 → CloudWatch Logs
- CloudWatch → S3 → Athena

---

# 🔥 Real AWS Incident RCA

## Incident:
Public Jenkins exposed → crypto mining attack

### Root Cause:
- Security Group open to 0.0.0.0/0
- No authentication

### Impact:
- CPU spike
- Cost increase

### Fix:
- Restrict SG
- Enable IAM roles
- Add WAF

---

# 🧪 Full DevOps Mock Interview

---

### Q1: Explain Jenkins workflow

Code → Build → Test → Deploy

---

### Q2: Difference between SCP and rsync

| SCP | rsync |
|-----|------|
| Copies full file | Copies delta |
| Slower | Faster |
| No compression | Compression supported |

---

### Q3: How to secure Jenkins?

- HTTPS
- RBAC
- Disable anonymous
- Backup Jenkins home

---

### Q4: What is CI/CD?

CI → Continuous Integration  
CD → Continuous Deployment

---

### Q5: Design CI/CD pipeline

- Git trigger
- Jenkins build
- Docker build
- Push to registry
- Deploy via Kubernetes

---

# 🏁 Conclusion

✔ Automated log transfer  
✔ Troubleshooting handled  
✔ Production-ready concept understood  
✔ Real DevOps scenario implemented  

---

# 🚀 End of File
