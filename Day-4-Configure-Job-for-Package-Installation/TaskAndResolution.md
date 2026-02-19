
# ==============================================================
# TASK
# ==============================================================

1. Access Jenkins UI and login using:
   - Username: admin
   - Password: 

2. Create a Jenkins Freestyle job named: `install-packages`

3. Configure:
   - String parameter named `PACKAGE`
   - Install the package specified in `${PACKAGE}` on the Storage Server

4. Ensure:
   - Required plugins installed
   - Non-interactive execution
   - Job runs successfully on repeated executions

Environment:
- Jenkins Server
- Storage Server
- OS: CentOS Stream 9

---

# ==============================================================
# PART 1 — IMPLEMENTATION & RESOLUTION
# ==============================================================

## Step 1 — Login to Jenkins

Access Jenkins UI → Login with:
```
Username: admin
Password: 
```

---

## Step 2 — Create Jenkins Job

New Item → Freestyle Project  
Name: `install-packages`

Enable:
✔ This project is parameterized  

Add Parameter:
- Type: String Parameter
- Name: PACKAGE
- Description: Package to install on storage server

---

## Step 3 — Identify OS on Storage Server

```bash
cat /etc/os-release
```

### Output

```
NAME="CentOS Stream"
VERSION="9"
```

### Conclusion

- Package Manager: `dnf`
- Not apt
- Not yum (wrapper only)

---

## Step 4 — Initial Manual Test

```bash
sudo dnf install -y vim
```

It prompted for password:

```
[sudo] password for natasha:
```

### Problem

Jenkins cannot handle interactive sudo password prompts.

---

## Step 5 — Configure Passwordless sudo (Restricted)

As root:

```bash
visudo
```

Add:

```
natasha ALL=(ALL) NOPASSWD: /usr/bin/dnf
```

---

## Step 6 — Verify sudo Configuration

As natasha:

```bash
sudo -l
```

Output:

```
(ALL) NOPASSWD: /usr/bin/dnf
```

Test:

```bash
sudo dnf install -y tree
```

Output:

```
Complete!
```

No password prompt → Automation ready.

---

## Step 7 — Jenkins SSH Host Key Issue

Initial build failure:

```
Host key verification failed.
Finished: FAILURE
```

### Fix

On Jenkins server:

```bash
sudo su - jenkins
ssh natasha@storage-server
```

Type `yes` to accept fingerprint.

---

## Step 8 — Configure SSH Key-Based Authentication

On Jenkins server:

```bash
ssh-keygen -t ed25519
```

Press Enter for defaults.

Then:

```bash
ssh-copy-id natasha@storage-server
```

Enter password once.

Test:

```bash
ssh natasha@storage-server "echo connected"
```

Output:

```
connected
```

Passwordless SSH confirmed.

---

## Step 9 — Final Jenkins Build Step

In Jenkins → Execute Shell:

```bash
ssh natasha@storage-server "sudo dnf install -y ${PACKAGE}"
```

---

## Step 10 — Successful Console Output

```
+ ssh natasha@storage-server sudo dnf install -y vim
Package vim-enhanced-... is already installed.
Nothing to do.
Complete!
Finished: SUCCESS
```

---

## Repeat Execution Validation

Re-run with same package:

```
Nothing to do.
Complete!
Finished: SUCCESS
```

Confirms:

✔ Idempotency  
✔ Reliability  
✔ Non-interactive automation  

---

# ==============================================================
# PART 2 — SCENARIO BASED INTERVIEW (L1 → L4)
# ==============================================================

# L1 — Junior Level

Q: How do you create a parameterized Jenkins job?

A:
- Enable "This project is parameterized"
- Add String parameter
- Use `${PARAM}` in build step

---

# L2 — System Administrator

Scenario:
Build fails with "Host key verification failed".

Answer:
SSH fingerprint not in known_hosts.  
Solution: Manually SSH once or manage known_hosts centrally.

---

# L3 — Senior Engineer

Scenario:
Jenkins job hangs during sudo.

Root Cause:
Interactive password prompt.

Fix:
Configure restricted NOPASSWD sudo.

Security Principle:
Least Privilege.

---

# L4 — Architect

Question:
Is SSH + sudo best design?

Better Architecture:
- Use Jenkins Agent on storage server
- Use Ansible
- Use centralized identity (LDAP/AD)
- Remove password authentication entirely
- Implement RBAC

---

# ==============================================================
# PART 3 — AWS MAPPING SCENARIOS
# ==============================================================

Linux Concept:
sudo privilege

AWS Equivalent:
IAM Role with restricted permissions.

Instead of:
```
natasha ALL=(ALL)
```

Use:
- IAM Role
- Policy with specific actions
- Least privilege

---

# AWS Scenario 1

EC2 package install via automation.

Best Practice:
- Use SSM Run Command
- Avoid SSH
- Use IAM roles

---

# AWS Scenario 2 — Real Incident

Incident:
Production EC2 compromised.

Root Cause:
Security group allowed 0.0.0.0/0 on port 22.

Fix:
- Use SSM instead of SSH
- Restrict SG
- Enable GuardDuty

---

# AWS Scenario 3 — IAM Credential Expiry

Incident:
App downtime due to expired temporary credentials.

Root Cause:
No automatic refresh logic.

Fix:
Use IAM roles with SDK auto-refresh.

---

# ==============================================================
# PART 4 — REAL INCIDENT RCAs
# ==============================================================

Incident 1 — CI/CD Pipeline Hanging

Root Cause:
Interactive sudo prompt.

Impact:
Pipeline stuck, delayed deployment.

Fix:
NOPASSWD configuration.

---

Incident 2 — Host Key Verification Failure

Root Cause:
Untrusted SSH fingerprint.

Impact:
Build failure.

Fix:
Preconfigure known_hosts or use configuration management.

---

Incident 3 — Security Misconfiguration

User had:
```
(ALL) ALL
```

Risk:
Privilege escalation.

Better:
Restricted command-only sudo.

---

# ==============================================================
# PART 5 — FULL DEVOPS MOCK INTERVIEW
# ==============================================================

Round 1 — Linux

Q: Difference between yum and dnf?
A: dnf is next-gen package manager in RHEL 8+.

Q: What is idempotency?
A: Repeated execution produces same result.

---

Round 2 — Jenkins

Q: How do you pass dynamic values to job?
A: Parameterized builds.

Q: How to debug failing build?
A:
- Console output
- Check exit code
- Verify SSH connectivity
- Check sudo config

---

Round 3 — Security

Q: Why not allow NOPASSWD: ALL?
A:
Violates least privilege.

---

Round 4 — Architecture

Design secure automation system:

- SSH key-based auth
- Restricted sudo
- No passwords in pipeline
- Centralized logging
- Audit trail
- RBAC
- Infrastructure as Code

---

# ==============================================================
# FINAL SUMMARY
# ==============================================================

✔ Jenkins job created  
✔ Parameterized configuration  
✔ OS identified correctly  
✔ sudo configured securely  
✔ SSH key authentication implemented  
✔ Host key issue resolved  
✔ Job runs successfully  
✔ Repeated execution validated  
✔ Automation secure & idempotent  
✔ Interview depth L1 → L4 covered  
✔ AWS mapping included  
✔ Real incident RCAs documented  

---

END OF DOCUMENT

