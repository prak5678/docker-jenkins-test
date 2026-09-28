pipeline {
    agent any
    
    environment {
        DOCKERHUB_CREDENTIALS = 'docker-hub-credentials'
        IMAGE_NAME = 'prak5678/my-test-app'
        IMAGE_TAG = "${IMAGE_NAME}:${env.BUILD_ID}"
    }

    stages {
        stage('Build Image') {
            steps {
                script {
                    echo "Building the Docker Image..."
                    // This uses your local Docker Desktop to build the image
                    dockerImage = docker.build("${IMAGE_TAG}")
                }
            }
        }

        stage('Push to Registry') {
            steps {
                script {
                    echo "Pushing to Docker Hub..."
                    docker.withRegistry('https://index.docker.io/v1/', DOCKERHUB_CREDENTIALS) {
                        dockerImage.push()
                        dockerImage.push('latest')
                    }
                }
            }
        }

        stage('Deploy to "Production"') {
            steps {
                script {
                    echo "Deploying locally on port 80..."
                    // Since you are testing on one laptop, "Production" is just running the container
                    // Remove the old container if it exists
                    bat 'docker rm -f my-production-app || exit 0'
                    // Run the newly pushed image from Docker Hub
                    bat "docker run -d -p 80:80 --name my-production-app ${IMAGE_TAG}"
                }
            }
        }
    }
}