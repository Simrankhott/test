pipeline {
    agent any

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
                        docker build -t myapp:v1 .
                    '''
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                script {
                    sh '''
                        echo "Stopping old container if exists..."
                        docker rm -f myapp || true

                        echo "Running new container..."
                        docker run -d --name myapp -p 8085:80 myapp:v1
                    '''
                }
            }
        }
    }
}
