pipeline {
    agent any
   // triggers { cron('H/5 * * * *') }
    environment {
        DOCKER_USER = 'amanrgv'
        REPO_NAME = 'hu-2025-docker-jenkins-draghav'
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

        stage('Test') {
            steps {
                script {
                    sh '''
                    docker run -d --name test-container ${DOCKER_USER}/${REPO_NAME}:latest || exit 1
                    timeout 10 bash -c 'while ! docker exec test-container curl -s localhost >/dev/null; do sleep 1; done'
                    if docker exec test-container curl -s localhost | grep -q "HU 2025"; then
                        echo "Test passed - Found expected content"
                    else
                        echo "Test failed - Content not found"
                        docker exec test-container curl -s localhost
                        exit 1
                    fi
                    docker stop test-container
                    docker rm test-container
                    '''
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

        stage('Deploy') {
            steps {
                script {
                    sh '''
                    docker rm -f deployed-container || true
                    docker run -d --name deployed-container -p 8082:80 ${DOCKER_USER}/${REPO_NAME}:latest
                    '''
                }
            }
        }
    }
}
