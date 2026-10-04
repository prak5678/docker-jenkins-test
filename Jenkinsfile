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
                echo "Building the Docker Image..."

                bat "docker build -t ${IMAGE_TAG} ."
            }
        }

        stage('Push to Registry') {
            steps {
                echo "Pushing to Docker Hub..."

                withCredentials([
                    usernamePassword(
                        credentialsId: "${DOCKERHUB_CREDENTIALS}",
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {

                    bat '''
                        docker logout
                        echo %DOCKER_PASS%| docker login -u %DOCKER_USER% --password-stdin docker.io

                        docker push %IMAGE_TAG%

                        docker tag %IMAGE_TAG% %IMAGE_NAME%:latest
                        docker push %IMAGE_NAME%:latest
                    '''
                }
            }
        }

        stage('Deploy to Production') {
            steps {
                echo "Deploying to Production..."

                bat '''
                    docker rm -f my-production-app || exit 0
                '''

                bat '''
                    docker pull %IMAGE_TAG%
                '''

                bat '''
                    docker run -d -p 80:80 --name my-production-app %IMAGE_TAG%
                '''
            }
        }
    }
}