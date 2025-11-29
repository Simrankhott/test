pipeline {
    agent any

    environment {
        APP_NAME = "test1"
    }

    stages {
        stage('Checkout') {
            steps {
                echo "Checking out code..."
                // With "Pipeline script from SCM" Jenkins auto-checkouts
                // but we echo just for logs
            }
        }

        stage('Build') {
            steps {
                echo "Building ${APP_NAME}..."
                sh 'echo "Simran build step running"'
            }
        }

        stage('Test') {
            steps {
                echo "Running tests..."
                sh 'echo "Pretend tests here"'
            }
        }

        stage('Package') {
            steps {
                echo "Packaging app..."
                sh 'ls -la'
            }
        }
    }

    post {
        always {
            echo "Pipeline finished for ${APP_NAME}"
        }
        success {
            echo "✅ Build SUCCESS"
        }
        failure {
            echo "❌ Build FAILED"
        }
    }
}
