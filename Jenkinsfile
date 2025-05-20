pipeline {
    agent any
    triggers { cron('H/5 * * * *') }
    environment {
        DOCKER_USER = 'amanrgv'
        REPO_NAME = 'hu-2025-docker-jenkins-draghav'
    }
    stages {
        stage('Checkout') { steps { checkout scm } }
        
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
                    # First verify container is running
                    if ! docker run -d --name test-container ${DOCKER_USER}/${REPO_NAME}:latest; then
                        echo "Failed to start container"
                        exit 1
                    fi
                    
                    # Wait for NGINX to start (max 10 seconds)
                    timeout 10 bash -c 'while ! docker exec test-container curl -s localhost >/dev/null; do sleep 1; done'
                    
                    # Check for HTML content
                    if docker exec test-container curl -s localhost | grep -q "HU 2025"; then
                        echo "Test passed - Found expected content"
                    else
                        echo "Test failed - Content not found"
                        docker exec test-container curl -s localhost  # Print actual output for debugging
                        exit 1
                    fi
                    
                    # Clean up
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
    }
}