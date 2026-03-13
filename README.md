# 🌍 Wanderlust CI/CD Project
A full-stack travel blog application deployed using a complete CI/CD pipeline with automated code quality checks, security scanning, and containerized deployment.

---

## 🛠️ Tech Stack

| Tool | Purpose |
|------|---------|
| **GitHub** | Source code management |
| **Docker** | Containerization |
| **Jenkins** | CI/CD Pipeline |
| **SonarQube** | Code Quality Analysis |
| **Trivy** | Docker Image Security Scanning |

---

## 🏗️ Architecture
```
GitHub Push → Jenkins Pipeline → SonarQube Scan → Docker Build → Trivy Scan → Docker Hub → Deploy
```

---

## 🚀 Setup & Installation

### 1. Clone the Repository
```bash
git clone https://github.com/juhisinha422/Wanderlust-CI-CD-Project.git
cd Wanderlust-CI-CD-Project
```

---

### 2. Install Docker
```bash
sudo apt-get update
sudo apt-get install docker.io -y
sudo usermod -aG docker ubuntu && newgrp docker
```

> `usermod -aG docker ubuntu` adds the current user to the docker group so you can run docker commands without `sudo`.

---

### 3. Install Java & Jenkins
```bash
sudo apt update
sudo apt install fontconfig openjdk-21-jre -y
java -version
```
```bash
sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2026.key
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt update
sudo apt install jenkins -y
```

> Jenkins runs on port **8080** by default. Access it at `http://<your-server-ip>:8080`

---

### 4. Install & Configure SonarQube

Run SonarQube as a Docker container:
```bash
docker run -itd --name SonarQube-Server -p 9000:9000 sonarqube:lts-community
```

> SonarQube runs on port **9000**. Access it at `http://<server-ip>:9000`

---

#### 4a. First Time Login

1. Open `http://<server-ip>:9000` in browser
2. Login with default credentials:
```
   Username: admin
   Password: admin
```
3. It will ask you to **change the password** — set a new strong password and save.

---

#### 4b. Generate PAT (Personal Access Token)

SonarQube token is needed so Jenkins can authenticate with SonarQube.
```
SonarQube → My Account (top right) → Security
→ Generate Token
→ Copy the token immediately (shown only once!)
```

Now add this token in Jenkins:
```
Jenkins → Manage Jenkins → Credentials → Global → Add Credentials
```

Configure SonarQube server in Jenkins:
```
Jenkins → Manage Jenkins → System → SonarQube Servers
→ Add SonarQube
```

---

#### 4c. Add SonarQube Webhook (for Quality Gate)

Webhook allows SonarQube to notify Jenkins when analysis is complete.
```
SonarQube → Administration → Webhooks
→ Create
   Name: jenkins
   URL:  http://<jenkins-server-ip>:8080/sonarqube-webhook/
→ Save
```

> ⚠️ Make sure Jenkins port **8080** is open in your EC2 Security Group inbound rules.

---

### 5. Install Trivy
```bash
sudo apt-get install wget gnupg -y
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | \
  gpg --dearmor | sudo tee /usr/share/keyrings/trivy.gpg > /dev/null
echo "deb [signed-by=/usr/share/keyrings/trivy.gpg] \
  https://aquasecurity.github.io/trivy-repo/deb generic main" | \
  sudo tee -a /etc/apt/sources.list.d/trivy.list
sudo apt-get update
sudo apt-get install trivy -y
```

Verify installation:
```bash
trivy --version
```

---

## 🔧 Jenkins Pipeline

The pipeline automates the following stages:

| Stage | Description |
|-------|-------------|
| **Code Clone** | Pulls latest code from GitHub |
| **SonarQube Analysis** | Scans code for bugs and vulnerabilities |
| **Docker Image Build** | Builds frontend & backend Docker images |
| **Trivy Image Scan** | Scans images for security vulnerabilities |
| **Push to Docker Hub** | Tags and pushes versioned images |
| **Database Deploy** | Runs MongoDB container |
| **App Deploy** | Runs frontend & backend containers |


<img width="1561" height="686" alt="Screenshot (564)" src="https://github.com/user-attachments/assets/8f6a078e-1fed-4580-9bb8-d9e81303408c" />

---

## 🌐 Access the Application

 | `http://<server-ip>:5173` |


