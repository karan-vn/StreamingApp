pipeline {
    agent any

    environment {
        AWS_ACCOUNT_ID = '832767338171'
        AWS_REGION = 'us-east-2'
        ECR = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                url: 'https://github.com/karan-vn/StreamingApp'
            }
        }

        stage('Build Admin') {
            steps {
                sh '''
                docker build -t admin-service \
                -f backend/adminService/Dockerfile backend
                '''
            }
        }

        stage('Build Auth') {
            steps {
                sh '''
                docker build -t auth-service \
                -f backend/authService/Dockerfile backend
                '''
            }
        }

       stage('Build Auth') {
            steps {
                sh '''
                docker build -t auth-service ./backend/authService
                '''
            }
        }

        stage('Build Streaming') {
            steps {
                sh '''
                docker build -t streaming-service \
                -f backend/streamingService/Dockerfile backend
                '''
            }
        }

        stage('Build Frontend') {
            steps {
                sh '''
                docker build -t streaming-frontend ./frontend
                '''
            }
        }

        stage('Login ECR') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'aws-creds',
                        usernameVariable: 'AWS_ACCESS_KEY_ID',
                        passwordVariable: 'AWS_SECRET_ACCESS_KEY'
                    )
                ]) {
                    sh '''
                    aws ecr get-login-password --region $AWS_REGION | \
                    docker login --username AWS --password-stdin $ECR
                    '''
                }
            }
        }

        stage('Push Images') {
            steps {
                sh '''
                docker tag admin-service:latest $ECR/admin-service:latest
                docker push $ECR/admin-service:latest

                docker tag auth-service:latest $ECR/auth-service:latest
                docker push $ECR/auth-service:latest

                docker tag chat-service:latest $ECR/chat-service:latest
                docker push $ECR/chat-service:latest

                docker tag streaming-service:latest $ECR/streaming-service:latest
                docker push $ECR/streaming-service:latest

                docker tag streaming-frontend:latest $ECR/streaming-frontend:latest
                docker push $ECR/streaming-frontend:latest
                '''
            }
        }
    }
}