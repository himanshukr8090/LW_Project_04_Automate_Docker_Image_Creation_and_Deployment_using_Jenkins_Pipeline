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
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Push to Local Registry') {
            steps {
                sh 'docker push $IMAGE_NAME'
            }
        }

        stage('Clean Up') {
            steps {
                sh 'docker rmi $IMAGE_NAME'
            }
        }
    }
}

