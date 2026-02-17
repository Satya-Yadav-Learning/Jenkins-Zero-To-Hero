# TaskAndResolution.md

# Jenkins Security Configuration – Project-based Matrix Authorization Strategy

---

# 📌 Task Objective

Configure Jenkins security with the following requirements:

1. Login with admin user.
2. Create user `john`.
3. Enable Project-based Matrix Authorization Strategy.
4. Grant:
   - admin → Overall Administer
   - john → Overall Read
   - john → Job Read (for existing job)
5. Remove all permissions from Anonymous.
6. Ensure john has ONLY read permissions (no SCM, Agent, Build, Configure, etc.)

---

# 🏗 Environment Details

- OS: CentOS Stream 9
- Jenkins Version: 2.541.1
- Java Version: OpenJDK 17
- Jenkins Port: 8080

---

# 🔹 Step 1 – Verify Jenkins Service

```bash
systemctl status jenkins
```

### Output:
```
Active: active (running)
```

### Explanation:
Confirms Jenkins service is running properly.

---

# 🔹 Step 2 – Verify Jenkins Port

```bash
ss -tulnp | grep 8080
```

### Output:
```
tcp LISTEN 0 50 0.0.0.0:8080
```

### Explanation:
Confirms Jenkins is listening on port 8080.

---

# 🔹 Step 3 – Login to Jenkins UI

URL:
```
http://<server-ip>:8080
```

Login Credentials:
```
Username: admin
Password: Admn321
```

---

# 🔹 Step 4 – Create User John

Navigation:
```
Manage Jenkins → Manage Users → Create User
```

Configuration:

| Field | Value |
|-------|-------|
| Username | john |
| Password | LQfKeWWxWD |
| Full Name | John |

---

# 🔹 Step 5 – Install Required Plugin

Plugin:
```
Matrix Authorization Strategy
```

Location:
```
Manage Jenkins → Manage Plugins
```

After installation:
✔ Restart Jenkins  
✔ Wait for login page to reload  

---

# 🔹 Step 6 – Configure Global Security

Navigation:
```
Manage Jenkins → Security
```

Authorization:
```
Project-based Matrix Authorization Strategy
```

---

# 🔹 Step 7 – Permission Configuration

## admin

| Permission | Status |
|------------|--------|
| Overall → Administer | ✅ |

---

## john

| Permission | Status |
|------------|--------|
| Overall → Read | ✅ |
| Job → Read | ✅ |
| Everything else | ❌ |

---

## Anonymous

| Permission | Status |
|------------|--------|
| All | ❌ |

---

# 🔎 Final Validation

Login as john:

✔ Can view dashboard  
✔ Can view job  
❌ Cannot build  
❌ Cannot configure  
❌ Cannot delete  
❌ Cannot access SCM  
❌ Cannot manage Jenkins  

---

# 🎯 Final State Summary

| User | Global Permission | Job Permission |
|------|------------------|---------------|
| admin | Administer | Full |
| john | Overall Read | Job Read Only |
| Anonymous | None | None |

---

# 🧠 Scenario-Based Interview Q&A

---

# 🔹 L1 – Junior Level

### Q1: What is Matrix Authorization Strategy?
It is a role-based access control mechanism in Jenkins allowing fine-grained permission control per user.

### Q2: Why remove Anonymous permissions?
To prevent unauthorized access to Jenkins jobs and configuration.

---

# 🔹 L2 – Mid-Level Engineer

### Q1: Why give Overall Read to john?
Overall Read allows viewing dashboard and system state without administrative control.

### Q2: What happens if Job → Build is enabled?
User can trigger builds, which may impact production pipelines.

---

# 🔹 L3 – Senior Engineer

### Q1: Why use Project-based Matrix instead of Matrix-based?
Project-based allows job-level RBAC, giving granular control per job.

### Q2: How would you secure Jenkins in production?
- Disable anonymous
- Integrate LDAP/AD
- Enable CSRF protection
- Use HTTPS
- Restrict admin users
- Backup Jenkins home

---

# 🔹 L4 – Architect Level

### Q1: Design enterprise-grade Jenkins RBAC.
- Integrate SSO (SAML/OIDC)
- Use Role-Based Strategy Plugin
- Separate Dev / QA / Prod folders
- Apply least privilege principle
- Enable audit logging

---

# ☁ AWS Scenario Mapping

---

## Scenario 1: Jenkins on EC2

Problem:
Unauthorized users accessed Jenkins dashboard.

Root Cause:
Anonymous read enabled.

Resolution:
- Disabled anonymous
- Enabled security group restrictions
- Enabled IAM-based SSM access

---

## Scenario 2: Production Pipeline Tampering

Problem:
Developer modified production job.

Root Cause:
Improper RBAC.

Resolution:
- Implemented project-based matrix
- Separated environments
- Applied IAM roles to agents

---

# 🔥 Real AWS Incident RCA Example

## Incident:
Public Jenkins exposed to internet.

### Root Cause:
Port 8080 open to 0.0.0.0/0 in Security Group.

### Impact:
Crypto mining attack deployed.

### Fix:
- Restricted SG to office IP
- Enabled WAF
- Moved Jenkins behind ALB with HTTPS
- Enabled IAM roles for EC2

---

# 🧪 Full DevOps Mock Interview

---

### Q1: Explain Jenkins architecture.

Master-Agent model:
- Master handles scheduling
- Agents execute builds
- Plugins extend functionality

---

### Q2: Difference between Matrix and Project Matrix?

| Feature | Matrix | Project Matrix |
|----------|---------|----------------|
| Global Control | Yes | Yes |
| Job-Level Control | No | Yes |
| Enterprise Suitability | Medium | High |

---

### Q3: How to secure Jenkins in AWS?

- Use IAM roles
- Use S3 for backups
- Use ALB + HTTPS
- Disable anonymous
- Enable audit logs
- Store secrets in AWS Secrets Manager

---

### Q4: What is Principle of Least Privilege?

Users receive minimum permissions required to perform their job.

---

### Q5: How would you design CI/CD for enterprise?

- GitHub Webhooks
- Jenkins Pipeline as Code
- Multi-stage pipeline
- Artifact storage in S3
- Blue-Green deployment
- Monitoring via CloudWatch

---

# 🏁 Conclusion

✔ Jenkins secured with RBAC  
✔ Anonymous removed  
✔ Least privilege applied  
✔ Job-level access controlled  
✔ Enterprise-ready configuration  

---

# 🚀 End of TaskAndResolution.md

