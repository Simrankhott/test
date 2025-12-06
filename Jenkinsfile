pipeline {
    agent any

    environment {
        IMAGE_NAME = "myapp"
        IMAGE_TAG  = "v1"
    }

    stages {

        stage('Checkout the code') {
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

        stage('Trivy Scan Docker Image') {
            steps {
                script {
                    sh """
                        echo "Running Trivy scan on image ${IMAGE_NAME}:${IMAGE_TAG}..."
                        docker run --rm \
                          -v /var/run/docker.sock:/var/run/docker.sock \
                          aquasec/trivy:latest image \
                          --severity HIGH,CRITICAL \
                          ${IMAGE_NAME}:${IMAGE_TAG} || true
                    """
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
                withCredentials([usernamePassword(credentialsId: 'docker', usernameVariable: 'DH_USER', passwordVariable: 'DH_PASS')]) {
                    script {
                        sh '''
                          echo "Logging into Docker Hub..."
                          echo "$DH_PASS" | docker login -u "$DH_USER" --password-stdin

                          echo "Tagging image..."
                          docker tag ${IMAGE_NAME}:${IMAGE_TAG} "$DH_USER/${IMAGE_NAME}:${IMAGE_TAG}"

                          echo "Pushing image..."
                          docker push "$DH_USER/${IMAGE_NAME}:${IMAGE_TAG}"
                        '''
                    }
                }
            }
        }
    }
}
