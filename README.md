# 🌐 Node.js CI/CD Project using Jenkins, Docker, and AWS

This project demonstrates setting up a full CI/CD pipeline for a Node.js application using **Jenkins**, **Docker**, and deploying it on an **AWS EC2 instance**.

---

## 🔧 Technologies Used

- Node.js
- Docker
- Jenkins
- AWS EC2 (Ubuntu)
- GitHub
- Docker Hub

---

## 📦 Project Files

- `app.js` – Node.js application file
- `package.json` – Node.js dependencies
- `Dockerfile` – To containerize the application
- `Jenkinsfile` – Jenkins pipeline to automate the build & deployment

---

## 🚀 Application Output

When accessed via public IP (port 3000) after deployment:

Hello from Jenkins CI/CD!

---

## 📁 Project Setup and Steps

### 1. Launch EC2 Instance on AWS

- Ubuntu-based t2.micro instance (Free tier)
- Allow inbound ports: `22` (SSH), `8080` (Jenkins), `3000` (App)

---

### 2. Install Required Tools

SSH into EC2 and install:

Install Docker & Java
sudo apt update
sudo apt install -y docker.io openjdk-17-jdk git
sudo systemctl enable docker
sudo usermod -aG docker ubuntu

### 3. Install Jenkins
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt install -y jenkins
sudo systemctl start jenkins
Access Jenkins on http://<EC2-Public-IP>:8080

### 4. Unlock Jenkins 
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
Install suggested plugins

### 5. Install Plugins
Nodejs,
Docker all plugins

### 6. Setup Docker Credentials in Jenkins
Go to: Manage Jenkins → Credentials → System → Global
Add “Username with Password”
ID: dockerhub-creds
Username: your Docker Hub username
Password: your Docker Hub password

### 7. Create Jenkins Pipeline
Go to Jenkins → New Item → Pipeline → Name: Node-CI-CD
Under Pipeline Script, add the following Jenkinsfile:

pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'nodejs-app'
        GIT_REPO = 'https://github.com/irfankhan47/-Node.js-Project.git'
        DOCKER_USER = 'irfankhan47'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git "${env.GIT_REPO}"
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Run Docker Container') {
            steps {
                sh '''
                    docker stop nodeapp || true
                    docker rm nodeapp || true
                    docker run -d -p 3000:3000 --name nodeapp $DOCKER_IMAGE
                '''
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKERHUB_USER', passwordVariable: 'DOCKERHUB_PASS')]) {
                    sh '''
                        echo "$DOCKERHUB_PASS" | docker login -u "$DOCKERHUB_USER" --password-stdin
                        docker tag $DOCKER_IMAGE $DOCKERHUB_USER/$DOCKER_IMAGE:latest
                        docker push $DOCKERHUB_USER/$DOCKER_IMAGE:latest
                    '''
                }
            }
        }
    }
}

### 8.
Jenkins will:
Clone the GitHub repo
Install Node.js dependencies
Build Docker image
Run container on port 3000
Push image to Docker Hub

### 9. Test Your App
Go to:
http://<your-ec2-public-ip>:3000

You should see:
Hello from Jenkins CI/CD!


📌 Author
Irfan Khan
www.linkedin.com/in/irfan-khan4
💼 DevOps & Cloud Enthusiast
🔗 GitHub
https://github.com/irfankhan47
🏁 Conclusion
This project is a full demonstration of CI/CD automation using Jenkins and Docker on an AWS EC2 instance — perfect to showcase on your resume or GitHub portfolio!


---


