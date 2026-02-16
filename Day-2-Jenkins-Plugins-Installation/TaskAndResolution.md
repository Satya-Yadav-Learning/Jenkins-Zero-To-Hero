# 1️⃣ Task Summary

## Task:
- Login to Jenkins UI using admin credentials
- Install **Git Plugin** and **GitLab Plugin**
- Restart Jenkins if required
- Verify plugin installation

---

# 2️⃣ Resolution Steps Performed

## Step 1: Access Jenkins
- URL: `http://<server-ip>:8080`
- Login using:
  - Username: `admin`
  - Password: `Adm!n31`

## Step 2: Navigate to Plugin Manager

Manage Jenkins → Plugins → Available Plugins


## Step 3: Install Plugins
Installed:
- Git Plugin
- GitLab Plugin

Chose:

Download now and install after restart


## Step 4: Restart Jenkins
Restarted Jenkins safely and waited until login page reappeared.

## Step 5: Verification
Confirmed under:

Manage Jenkins → Plugins → Installed

Both plugins were:
- Installed
- Enabled
- Active

---

# 3️⃣ Scenario-Based Interview Questions & Answers (L1 → L4)

---

# 🔹 L1 Level (Junior Engineer)

### Q1: What is the purpose of Git plugin in Jenkins?
**Answer:**
Git plugin integrates Jenkins with Git repositories. It allows Jenkins jobs to:
- Clone repositories
- Pull code
- Trigger builds on commit

---

### Q2: Why do we restart Jenkins after plugin installation?
**Answer:**
Some plugins modify core components or load new dependencies. Restart ensures:
- Proper class loading
- Dependency initialization
- Stable runtime behavior

---

# 🔹 L2 Level (Mid-Level Engineer)

### Q3: What happens if Git plugin has a security vulnerability?
**Answer:**
If the plugin has an OS command injection vulnerability:
- Malicious user may execute arbitrary commands on agent nodes
- Can lead to privilege escalation
- Infrastructure compromise

Mitigation:
- Update plugin
- Restrict agent permissions
- Use least privilege IAM

---

### Q4: How does GitLab plugin trigger Jenkins builds?
**Answer:**
- GitLab sends webhook event (push/merge)
- Jenkins endpoint receives payload
- Validates token
- Triggers pipeline

---

# 🔹 L3 Level (Senior Engineer)

### Q5: What could go wrong in production while installing plugins?

**Possible Issues:**
- Plugin dependency conflict
- Jenkins version incompatibility
- Downtime during restart
- Memory spike due to plugin load

Mitigation Strategy:
- Test in staging
- Take Jenkins backup
- Use maintenance window

---

### Q6: How would you secure Jenkins in production?

**Best Practices:**
- Enable Role-Based Access Control (RBAC)
- Use HTTPS
- Store secrets in credential store
- Restrict agent execution
- Enable audit logs
- Keep plugins updated

---

# 🔹 L4 Level (Architect Level)

### Q7: Design a Highly Available Jenkins Architecture in AWS

**Architecture:**
- Jenkins Master on EC2
- Agents as auto-scaling EC2
- EFS for shared storage
- ALB in front
- RDS for metadata (optional)
- S3 for backup
- IAM role-based access

---

### Q8: How would you eliminate Jenkins single point of failure?

**Solution Options:**
- Jenkins in Kubernetes (HA)
- Use ECS/EKS agents
- Use managed CI tools (GitHub Actions / GitLab CI)

---

# 4️⃣ AWS Scenario-Based Interview Questions & Answers

---

## Scenario 1: EC2 Suddenly Unreachable

### Root Cause:
Security Group inbound rule removed.

### Resolution:
- Verified via AWS Console
- Restored port 22 access
- Implemented change management process

---

## Scenario 2: Jenkins Build Failed Suddenly

### Root Cause:
IAM Role attached to EC2 lost S3 permission.

### Resolution:
- Reviewed CloudTrail logs
- Updated IAM policy
- Implemented least privilege review process

---

## Scenario 3: High AWS Bill After CI/CD Setup

### Root Cause:
- EC2 agents not terminating
- NAT Gateway running 24/7
- No lifecycle policy on S3

### Resolution:
- Enabled auto-scaling termination
- Replaced NAT with VPC endpoints
- Enabled S3 lifecycle rules

---

# 5️⃣ Real AWS Incident RCA (Example 1)

## Incident:
Production outage for 2 hours.

## Symptoms:
- Application returning 502 error.
- ALB health checks failing.

## Root Cause:
Deployment pipeline pushed broken build without health check validation.

## Contributing Factors:
- No blue-green deployment
- No rollback automation
- Manual approval skipped

## Resolution:
- Rolled back AMI
- Implemented blue-green deployment
- Added pre-deployment smoke tests

---

# 6️⃣ Real AWS Incident RCA (Example 2)

## Incident:
Data breach via exposed S3 bucket.

## Root Cause:
S3 bucket policy allowed public read access.

## Detection:
AWS GuardDuty alert.

## Resolution:
- Removed public access
- Enabled S3 Block Public Access
- Enabled AWS Config rule

## Preventive Action:
- SCP to block public bucket policy
- Continuous compliance scanning

---

# 7️⃣ Full DevOps Mock Interview

---

## 🔹 Round 1: CI/CD Basics

**Q:** What is CI/CD?
**Answer:**
Continuous Integration and Continuous Delivery automate:
- Code integration
- Build
- Test
- Deployment

---

## 🔹 Round 2: Jenkins Deep Dive

**Q:** Explain Jenkins architecture.
**Answer:**
- Master coordinates
- Agents execute jobs
- Plugins extend functionality

---

## 🔹 Round 3: AWS DevOps

**Q:** How would you deploy microservices on AWS?

**Answer:**
- Dockerize app
- Push to ECR
- Deploy via ECS/EKS
- Use ALB
- Use CloudWatch monitoring

---

## 🔹 Round 4: Kubernetes

**Q:** What is difference between Deployment and StatefulSet?

Deployment:
- Stateless apps
- Rolling updates

StatefulSet:
- Persistent storage
- Stable network identity

---

## 🔹 Round 5: Troubleshooting

**Q:** Build failing randomly?

Steps:
1. Check console logs
2. Check disk space
3. Check memory
4. Validate credentials
5. Check network connectivity

---

# 8️⃣ Advanced DevOps Architect-Level Questions

---

### Q: How do you design enterprise-grade CI/CD?

**Answer:**
- Separate build and deploy pipelines
- Use artifact repository (Nexus/Artifactory)
- Immutable infrastructure
- Infrastructure as Code (Terraform)
- Automated rollback
- Observability integration

---

# 9️⃣ Final Conclusion

Task successfully completed:
- Plugins installed
- Jenkins validated
- Security warning identified
- Environment stable

This task demonstrates:
- CI/CD fundamentals
- Plugin management
- Production awareness
[O- Security posture understanding
- AWS DevOps alignment

---

END OF DOCUMENT


