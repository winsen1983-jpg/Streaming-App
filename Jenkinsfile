pipeline {
agent any
environment {
AWS_ACCOUNT_ID = '985368780045'
AWS_DEFAULT_REGION = 'us-east-1'
IMAGE_TAG = '1.0.0'
}
stages {
stage('Checkout Code') {
steps {
checkout scm
}
}
stage('Build & Push Services') {
steps {
echo 'Building and pushing Docker images...'
}
}
}
post {
success {
echo 'CI/CD Pipeline Completed Successfully!'
}
failure {
echo 'Pipeline failed. Please check the logs.'
}
}
}