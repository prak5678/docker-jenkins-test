pipeline {
    agent any
    
    environment {
        // The ID of your Jenkins credentials
        DOCKERHUB_CREDENTIALS = 'docker-hub-credentials'
        IMAGE_NAME = 'prak5678/my-test-app'
        IMAGE_TAG = "${IMAGE_NAME}:${env.BUILD_ID}"
    }

    stages {
        stage('Build Image') {
            steps {
                script {
                    echo "Building the Docker Image..."
                    bat "docker build -t ${IMAGE_TAG} ."
                }
            }
        }

        stage('Push to Registry') {
            steps {
                script {
                    echo "Pushing to Docker Hub..."
                    // This explicitly logs in using your Jenkins credentials
                    withCredentials([usernamePassword(credentialsId: "${DOCKERHUB_CREDENTIALS}", passwordVariable: 'DOCKER_PASS', usernameVariable: 'DOCKER_USER')]) {
                        bat "echo %DOCKER_PASS%| docker login -u %DOCKER_USER% --password-stdin"
                        bat "docker push ${IMAGE_TAG}"
                        bat "docker tag ${IMAGE_TAG} ${IMAGE_NAME}:latest"
                        bat "docker push ${IMAGE_NAME}:latest"
                    }
                }
            }
        }

        stage('Deploy to "Production"') {
            steps {
                script {
                    echo "Deploying locally on port 80..."
                    // Remove the old container if it exists
                    bat 'docker rm -f my-production-app || exit 0'
                    // Run the newly pushed image
                    bat "docker run -d -p 80:80 --name my-production-app ${IMAGE_TAG}"
                }
            }
        }
    }
}