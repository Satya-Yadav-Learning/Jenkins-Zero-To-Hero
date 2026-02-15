# Task and Resolution – Jenkins Installation

## Objective
Install Jenkins on CentOS Stream 9 using `yum` only, start the service, and verify successful setup.

---

## 1️⃣ Verify Operating System

### Command
cat /etc/os-release

### Output
NAME="CentOS Stream"
VERSION="9"
ID="centos"
VERSION_ID="9"

### Explanation
Confirmed the server is running CentOS Stream 9 (EL9).  
Jenkins on EL9 requires Java 17.

---

## 2️⃣ Verify Java Availability (Initial Check)

### Command
java -version

### Output
-bash: java: command not found

### Explanation
Java was not installed. Jenkins requires Java runtime.

---

## 3️⃣ Install Java 17 Using yum

### Command
yum install -y java-17-openjdk java-17-openjdk-devel

### Installation Summary (Key Lines)
Installed:
java-17-openjdk-17.0.17
java-17-openjdk-devel-17.0.17

### Verify Installation
java -version

### Output
openjdk version "17.0.17" 2025-10-21 LTS
OpenJDK Runtime Environment (Red_Hat-17.0.17.0.10-1)
OpenJDK 64-Bit Server VM

### Explanation
Java 17 successfully installed and configured.

---

## 4️⃣ Add Jenkins Repository

### Add Repository
wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo

### Output
Saving to: ‘/etc/yum.repos.d/jenkins.repo’
saved [267/267]

### Import GPG Key
rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key

### Refresh Repository Metadata
yum clean all
yum makecache

### Verify Repository
yum repolist | grep jenkins

### Output
jenkins   Jenkins-stable

### Explanation
Jenkins repository successfully configured.

---

## 5️⃣ Install Jenkins Using yum

### Command
yum install -y jenkins

### Installation Summary
Installed:
jenkins-2.541.1-1.noarch

### Explanation
Jenkins installed successfully from Jenkins-stable repository.

---

## 6️⃣ Enable and Start Jenkins Service

### Commands
systemctl daemon-reexec
systemctl enable jenkins
systemctl start jenkins

### Verify Service Status
systemctl status jenkins

### Output (Key Lines)
Active: active (running)
Main PID: 7710 (java)

### Explanation
Jenkins service started successfully and enabled at boot.

---

## 7️⃣ Verify Listening Port

### Command
ss -tulnp | grep 8080

### Output
tcp LISTEN 0 50 0.0.0.0:8080 0.0.0.0:* users:(("java",pid=7710))

### Explanation
Jenkins is listening on port 8080 on all interfaces.

---

## 8️⃣ Retrieve Initial Admin Password

### Command
cat /var/lib/jenkins/secrets/initialAdminPassword

### Output
ca150583a36141f385ff5e7dc8be24d0

### Explanation
Used this password to unlock Jenkins Web UI and create the required admin user.

---

## 9️⃣ Final Service Validation

### Commands
systemctl is-enabled jenkins
systemctl is-active jenkins

### Output
enabled
active

### Explanation
Jenkins service is enabled and running properly.

---

# ✅ Final Status

✔ CentOS Stream 9 verified  
✔ Java 17 installed  
✔ Jenkins repository configured  
✔ Jenkins installed using yum  
✔ Jenkins service enabled  
✔ Service running successfully  
✔ Port 8080 listening  
✔ Admin password retrieved  

Task Completed Successfully.

