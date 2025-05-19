pipeline {
    agent any

    environment {
        // Use YOUR Docker Hub username here
        DOCKER_HUB = 'amanrgv/hu-2025-docker-jenkins-draghav'  
        IMAGE_NAME = 'sample-app'
        TAG = 'latest'
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm  // This automatically uses the repo configured in Jenkins
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${DOCKER_HUB}/${IMAGE_NAME}:${TAG}")
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
                        dockerImage.push()
                    }
                }
            }
        }
    }
}