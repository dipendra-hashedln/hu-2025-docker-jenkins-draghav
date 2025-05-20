pipeline {
    agent any

    triggers {
        cron('H/5 * * * *') 
    }

    environment {

        DOCKER_USER = 'amanrgv'  
        REPO_NAME = 'hu-2025-docker-jenkins-draghav'
        IMAGE_NAME = 'sample-app'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Image') {
            steps {
                script {
                    docker.build("${DOCKER_USER}/${REPO_NAME}:latest") 
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
                        docker.image("${DOCKER_USER}/${REPO_NAME}:latest").push()
                    }
                }
            }
        }
    }
}