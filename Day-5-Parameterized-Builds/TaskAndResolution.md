
# ==============================================================
# TASK
# ==============================================================

1. Login to Jenkins UI using:
   - Username: admin
   - Password: 

2. Create a parameterized job named:
   parameterized-job

3. Add parameters:
   - String Parameter: Stage (Default value: Build)
   - Choice Parameter: env (Development, Staging, Production)

4. Configure job to execute a shell command that echoes both parameter values.

5. Build the job at least once with env=Production and ensure it passes.

---

# ==============================================================
# PART 1 — IMPLEMENTATION & RESOLUTION
# ==============================================================

## Step 1 — Login to Jenkins

Access Jenkins → Login with:

```
Username: admin
Password: 
```

---

## Step 2 — Create Freestyle Job

New Item → Enter name:

```
parameterized-job
```

Select:
```
Freestyle project
```

Click OK.

---

## Step 3 — Enable Parameterized Build

✔ Check:
```
This project is parameterized
```

---

## Step 4 — Add String Parameter

Add Parameter → String Parameter

Configuration:

```
Name: Stage
Default Value: Build
Description: Pipeline stage
```

Explanation:

- String parameter allows free-text input.
- Default value ensures consistent build behavior.

---

## Step 5 — Add Choice Parameter

Add Parameter → Choice Parameter

Configuration:

```
Name: env
Choices:
Development
Staging
Production
```

Explanation:

- Choice parameter restricts values.
- Prevents human input errors.
- Improves environment control.

---

## Step 6 — Add Build Step (Execute Shell)

Add build step → Execute Shell

```bash
echo "Stage: ${Stage}"
echo "Environment: ${env}"
```

Explanation:

- `${Stage}` and `${env}` reference Jenkins parameters.
- Variables expand at runtime.
- Useful for pipeline logic and conditional execution.

---

## Step 7 — Build the Job

Click:

Build with Parameters

Select:

```
Stage: Build
env: Production
```

---

## Step 8 — Console Output (Successful Run)

```
Started by user admin
Running as SYSTEM
Building in workspace /var/lib/jenkins/workspace/parameterized-job
[parameterized-job] $ /bin/sh -xe /tmp/jenkins17949881217533328629.sh
+ echo Stage: Build
Stage: Build
+ echo Environment: Production
Environment: Production
Finished: SUCCESS
```

---

# ==============================================================
# VALIDATION
# ==============================================================

✔ Job name correct  
✔ String parameter working  
✔ Choice parameter working  
✔ env=Production selected  
✔ Build result SUCCESS  
✔ Output correctly echoed  

Task successfully completed.

---

# ==============================================================
# PART 2 — SCENARIO BASED INTERVIEW Q&A (L1 → L4)
# ==============================================================

# L1 — Junior Level

Q1: What is a parameterized job in Jenkins?

A:
A job that accepts input parameters at runtime to make builds dynamic.

Q2: Difference between String and Choice parameter?

A:
- String: Free-text input.
- Choice: Restricted predefined values.

---

# L2 — System Administrator

Scenario:
User selects wrong environment accidentally.

Solution:
Use Choice parameter to restrict environment options.

Security Benefit:
Prevents deployment to wrong environment.

---

# L3 — Senior DevOps Engineer

Scenario:
Deploy different scripts based on env parameter.

Example:

```bash
if [ "$env" = "Production" ]; then
    echo "Running production deployment"
else
    echo "Running non-production deployment"
fi
```

Concepts:
- Conditional logic
- Environment separation
- Controlled release management

---

# L4 — Architect Level

Question:
Is freestyle job scalable for enterprise?

Answer:
No.

Better Architecture:
- Jenkins Pipeline (Jenkinsfile)
- Git-based pipeline versioning
- Role-based environment promotion
- Approval gates for Production
- RBAC enforcement

Enterprise Considerations:
- Audit trail
- Change management
- Separation of duties

---

# ==============================================================
# PART 3 — AWS SCENARIO MAPPING
# ==============================================================

Jenkins Parameter → AWS Equivalent

| Jenkins | AWS Equivalent |
|----------|----------------|
| env parameter | Environment tag |
| Choice parameter | IAM condition |
| Build trigger | CodePipeline stage |

---

# AWS Scenario 1

Deploy to different AWS accounts based on env.

Mapping:

```
Development → Dev AWS Account
Staging → Stage AWS Account
Production → Prod AWS Account
```

Use:
- IAM roles
- STS AssumeRole
- Separate accounts

---

# AWS Scenario 2 — Real Incident RCA

Incident:
Production deployment triggered accidentally.

Root Cause:
No environment restriction in pipeline.

Impact:
Service outage.

Resolution:
- Introduced environment parameter validation
- Added approval step for Production
- Restricted IAM permissions

Lesson:
Environment separation is critical.

---

# AWS Scenario 3 — IAM Misconfiguration

Incident:
Pipeline had AdministratorAccess.

Impact:
Accidental resource deletion.

Root Cause:
No least-privilege policy.

Fix:
Use restricted IAM policies per environment.

---

# ==============================================================
# PART 4 — REAL INCIDENT RCAs
# ==============================================================

Incident 1 — Deployment to Wrong Environment

Cause:
Manual input error.

Fix:
Use Choice parameters.

---

Incident 2 — Production Overwrite

Cause:
Single shared environment.

Fix:
Isolate environments using separate pipelines.

---

Incident 3 — Missing Approval Gate

Cause:
Auto-deploy enabled for Production.

Fix:
Add manual approval stage.

---

# ==============================================================
# PART 5 — FULL DEVOPS MOCK INTERVIEW
# ==============================================================

Round 1 — Jenkins Basics

Q: What is Freestyle vs Pipeline?
A:
Freestyle: UI-based configuration.
Pipeline: Code-based CI/CD (recommended).

---

Round 2 — Security

Q: How to prevent Production deployment without approval?
A:
- Use input step in pipeline
- Restrict Production environment
- RBAC enforcement

---

Round 3 — AWS Integration

Q: How to deploy to AWS securely?
A:
- Use IAM role
- Avoid static credentials
- Use AssumeRole

---

Round 4 — Advanced

Q: How to scale CI/CD across environments?

Answer:
- Infrastructure as Code
- Multi-account AWS setup
- Centralized Jenkins
- Parameterized pipelines
- Role-based deployments

---

Round 5 — Architecture Discussion

Design enterprise CI/CD:

- SCM-triggered pipelines
- Parameter validation
- Separate Dev/Stage/Prod accounts
- Manual approval gates
- Audit logging
- Least privilege IAM
- Rollback strategy

---

# ==============================================================
# FINAL SUMMARY
# ==============================================================

✔ Parameterized Jenkins job created  
✔ String parameter configured  
✔ Choice parameter configured  
✔ Shell command echoing parameters  
✔ Production build executed successfully  
✔ Build status SUCCESS  
✔ L1 → L4 interview depth covered  
✔ AWS mapping included  
✔ Real incident RCAs explained  
✔ DevOps mock interview completed  

---

END OF DOCUMENT

