# TaskAndResolution.md

# Jenkins – Automated Database Backup (Stratos Datacenter)

---

# 1. Task Description

The Nautilus DevOps team needed to automate database backups using Jenkins.

## Requirements

- Create a Jenkins job named `database-backup`
- Take MySQL dump of database: `kodekloud_db01`
- Database user: `kodekloud_roy`
- Password: `asdfgdsd`
- Source server: `stapp01`
- Destination server: `ststor01`
- Backup path: `/home/natasha/db_backups`
- File format: `db_$(date +%F).sql`
- Schedule: `*/10 * * * *`

---

# 2. Environment Details

- Jenkins Server: iac-server
- App Server: stapp01 (User: tony)
- Storage Server: ststor01 (User: natasha)
- Database: MySQL
- OS: Linux

---

# 3. Solution Architecture

```
Jenkins Server
     │
     │ (SSH)
     ▼
stapp01 (MySQL DB)
     │
     │ (SCP)
     ▼
ststor01 (/home/natasha/db_backups)
```

---

# 4. Step-by-Step Implementation

---

## Step 1: Access Jenkins

Open browser:

```
http://<jenkins-server-ip>:8080
```

Login with admin credentials.

---

## Step 2: Create Jenkins Job

- Click **New Item**
- Enter name:

```
database-backup
```

- Select:
OBOBOB
```
Freestyle Project
```

- Click **OK**
OBOBOB
---

## Step 3: Configure Build Trigger
OBOBOB
Enable:

```
Build periodically
```

Add schedule:

```
*/10 * * * *
```

Explanation:

- Runs job every 10 minutes

---

## Step 4: Configure Build Step
OBOBOB
Click:
OBOBOB
```
Add Build Step → Execute Shell
```

---

## Step 5: Add Shell Script

```bash
# Take database dump on stapp01
ssh tony@stapp01 "mysqldump -u kodekloud_roy -pasdfgdsd kodekloud_db01 > /tmp/db_$(date +%F).sql"

# Copy dump to storage server
scp tony@stapp01:/tmp/db_$(date +%F).sql natasha@ststor01:/home/natasha/db_backups/
```

---

## Step 6: Configure Passwordless SSH

Run on Jenkins server:

```bash
ssh-keygen
ssh-copy-id tony@stapp01
ssh-copy-id natasha@ststor01
```

Explanation:

- Enables Jenkins to connect without password prompts

---

## Step 7: Save Job

Click:

```
Save
```

---

# 5. Execution

## Run Job

Click:

```
Build Now
```

---

## Console Output

```
Started by user admin
Running as SYSTEM
Building in workspace /var/lib/jenkins/workspace/database-backup
+ date +%F
+ ssh tony@stapp01 mysqldump -u kodekloud_roy -pasdfgdsd kodekloud_db01 > /tmp/db_2026-03-18.sql
+ date +%F
+ scp tony@stapp01:/tmp/db_2026-03-18.sql natasha@ststor01:/home/natasha/db_backups/
Finished: SUCCESS
```

---

# 6. Verification

## Check on Storage Server

```bash
ssh natasha@ststor01
ls -l /home/natasha/db_backups/
```

Expected Output:

```
db_2026-03-18.sql
```

---

# 7. Explanation of Commands

---

## mysqldump

```bash
mysqldump -u kodekloud_roy -pasdfgdsd kodekloud_db01
```

- Dumps MySQL database
- `-u` → username
- `-p` → password

---

## SSH

```bash
ssh tony@stapp01 "command"
```

- Executes command remotely

---

## SCP

```bash
scp source destination
```

- Secure file transfer between servers

---

## date

```bash
date +%F
```

- Returns date in `YYYY-MM-DD` format

---

# 8. Scenario-Based Interview Q&A

---

## L1 – Junior

### Q1: What is Jenkins?
Jenkins is a CI/CD tool used to automate build, test, and deployment tasks.

### Q2: What is mysqldump?
It is a utility to export MySQL database into a SQL file.

---

## L2 – Mid-Level

### Q1: Why use SSH in Jenkins jobs?
To execute commands on remote servers.

### Q2: What is SCP?
Secure Copy Protocol used to transfer files between servers.

---

## L3 – Senior

### Q1: How to secure DB credentials?
- Use Jenkins credentials store
- Avoid hardcoding passwords

### Q2: How to improve this pipeline?
- Add logging
- Add failure alerts
- Store backups in S3

---

## L4 – Architect

### Q1: How to design scalable backup system?

- Use centralized backup service
- Use S3 + lifecycle policies
- Automate retention

---

# 9. AWS Scenario Mapping

---

## Scenario: Store backups in AWS

Flow:

```
EC2 → Jenkins → S3 Bucket → Glacier
```

Tools:

- S3 (storage)
- IAM (security)
- CloudWatch (monitoring)
- Lambda (automation)

---

# 10. Real AWS Incident RCA

---

## Incident: Backup Failure

Root Cause:
- SSH key expired

Impact:
- No backups generated

Resolution:
- Rotated keys
- Added monitoring

---

## Incident: Data Loss

Root Cause:
- Backup stored on same server

Impact:
- Server crash = data loss

Resolution:
- Use remote storage (S3)

---

# 11. Full DevOps Mock Interview

---

### Q1: How would you automate DB backups?

Answer:
- Use Jenkins
- Use cron scheduling
- Use mysqldump
- Store backups remotely

---

### Q2: How to handle failures?

Answer:
- Add retry mechanism
- Use alerts (Slack/Email)
- Monitor logs

---

### Q3: How to secure pipeline?

Answer:
- Use Jenkins credentials
- Use IAM roles
- Encrypt backups

---

### Q4: How to scale this solution?

Answer:
- Use Kubernetes jobs
- Use managed DB backups
- Use centralized storage

---

# 12. Key Learnings

- Jenkins automates operational tasks
- SSH enables remote execution
- SCP enables secure file transfer
- Always store backups on separate server
- Automation reduces manual errors
- Scheduling ensures regular backups

---

# 13. Conclusion

The Jenkins job successfully:

- Created database dump
- Stored backup with timestamp
- Transferred backup to storage server
- Automated execution every 10 minutes

---

END OF DOCUMENT
