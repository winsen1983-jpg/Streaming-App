pipeline {
    agent any
    
    environment {
        AWS_ACCOUNT_ID = '985368780045'
        AWS_DEFAULT_REGION = 'us-east-1'
        ECR_REGISTRY = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com"
        IMAGE_TAG = '1.0.0'
    }
    
    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }
        
        stage('AWS ECR Login') {
            steps {
                // Jenkins Credentials-இல் உள்ள AWS Keys-ஐப் பயன்படுத்தி லாகின் செய்ய
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'AWS_Winsen1983']]) {
                    sh "aws ecr get-login-password --region ${AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin ${ECR_REGISTRY}"
                }
            }
        }
        
        stage('Build & Push Auth Service') {
            steps {
                sh "docker build -t streaming-auth ./backend/authService"
                sh "docker tag streaming-auth:latest ${ECR_REGISTRY}/streaming-auth:${IMAGE_TAG}"
                sh "docker push ${ECR_REGISTRY}/streaming-auth:${IMAGE_TAG}"
            }
        }
        
 stage('Build & Push Streaming Service') {
    steps {
        sh "docker build -t streaming-service ./backend/streamingService"
        sh "docker tag streaming-service:latest ${ECR_REGISTRY}/streaming-service:${IMAGE_TAG}"
        sh "docker push ${ECR_REGISTRY}/streaming-service:${IMAGE_TAG}"
    }
}
        
        stage('Build & Push Admin Service') {
            steps {
                sh "docker build -t admin-service -f ./backend/adminService/Dockerfile ./backend"
                sh "docker tag admin-service:latest ${ECR_REGISTRY}/admin-service:${IMAGE_TAG}"
                sh "docker push ${ECR_REGISTRY}/admin-service:${IMAGE_TAG}"
            }
        }
        
        stage('Build & Push Chat Service') {
            steps {
                sh "docker build -t chat-service -f ./backend/chatService/Dockerfile ./backend"
                sh "docker tag chat-service:latest ${ECR_REGISTRY}/chat-service:${IMAGE_TAG}"
                sh "docker push ${ECR_REGISTRY}/chat-service:${IMAGE_TAG}"
            }
        }
        
        stage('Build & Push Frontend') {
            steps {
                sh "docker build -t frontend-service ./frontend"
                sh "docker tag frontend-service:latest ${ECR_REGISTRY}/frontend-service:${IMAGE_TAG}"
                sh "docker push ${ECR_REGISTRY}/frontend-service:${IMAGE_TAG}"
            }
        }
  
    }
    
    post {
        success {
            echo 'CI/CD Pipeline Completed Successfully! All images pushed to ECR.'
        }
        failure {
            echo 'Pipeline failed. Please check the logs.'
        }
    }
}


