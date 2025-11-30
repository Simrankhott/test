pipeline {
    agent any

    environment {
        DOCKERHUB_USER = "simrankhot"   // <-- your Docker Hub username
        IMAGE_NAME = "myapp"
        IMAGE_TAG = "v1"
    }

    stages {

        stage('Checkout code') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh '''
                        echo "Building Docker Image..."
                        docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .
                    '''
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                script {
                    sh '''
                        echo "Stopping old container..."
                        docker rm -f ${IMAGE_NAME} || true

                        echo "Running new container..."
                        docker run -d --name ${IMAGE_NAME} -p 8085:80 ${IMAGE_NAME}:${IMAGE_TAG}
                        docker ps
                    '''
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    withCredentials([usernamePassword(
                        credentialsId: 'docker',
                        usernameVariable: 'DH_USER',
                        passwordVariable: 'DH_PASS'
                    )]) {

                        sh '''
                            echo "Logging into Docker Hub..."
                            echo "$DH_PASS" | docker login -u "$DH_USER" --password-stdin

                            echo "Tagging image..."
                            docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${DOCKERHUB_USER}/${IMAGE_NAME}:${IMAGE_TAG}

                            echo "Pushing image to Docker Hub..."
                            docker push ${DOCKERHUB_USER}/${IMAGE_NAME}:${IMAGE_TAG}

                            docker logout
                        '''
                    }
                }
            }
        }
    }
}
