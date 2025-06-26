# 🚀 Project 4: Automate Docker Image Creation & Deployment using Jenkins Pipeline

This project demonstrates how to automate the Docker image build and deployment process using **Jenkins Pipeline**, with Jenkins installed **from scratch** (no container images used) and a **self-hosted Docker registry** for storing images.

---

## 🧰 Tech Stack

- Jenkins (Manual Installation)
- Docker
- GitHub
- Python (Flask)
- Bash
- Ubuntu
- Local Docker Registry

---
## 🔧 Step 1: Install Docker

```bash
sudo apt update
sudo apt install -y docker.io
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker $USER
```
- Then logout and login again (or run `newgrp docker`).

## 🔧 Step 2: Install Java (for Jenkins)

```bash
sudo apt install -y openjdk-11-jdk
java -version
```

## 🔧 Step 3: Install Jenkins (manually)

```bash
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt install -y jenkins
sudo systemctl start jenkins
sudo systemctl enable jenkins
```

## 🔑 Step 4: Access Jenkins Web UI
- Open Jenkins in your browser:
- `http://<your-public-ip>:8080`
- Get initial password:
```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```
- Install recommended plugins.
- Create `admin` user.

## 📦 Step 5: Install Required Jenkins Plugins

Install the following plugins:
- **Docker Pipeline**
- **Git**
- **Pipeline**
- **Docker Commons**
- **Docker plugin**

🔧 Navigate to:  
**Manage Jenkins → Manage Plugins**

## 🏗️ Step 6: Set Up a Local Docker Registry
- Start a local Docker registry container:
```bash
docker run -d -p 5000:5000 --name registry registry:2
```
- ✅ Test the Registry
```bash
curl http://localhost:5000/v2/_catalog
```

## 📂 Step 7: Sample Project Structure  
Push this to your GitHub repo (e.g., `flask-docker-app`):
flask-docker-app/
│
├── app.py
├── requirements.txt
└── Dockerfile


### 📄 `app.py`

```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def home():
    return "Hello from Jenkins Docker Pipeline!"

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=5000)
```

### 📄 `requirements.txt`
```bash
flask
```

### 📄 `Dockerfile`
```dockerfile
FROM python:3.8-slim
WORKDIR /app
COPY . .
RUN pip install -r requirements.txt
CMD ["python", "app.py"]
```

## ⚙️ Step 8: Configure Docker in Jenkins
Run this to allow Jenkins to access Docker:
```bash
sudo usermod -aG docker jenkins
sudo systemctl restart jenkins
```

## 📜 Step 9: Create a Jenkins Pipeline Job
- Go to Jenkins:
- **New Item** → **Pipeline** → **Name it** `docker-pipeline`
- Under **Pipeline script**, use the following:

### 🧪 Sample Jenkinsfile
```groovy
pipeline {
    agent any

    environment {
        IMAGE_NAME = "localhost:5000/flask-docker-app"
    }

    stages {
        stage('Clone Repo') {
            steps {
                git 'https://github.com/<your-username>/flask-docker-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t $IMAGE_NAME .'
                }
            }
        }

        stage('Push to Local Registry') {
            steps {
                script {
                    sh 'docker push $IMAGE_NAME'
                }
            }
        }

        stage('Clean Up') {
            steps {
                script {
                    sh 'docker rmi $IMAGE_NAME'
                }
            }
        }
    }
}
```
- 🔁 Replace <your-username> with your actual GitHub username.

## ✅ Step 10: Run Pipeline and Verify
- Click Build Now in Jenkins.
- After a successful build, verify the image in the local registry:
```bash
curl http://localhost:5000/v2/_catalog
```
- You should see:
```json
{"repositories":["flask-docker-app"]}
```

## ✅ Outcome
- Jenkins installed manually (no Docker image pulled)
- Docker image built from source code using Jenkins GUI
- Image pushed to a local Docker registry

## 📌 Author
**Himanshu Kumar Singh**  
[Let's connect on LinkedIn](https://www.linkedin.com/in/himanshukrsingh0)


## 🏷️ Tags
#Jenkins #Docker #CI #Pipeline #DevOps #Automation #GitHub #SelfHosted
