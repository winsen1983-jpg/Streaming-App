<img width="975" height="86" alt="image" src="https://github.com/user-attachments/assets/20f38150-ea73-4076-a566-d7181211cf18" />27-Sep-2026-Assignment:
 
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 985368780045.dkr.ecr.us-east-1.amazonaws.com
 <img width="975" height="484" alt="image" src="https://github.com/user-attachments/assets/ff26643c-39a8-4c41-b607-2f5bebfd7289" />


Streaming-auth Docker Creation:
 <img width="975" height="484" alt="image" src="https://github.com/user-attachments/assets/34247f1a-c0fe-4aa7-a4a0-00f449129ee3" />
<img width="975" height="484" alt="image" src="https://github.com/user-attachments/assets/cc742dc2-1d2d-47d1-9251-5bb29ea88d10" />

Create ECR:
 <img width="975" height="484" alt="image" src="https://github.com/user-attachments/assets/8e2fb9bd-9ed6-4416-be4d-befefe7a6e45" />

Push Docker Images in ECR
 <img width="975" height="223" alt="image" src="https://github.com/user-attachments/assets/1e6336e7-81cc-4f3e-905b-adc6c37c24ff" />


Create Streaming Service Docker:
 <img width="975" height="413" alt="image" src="https://github.com/user-attachments/assets/b4fa2c19-45c3-46bb-b481-b88f35ffe212" />


Pushed the Streaming-Service in ECR
 <img width="975" height="229" alt="image" src="https://github.com/user-attachments/assets/f05fea7d-e094-4dfa-95b9-1070d9467e8c" />


Admin-Service ECR Created
docker build -t admin-service -f ./backend/adminService/Dockerfile ./backend
 <img width="975" height="358" alt="image" src="https://github.com/user-attachments/assets/a62df3b2-294c-48d0-a8c0-99e404285813" />

docker tag admin-service:latest 985368780045.dkr.ecr.us-east-1.amazonaws.com/admin-service
docker push 985368780045.dkr.ecr.us-east-1.amazonaws.com/admin-service
<img width="975" height="240" alt="image" src="https://github.com/user-attachments/assets/8d29adee-8d84-4157-9c2c-3afd5c14ff8e" />

 

Chat-Service Creation in ECR
docker build -t chat-service -f ./backend/chatService/Dockerfile ./backend

 <img width="975" height="420" alt="image" src="https://github.com/user-attachments/assets/73f79107-3817-449a-a3a8-18d61da991ab" />


docker tag admin-service:latest 985368780045.dkr.ecr.us-east-1.amazonaws.com/chat-service

docker push 985368780045.dkr.ecr.us-east-1.amazonaws.com/chat-service
<img width="975" height="223" alt="image" src="https://github.com/user-attachments/assets/2669136b-0172-4a3e-9436-b6b76e494d8f" />

 
docker build -t frontend-service -f ./frontend/Dockerfile ./frontend


Docker Images:
 <img width="975" height="127" alt="image" src="https://github.com/user-attachments/assets/0b5e2dda-7b08-4392-bac2-06c24a96b14d" />


Frontend Validation:  
GIthub Repo: Jenkinsfile updated from local machine.
 <img width="975" height="656" alt="image" src="https://github.com/user-attachments/assets/495826eb-3e22-4a3b-a02e-8c8df1565186" />
<img width="975" height="659" alt="image" src="https://github.com/user-attachments/assets/8009cdd0-8c2b-42d8-9049-b76828c75d09" />


Jenkins Pipeline:
Jenkins file:
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
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws-credentials']]) {
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
-----------------------------
Jenkins Configured Output:
Started by user herovired

Obtained Jenkinsfile from git https://github.com/winsen1983-jpg/Streaming-App
[Pipeline] Start of Pipeline
[Pipeline] node
Running on Jenkins
 in /var/lib/jenkins/workspace/27sep2026
[Pipeline] {
[Pipeline] stage
[Pipeline] { (Declarative: Checkout SCM)
[Pipeline] checkout
The recommended git tool is: git
using credential 1c4543be-3a88-48c3-b03c-c6e903357ed7
 > git rev-parse --resolve-git-dir /var/lib/jenkins/workspace/27sep2026/.git # timeout=10
Fetching changes from the remote Git repository
 > git config remote.origin.url https://github.com/winsen1983-jpg/Streaming-App # timeout=10
Fetching upstream changes from https://github.com/winsen1983-jpg/Streaming-App
 > git --version # timeout=10
 > git --version # 'git version 2.43.0'
using GIT_ASKPASS to set credentials 
 > git fetch --tags --force --progress -- https://github.com/winsen1983-jpg/Streaming-App +refs/heads/*:refs/remotes/origin/* # timeout=10
 > git rev-parse refs/remotes/origin/main^{commit} # timeout=10
Checking out Revision 32c64659543570d4079065697b7737bf5c39cf7c (refs/remotes/origin/main)
 > git config core.sparsecheckout # timeout=10
 > git checkout -f 32c64659543570d4079065697b7737bf5c39cf7c # timeout=10
Commit message: "Dockerfile Updated in streamfile"
 > git rev-list --no-walk 8bcca7320998c0fa4f06cd3845973d8f0cc719e0 # timeout=10
[Pipeline] }
[Pipeline] // stage
[Pipeline] withEnv
[Pipeline] {
[Pipeline] withEnv
[Pipeline] {
[Pipeline] stage
[Pipeline] { (Checkout Code)
[Pipeline] checkout
The recommended git tool is: git
using credential 1c4543be-3a88-48c3-b03c-c6e903357ed7
 > git rev-parse --resolve-git-dir /var/lib/jenkins/workspace/27sep2026/.git # timeout=10
Fetching changes from the remote Git repository
 > git config remote.origin.url https://github.com/winsen1983-jpg/Streaming-App # timeout=10
Fetching upstream changes from https://github.com/winsen1983-jpg/Streaming-App
 > git --version # timeout=10
 > git --version # 'git version 2.43.0'
using GIT_ASKPASS to set credentials 
 > git fetch --tags --force --progress -- https://github.com/winsen1983-jpg/Streaming-App +refs/heads/*:refs/remotes/origin/* # timeout=10
 > git rev-parse refs/remotes/origin/main^{commit} # timeout=10
Checking out Revision 32c64659543570d4079065697b7737bf5c39cf7c (refs/remotes/origin/main)
 > git config core.sparsecheckout # timeout=10
 > git checkout -f 32c64659543570d4079065697b7737bf5c39cf7c # timeout=10
Commit message: "Dockerfile Updated in streamfile"
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (AWS ECR Login)
[Pipeline] withCredentials
Masking supported pattern matches of $AWS_ACCESS_KEY_ID or $AWS_SECRET_ACCESS_KEY
[Pipeline] {
[Pipeline] sh
+ aws ecr get-login-password --region us-east-1
+ docker login --username AWS --password-stdin 985368780045.dkr.ecr.us-east-1.amazonaws.com

WARNING! Your credentials are stored unencrypted in '/var/lib/jenkins/.docker/config.json'.
Configure a credential helper to remove this warning. See
https://docs.docker.com/go/credential-store/

Login Succeeded
[Pipeline] }
[Pipeline] // withCredentials
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Build & Push Auth Service)
[Pipeline] sh
+ docker build -t streaming-auth ./backend/authService
DEPRECATED: The legacy builder is deprecated and will be removed in a future release.
            Install the buildx component to build images with BuildKit:
            https://docs.docker.com/go/buildx/

Sending build context to Docker daemon  102.9kB

Step 1/8 : FROM node:18-alpine AS base
 ---> 8d6421d663b4
Step 2/8 : WORKDIR /app
 ---> Using cache
 ---> a8c58e52590c
Step 3/8 : COPY package*.json ./
 ---> Using cache
 ---> 928830dbcf0c
Step 4/8 : RUN npm install --production
 ---> Using cache
 ---> 70bd58ec4a75
Step 5/8 : COPY . .
 ---> Using cache
 ---> fe625073e0f9
Step 6/8 : ENV NODE_ENV=production
 ---> Using cache
 ---> 1cbf5a08f9bc
Step 7/8 : EXPOSE 3001
 ---> Using cache
 ---> 6a6574335610
Step 8/8 : CMD ["npm", "run", "start"]
 ---> Using cache
 ---> 2daa294135a4
Successfully built 2daa294135a4
Successfully tagged streaming-auth:latest
[Pipeline] sh
+ docker tag streaming-auth:latest 985368780045.dkr.ecr.us-east-1.amazonaws.com/streaming-auth:1.0.0
[Pipeline] sh
+ docker push 985368780045.dkr.ecr.us-east-1.amazonaws.com/streaming-auth:1.0.0
The push refers to repository [985368780045.dkr.ecr.us-east-1.amazonaws.com/streaming-auth]
25ff2da83641: Waiting
e191dc0209eb: Waiting
08a41863402b: Waiting
ee4818747951: Waiting
147782606c31: Waiting
f18232174bc9: Waiting
dd71dde834b5: Waiting
1e5a4c89cee5: Waiting
08a41863402b: Waiting
ee4818747951: Waiting
147782606c31: Waiting
f18232174bc9: Waiting
dd71dde834b5: Waiting
1e5a4c89cee5: Waiting
25ff2da83641: Waiting
e191dc0209eb: Waiting
147782606c31: Waiting
f18232174bc9: Waiting
dd71dde834b5: Waiting
1e5a4c89cee5: Waiting
25ff2da83641: Waiting
e191dc0209eb: Waiting
08a41863402b: Waiting
ee4818747951: Waiting
ee4818747951: Waiting
147782606c31: Waiting
f18232174bc9: Waiting
dd71dde834b5: Waiting
1e5a4c89cee5: Waiting
25ff2da83641: Waiting
e191dc0209eb: Waiting
08a41863402b: Waiting
25ff2da83641: Waiting
e191dc0209eb: Waiting
08a41863402b: Waiting
ee4818747951: Waiting
147782606c31: Waiting
f18232174bc9: Waiting
dd71dde834b5: Waiting
1e5a4c89cee5: Waiting
147782606c31: Waiting
f18232174bc9: Waiting
dd71dde834b5: Waiting
1e5a4c89cee5: Waiting
25ff2da83641: Waiting
e191dc0209eb: Waiting
08a41863402b: Waiting
ee4818747951: Waiting
ee4818747951: Waiting
147782606c31: Waiting
f18232174bc9: Waiting
dd71dde834b5: Waiting
1e5a4c89cee5: Waiting
25ff2da83641: Waiting
e191dc0209eb: Waiting
08a41863402b: Waiting
dd71dde834b5: Waiting
1e5a4c89cee5: Waiting
25ff2da83641: Waiting
e191dc0209eb: Waiting
08a41863402b: Waiting
ee4818747951: Waiting
147782606c31: Waiting
f18232174bc9: Waiting
25ff2da83641: Waiting
e191dc0209eb: Waiting
08a41863402b: Waiting
ee4818747951: Waiting
147782606c31: Waiting
f18232174bc9: Layer already exists
dd71dde834b5: Layer already exists
1e5a4c89cee5: Waiting
1e5a4c89cee5: Layer already exists
25ff2da83641: Layer already exists
e191dc0209eb: Layer already exists
08a41863402b: Layer already exists
ee4818747951: Layer already exists
147782606c31: Layer already exists
1.0.0: digest: sha256:2daa294135a48aac16092092eacee0e8796f5a72de4fb2294e74b6fafa187144 size: 2058
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Build & Push Streaming Service)
[Pipeline] sh
+ docker build -t streaming-service ./backend/streamingService
DEPRECATED: The legacy builder is deprecated and will be removed in a future release.
            Install the buildx component to build images with BuildKit:
            https://docs.docker.com/go/buildx/

Sending build context to Docker daemon  159.7kB

Step 1/8 : FROM node:18-alpine AS base
 ---> 8d6421d663b4
Step 2/8 : WORKDIR /app/streamingService
 ---> Using cache
 ---> ca089148ca3d
Step 3/8 : COPY package*.json ./
 ---> 5e3b03aedc12
Step 4/8 : RUN npm install --production
 ---> Running in 51246f56def8
 [91mnpm warn config production Use `--omit=dev` instead.
 [0m
added 239 packages, and audited 240 packages in 12s

21 packages are looking for funding
  run `npm fund` for details

36 vulnerabilities (5 low, 22 moderate, 7 high, 2 critical)

To address all issues, run:
  npm audit fix

Run `npm audit` for details.
 [91mnpm notice
npm notice New major version of npm available! 10.8.2 -> 12.1.0
npm notice Changelog: https://github.com/npm/cli/releases/tag/v12.1.0
npm notice To update run: npm install -g npm@12.1.0
npm notice
 [0m ---> Removed intermediate container 51246f56def8
 ---> 7d52472bd72e
Step 5/8 : COPY . .
 ---> f5800d58e365
Step 6/8 : ENV NODE_ENV=production
 ---> Running in e29c20ccf51a
 ---> Removed intermediate container e29c20ccf51a
 ---> 457c36e4ac6d
Step 7/8 : EXPOSE 3001
 ---> Running in e257f8fa6037
 ---> Removed intermediate container e257f8fa6037
 ---> 7666c6fb6ab7
Step 8/8 : CMD ["npm", "run", "start"]
 ---> Running in 8826600b5394
 ---> Removed intermediate container 8826600b5394
 ---> ea990301c76f
Successfully built ea990301c76f
Successfully tagged streaming-service:latest
[Pipeline] sh
+ docker tag streaming-service:latest 985368780045.dkr.ecr.us-east-1.amazonaws.com/streaming-service:1.0.0
[Pipeline] sh
+ docker push 985368780045.dkr.ecr.us-east-1.amazonaws.com/streaming-service:1.0.0
The push refers to repository [985368780045.dkr.ecr.us-east-1.amazonaws.com/streaming-service]
1e5a4c89cee5: Waiting
2dfe1adbbfc7: Waiting
25ff2da83641: Waiting
9a05af971d61: Waiting
152ef5bae57e: Waiting
2fe098179e34: Waiting
dd71dde834b5: Waiting
f18232174bc9: Waiting
152ef5bae57e: Waiting
2fe098179e34: Waiting
dd71dde834b5: Waiting
f18232174bc9: Waiting
1e5a4c89cee5: Waiting
2dfe1adbbfc7: Waiting
25ff2da83641: Waiting
9a05af971d61: Waiting
9a05af971d61: Waiting
152ef5bae57e: Waiting
2fe098179e34: Waiting
dd71dde834b5: Waiting
f18232174bc9: Waiting
1e5a4c89cee5: Waiting
2dfe1adbbfc7: Waiting
25ff2da83641: Waiting
dd71dde834b5: Waiting
f18232174bc9: Waiting
1e5a4c89cee5: Waiting
2dfe1adbbfc7: Waiting
25ff2da83641: Waiting
9a05af971d61: Waiting
152ef5bae57e: Waiting
2fe098179e34: Waiting
2fe098179e34: Waiting
dd71dde834b5: Waiting
f18232174bc9: Waiting
1e5a4c89cee5: Waiting
2dfe1adbbfc7: Waiting
25ff2da83641: Waiting
9a05af971d61: Waiting
152ef5bae57e: Waiting
2fe098179e34: Waiting
dd71dde834b5: Waiting
f18232174bc9: Waiting
1e5a4c89cee5: Waiting
2dfe1adbbfc7: Waiting
25ff2da83641: Waiting
9a05af971d61: Waiting
152ef5bae57e: Waiting
2fe098179e34: Waiting
dd71dde834b5: Waiting
f18232174bc9: Waiting
1e5a4c89cee5: Waiting
2dfe1adbbfc7: Waiting
25ff2da83641: Waiting
9a05af971d61: Waiting
152ef5bae57e: Waiting
9a05af971d61: Waiting
152ef5bae57e: Waiting
2fe098179e34: Waiting
dd71dde834b5: Waiting
f18232174bc9: Waiting
1e5a4c89cee5: Waiting
2dfe1adbbfc7: Waiting
25ff2da83641: Waiting
25ff2da83641: Waiting
9a05af971d61: Waiting
152ef5bae57e: Waiting
2fe098179e34: Waiting
dd71dde834b5: Layer already exists
f18232174bc9: Waiting
1e5a4c89cee5: Waiting
2dfe1adbbfc7: Waiting
f18232174bc9: Layer already exists
1e5a4c89cee5: Layer already exists
2dfe1adbbfc7: Waiting
25ff2da83641: Layer already exists
9a05af971d61: Waiting
152ef5bae57e: Waiting
2fe098179e34: Waiting
9a05af971d61: Waiting
152ef5bae57e: Waiting
2fe098179e34: Waiting
2dfe1adbbfc7: Waiting
152ef5bae57e: Waiting
2dfe1adbbfc7: Waiting
9a05af971d61: Pushed
2dfe1adbbfc7: Pushed
152ef5bae57e: Pushed
2fe098179e34: Pushed
1.0.0: digest: sha256:ea990301c76fdb19f56563a1499a19937da71d42b52269993fe2eadf9902016a size: 2059
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Build & Push Admin Service)
[Pipeline] sh
+ docker build -t admin-service -f ./backend/adminService/Dockerfile ./backend
DEPRECATED: The legacy builder is deprecated and will be removed in a future release.
            Install the buildx component to build images with BuildKit:
            https://docs.docker.com/go/buildx/

Sending build context to Docker daemon  16.15MB

Step 1/8 : FROM node:18-alpine AS base
 ---> 8d6421d663b4
Step 2/8 : WORKDIR /app/adminService
 ---> Using cache
 ---> 82fe5ea84240
Step 3/8 : COPY adminService/package*.json ./
 ---> Using cache
 ---> 8402a00ac75d
Step 4/8 : RUN npm install --production
 ---> Using cache
 ---> 02b1dba6546a
Step 5/8 : COPY adminService/. ./
 ---> Using cache
 ---> ed09eb502099
Step 6/8 : ENV NODE_ENV=production
 ---> Using cache
 ---> 31e2ac172e64
Step 7/8 : EXPOSE 3003
 ---> Using cache
 ---> 004b561d0768
Step 8/8 : CMD ["npm", "run", "start"]
 ---> Using cache
 ---> 713d85da1125
Successfully built 713d85da1125
Successfully tagged admin-service:latest
[Pipeline] sh
+ docker tag admin-service:latest 985368780045.dkr.ecr.us-east-1.amazonaws.com/admin-service:1.0.0
[Pipeline] sh
+ docker push 985368780045.dkr.ecr.us-east-1.amazonaws.com/admin-service:1.0.0
The push refers to repository [985368780045.dkr.ecr.us-east-1.amazonaws.com/admin-service]
1fd2b9fa0e68: Waiting
25ff2da83641: Waiting
f18232174bc9: Waiting
cc71bf6b2532: Waiting
e272d24a05fc: Waiting
90807f21e39c: Waiting
dd71dde834b5: Waiting
1e5a4c89cee5: Waiting
90807f21e39c: Waiting
dd71dde834b5: Waiting
1e5a4c89cee5: Waiting
1fd2b9fa0e68: Waiting
25ff2da83641: Waiting
f18232174bc9: Waiting
cc71bf6b2532: Waiting
e272d24a05fc: Waiting
1e5a4c89cee5: Waiting
1fd2b9fa0e68: Waiting
25ff2da83641: Waiting
f18232174bc9: Waiting
cc71bf6b2532: Waiting
e272d24a05fc: Waiting
90807f21e39c: Waiting
dd71dde834b5: Waiting
e272d24a05fc: Waiting
90807f21e39c: Waiting
dd71dde834b5: Waiting
1e5a4c89cee5: Waiting
1fd2b9fa0e68: Waiting
25ff2da83641: Waiting
f18232174bc9: Waiting
cc71bf6b2532: Waiting
1e5a4c89cee5: Waiting
1fd2b9fa0e68: Waiting
25ff2da83641: Waiting
f18232174bc9: Waiting
cc71bf6b2532: Waiting
e272d24a05fc: Waiting
90807f21e39c: Waiting
dd71dde834b5: Waiting
90807f21e39c: Waiting
dd71dde834b5: Waiting
1e5a4c89cee5: Waiting
1fd2b9fa0e68: Waiting
25ff2da83641: Waiting
f18232174bc9: Waiting
cc71bf6b2532: Waiting
e272d24a05fc: Waiting
25ff2da83641: Waiting
f18232174bc9: Waiting
cc71bf6b2532: Waiting
e272d24a05fc: Waiting
90807f21e39c: Waiting
dd71dde834b5: Waiting
1e5a4c89cee5: Waiting
1fd2b9fa0e68: Waiting
dd71dde834b5: Waiting
1e5a4c89cee5: Waiting
1fd2b9fa0e68: Waiting
25ff2da83641: Waiting
f18232174bc9: Waiting
cc71bf6b2532: Waiting
e272d24a05fc: Waiting
90807f21e39c: Waiting
dd71dde834b5: Waiting
1e5a4c89cee5: Layer already exists
1fd2b9fa0e68: Waiting
25ff2da83641: Waiting
f18232174bc9: Waiting
cc71bf6b2532: Waiting
e272d24a05fc: Waiting
90807f21e39c: Waiting
1fd2b9fa0e68: Waiting
25ff2da83641: Layer already exists
f18232174bc9: Layer already exists
cc71bf6b2532: Waiting
e272d24a05fc: Waiting
90807f21e39c: Waiting
dd71dde834b5: Layer already exists
cc71bf6b2532: Waiting
e272d24a05fc: Waiting
90807f21e39c: Waiting
1fd2b9fa0e68: Waiting
1fd2b9fa0e68: Waiting
cc71bf6b2532: Waiting
cc71bf6b2532: Pushed
e272d24a05fc: Pushed
1fd2b9fa0e68: Pushed
90807f21e39c: Pushed
1.0.0: digest: sha256:713d85da11257e55324f3698972753837a8676c5d53aba04454761bd362466b1 size: 2059
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Build & Push Chat Service)
[Pipeline] sh
+ docker build -t chat-service -f ./backend/chatService/Dockerfile ./backend
DEPRECATED: The legacy builder is deprecated and will be removed in a future release.
            Install the buildx component to build images with BuildKit:
            https://docs.docker.com/go/buildx/

Sending build context to Docker daemon  16.15MB

Step 1/8 : FROM node:18-alpine AS base
 ---> 8d6421d663b4
Step 2/8 : WORKDIR /app/chatService
 ---> Using cache
 ---> 0ec0046abbc1
Step 3/8 : COPY chatService/package*.json ./
 ---> Using cache
 ---> 99ac96bdc78b
Step 4/8 : RUN npm install --production
 ---> Using cache
 ---> 0d85fa500d0f
Step 5/8 : COPY chatService/. ./
 ---> Using cache
 ---> 20de841050f7
Step 6/8 : ENV NODE_ENV=production
 ---> Using cache
 ---> 4a70a67e3f7c
Step 7/8 : EXPOSE 3004
 ---> Using cache
 ---> 2cd40cbdeb78
Step 8/8 : CMD ["npm", "run", "start"]
 ---> Using cache
 ---> 0d1210e13425
Successfully built 0d1210e13425
Successfully tagged chat-service:latest
[Pipeline] sh
+ docker tag chat-service:latest 985368780045.dkr.ecr.us-east-1.amazonaws.com/chat-service:1.0.0
[Pipeline] sh
+ docker push 985368780045.dkr.ecr.us-east-1.amazonaws.com/chat-service:1.0.0
The push refers to repository [985368780045.dkr.ecr.us-east-1.amazonaws.com/chat-service]
b02277ca30a1: Waiting
ab84a0f7a155: Waiting
f18232174bc9: Waiting
dd71dde834b5: Waiting
1e5a4c89cee5: Waiting
25ff2da83641: Waiting
410df76ef4c4: Waiting
7a793a15da6f: Waiting
410df76ef4c4: Waiting
7a793a15da6f: Waiting
b02277ca30a1: Waiting
ab84a0f7a155: Waiting
f18232174bc9: Waiting
dd71dde834b5: Waiting
1e5a4c89cee5: Waiting
25ff2da83641: Waiting
b02277ca30a1: Waiting
ab84a0f7a155: Waiting
f18232174bc9: Waiting
dd71dde834b5: Waiting
1e5a4c89cee5: Waiting
25ff2da83641: Waiting
410df76ef4c4: Waiting
7a793a15da6f: Waiting
b02277ca30a1: Waiting
ab84a0f7a155: Waiting
f18232174bc9: Waiting
dd71dde834b5: Waiting
1e5a4c89cee5: Waiting
25ff2da83641: Waiting
410df76ef4c4: Waiting
7a793a15da6f: Waiting
410df76ef4c4: Waiting
7a793a15da6f: Waiting
b02277ca30a1: Waiting
ab84a0f7a155: Waiting
f18232174bc9: Waiting
dd71dde834b5: Waiting
1e5a4c89cee5: Waiting
25ff2da83641: Waiting
410df76ef4c4: Waiting
7a793a15da6f: Waiting
b02277ca30a1: Waiting
ab84a0f7a155: Waiting
f18232174bc9: Waiting
dd71dde834b5: Waiting
1e5a4c89cee5: Waiting
25ff2da83641: Waiting
410df76ef4c4: Waiting
7a793a15da6f: Waiting
b02277ca30a1: Waiting
ab84a0f7a155: Waiting
f18232174bc9: Waiting
dd71dde834b5: Waiting
1e5a4c89cee5: Waiting
25ff2da83641: Waiting
7a793a15da6f: Waiting
b02277ca30a1: Waiting
ab84a0f7a155: Waiting
f18232174bc9: Waiting
dd71dde834b5: Waiting
1e5a4c89cee5: Waiting
25ff2da83641: Waiting
410df76ef4c4: Waiting
f18232174bc9: Waiting
dd71dde834b5: Waiting
1e5a4c89cee5: Layer already exists
25ff2da83641: Waiting
410df76ef4c4: Waiting
7a793a15da6f: Waiting
b02277ca30a1: Waiting
ab84a0f7a155: Waiting
dd71dde834b5: Layer already exists
25ff2da83641: Layer already exists
410df76ef4c4: Waiting
7a793a15da6f: Waiting
b02277ca30a1: Waiting
ab84a0f7a155: Waiting
f18232174bc9: Layer already exists
ab84a0f7a155: Waiting
410df76ef4c4: Waiting
7a793a15da6f: Waiting
b02277ca30a1: Waiting
410df76ef4c4: Waiting
7a793a15da6f: Waiting
b02277ca30a1: Waiting
ab84a0f7a155: Waiting
7a793a15da6f: Pushed
410df76ef4c4: Pushed
ab84a0f7a155: Pushed
b02277ca30a1: Pushed
1.0.0: digest: sha256:0d1210e134255be4a3835fc55a02cfad8e4802eaddbf93255b597e6d610ed7e2 size: 2058
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Build & Push Frontend)
[Pipeline] sh
+ docker build -t frontend-service ./frontend
DEPRECATED: The legacy builder is deprecated and will be removed in a future release.
            Install the buildx component to build images with BuildKit:
            https://docs.docker.com/go/buildx/

Sending build context to Docker daemon  967.7kB

Step 1/22 : FROM node:18-alpine AS build
 ---> 8d6421d663b4
Step 2/22 : WORKDIR /app
 ---> Using cache
 ---> a8c58e52590c
Step 3/22 : COPY package*.json ./
 ---> Using cache
 ---> bd777024c63c
Step 4/22 : RUN npm install
 ---> Using cache
 ---> c04caa2f33a7
Step 5/22 : COPY . .
 ---> Using cache
 ---> 46887c747928
Step 6/22 : ARG REACT_APP_AUTH_API_URL
 ---> Using cache
 ---> 725dbdc10656
Step 7/22 : ARG REACT_APP_STREAMING_API_URL
 ---> Using cache
 ---> c09d94392eb4
Step 8/22 : ARG REACT_APP_STREAMING_PUBLIC_URL
 ---> Using cache
 ---> cdf2adf0da2b
Step 9/22 : ARG REACT_APP_ADMIN_API_URL
 ---> Using cache
 ---> e453c1d02109
Step 10/22 : ARG REACT_APP_CHAT_API_URL
 ---> Using cache
 ---> 50b57e29f28c
Step 11/22 : ARG REACT_APP_CHAT_SOCKET_URL
 ---> Using cache
 ---> d0394924eb05
Step 12/22 : ENV REACT_APP_AUTH_API_URL=${REACT_APP_AUTH_API_URL}
 ---> Running in 9ad5a85e7a08
 ---> Removed intermediate container 9ad5a85e7a08
 ---> bdefac388fa6
Step 13/22 : ENV REACT_APP_STREAMING_API_URL=${REACT_APP_STREAMING_API_URL}
 ---> Running in f68828a411fc
 ---> Removed intermediate container f68828a411fc
 ---> 9f23f8f94c7f
Step 14/22 : ENV REACT_APP_STREAMING_PUBLIC_URL=${REACT_APP_STREAMING_PUBLIC_URL}
 ---> Running in 77d6fa3214e3
 ---> Removed intermediate container 77d6fa3214e3
 ---> 1531d06c74a7
Step 15/22 : ENV REACT_APP_ADMIN_API_URL=${REACT_APP_ADMIN_API_URL}
 ---> Running in 91a67fc8df5f
 ---> Removed intermediate container 91a67fc8df5f
 ---> fffe5e763ef5
Step 16/22 : ENV REACT_APP_CHAT_API_URL=${REACT_APP_CHAT_API_URL}
 ---> Running in dbc0fdb0c1c3
 ---> Removed intermediate container dbc0fdb0c1c3
 ---> f8490d7b53c8
Step 17/22 : ENV REACT_APP_CHAT_SOCKET_URL=${REACT_APP_CHAT_SOCKET_URL}
 ---> Running in 221bb5af21dc
 ---> Removed intermediate container 221bb5af21dc
 ---> 9a4e0066aaa7
Step 18/22 : RUN npm run build
 ---> Running in 39a74cf5d85d

> frontend@0.1.0 build
> react-scripts build

Creating an optimized production build...
 [91mBrowserslist: caniuse-lite is outdated. Please run:
  npx update-browserslist-db@latest
  Why you should do it regularly: https://github.com/browserslist/update-db#readme
 [0m [91m [0;33mOne of your dependencies, babel-preset-react-app, is importing the
"@babel/plugin-proposal-private-property-in-object" package without
declaring it in its dependencies. This is currently working because
"@babel/plugin-proposal-private-property-in-object" is already in your
node_modules folder for unrelated reasons, but it  [1mmay break at any time [0;33m.

babel-preset-react-app is part of the create-react-app project,  [1mwhich
is not maintianed anymore [0;33m. It is thus unlikely that this bug will
ever be fixed. Add "@babel/plugin-proposal-private-property-in-object" to
your devDependencies to work around this error. This will make this message
go away. [0m
  
 [0m [91mBrowserslist: caniuse-lite is outdated. Please run:
  npx update-browserslist-db@latest
  Why you should do it regularly: https://github.com/browserslist/update-db#readme
 [0mCompiled with warnings.

[eslint] 
src/contexts/AuthContext.js
  Line 41:6:  React Hook useEffect has a missing dependency: 'handleLogout'. Either include it or remove the dependency array  react-hooks/exhaustive-deps

src/pages/AdminDashboard.js
  Line 31:9:  'theme' is assigned a value but never used  no-unused-vars

Search for the keywords to learn more about each warning.
To ignore, add // eslint-disable-next-line to the line before.

File sizes after gzip:

  191.32 kB  build/static/js/main.eef51305.js
  2.44 kB    build/static/css/main.c78d62fe.css
  1.77 kB    build/static/js/453.d855a71b.chunk.js

The project was built assuming it is hosted at /.
You can control this with the homepage field in your package.json.

The build folder is ready to be deployed.
You may serve it with a static server:

  npm install -g serve
  serve -s build

Find out more about deployment here:

  https://cra.link/deployment

 ---> Removed intermediate container 39a74cf5d85d
 ---> b22eb528bef5
Step 19/22 : FROM nginx:1.27-alpine AS production
 ---> 65645c7bb6a0
Step 20/22 : COPY --from=build /app/build /usr/share/nginx/html
 ---> 5c4e07acf114
Step 21/22 : EXPOSE 80
 ---> Running in f44d135cdbe1
 ---> Removed intermediate container f44d135cdbe1
 ---> a0034fd16882
Step 22/22 : CMD ["nginx", "-g", "daemon off;"]
 ---> Running in 3d07c611718e
 ---> Removed intermediate container 3d07c611718e
 ---> 3f64b4b74066
Successfully built 3f64b4b74066
Successfully tagged frontend-service:latest
[Pipeline] sh
+ docker tag frontend-service:latest 985368780045.dkr.ecr.us-east-1.amazonaws.com/frontend-service:1.0.0
[Pipeline] sh
+ docker push 985368780045.dkr.ecr.us-east-1.amazonaws.com/frontend-service:1.0.0
The push refers to repository [985368780045.dkr.ecr.us-east-1.amazonaws.com/frontend-service]
b464cfdf2a63: Waiting
197eb75867ef: Waiting
39c2ddfd6010: Waiting
f18232174bc9: Waiting
61ca4f733c80: Waiting
81bd8ed7ec67: Waiting
34a64644b756: Waiting
5c11aa13f7b3: Waiting
d7e507024086: Waiting
5c11aa13f7b3: Waiting
d7e507024086: Waiting
b464cfdf2a63: Waiting
197eb75867ef: Waiting
39c2ddfd6010: Waiting
f18232174bc9: Waiting
61ca4f733c80: Waiting
81bd8ed7ec67: Waiting
34a64644b756: Waiting
f18232174bc9: Waiting
61ca4f733c80: Waiting
81bd8ed7ec67: Waiting
34a64644b756: Waiting
5c11aa13f7b3: Waiting
d7e507024086: Waiting
b464cfdf2a63: Waiting
197eb75867ef: Waiting
39c2ddfd6010: Waiting
61ca4f733c80: Waiting
81bd8ed7ec67: Waiting
34a64644b756: Waiting
5c11aa13f7b3: Waiting
d7e507024086: Waiting
b464cfdf2a63: Waiting
197eb75867ef: Waiting
39c2ddfd6010: Waiting
f18232174bc9: Waiting
5c11aa13f7b3: Waiting
d7e507024086: Waiting
b464cfdf2a63: Waiting
197eb75867ef: Waiting
39c2ddfd6010: Waiting
f18232174bc9: Waiting
61ca4f733c80: Waiting
81bd8ed7ec67: Waiting
34a64644b756: Waiting
34a64644b756: Waiting
5c11aa13f7b3: Waiting
d7e507024086: Waiting
b464cfdf2a63: Waiting
197eb75867ef: Waiting
39c2ddfd6010: Waiting
f18232174bc9: Waiting
61ca4f733c80: Waiting
81bd8ed7ec67: Waiting
f18232174bc9: Waiting
61ca4f733c80: Waiting
81bd8ed7ec67: Waiting
34a64644b756: Waiting
5c11aa13f7b3: Waiting
d7e507024086: Waiting
b464cfdf2a63: Waiting
197eb75867ef: Waiting
39c2ddfd6010: Waiting
39c2ddfd6010: Waiting
f18232174bc9: Waiting
61ca4f733c80: Waiting
81bd8ed7ec67: Waiting
34a64644b756: Waiting
5c11aa13f7b3: Waiting
d7e507024086: Waiting
b464cfdf2a63: Waiting
197eb75867ef: Waiting
f18232174bc9: Waiting
61ca4f733c80: Waiting
81bd8ed7ec67: Waiting
34a64644b756: Waiting
5c11aa13f7b3: Waiting
d7e507024086: Waiting
b464cfdf2a63: Waiting
197eb75867ef: Waiting
39c2ddfd6010: Waiting
61ca4f733c80: Layer already exists
81bd8ed7ec67: Layer already exists
34a64644b756: Waiting
5c11aa13f7b3: Waiting
d7e507024086: Layer already exists
b464cfdf2a63: Layer already exists
197eb75867ef: Layer already exists
39c2ddfd6010: Layer already exists
f18232174bc9: Layer already exists
34a64644b756: Waiting
5c11aa13f7b3: Waiting
34a64644b756: Layer already exists
5c11aa13f7b3: Waiting
5c11aa13f7b3: Pushed
1.0.0: digest: sha256:3f64b4b74066c8e60dbade8774ecbe590c0af2bc8f66a0ecab726aa85711c328 size: 2264
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Declarative: Post Actions)
[Pipeline] echo
CI/CD Pipeline Completed Successfully! All images pushed to ECR.
[Pipeline] }
[Pipeline] // stage
[Pipeline] }
[Pipeline] // withEnv
[Pipeline] }
[Pipeline] // withEnv
[Pipeline] }
[Pipeline] // node
[Pipeline] End of Pipeline
Finished: SUCCESS
Using the below command created the EKS Cluster:
PS D:\DevOps&MultiCloud\Assignment\27_Sep_2026\Streaming-App> eksctl create cluster --name streaming-app-cluster --region us-east-1 --node-type t3.small --nodes 4

<img width="975" height="202" alt="image" src="https://github.com/user-attachments/assets/6582586b-2663-4994-8f66-d51c50a2716c" />

Helm Chart Created:
<img width="676" height="69" alt="image" src="https://github.com/user-attachments/assets/e6f72327-fd1b-489d-8e56-
 9312b0f34619" />

 Updated the Configmap.yaml,Ingress.yaml,Mongodb.yaml,Services-clusterip.yaml and Services-deployment.yaml in Streamingapp\templates folder

Executed  helm install streaming-release ./streamingapp 
<img width="975" height="221" alt="image" src="https://github.com/user-attachments/assets/26152e31-c401-4733-831e-4472f52af462" />

<img width="975" height="103" alt="image" src="https://github.com/user-attachments/assets/48edbc7d-f63c-4672-aa08-
 50f28c8935a3" />

 PS D:\DevOps&MultiCloud\Assignment\27_Sep_2026\Streaming-App> kubectl get pods
 
 <img width="975" height="368" alt="image" src="https://github.com/user-attachments/assets/aad80a75-ec50-4329-a3ee-244f084e17ea" />
 <img width="975" height="82" alt="image" src="https://github.com/user-attachments/assets/1f90c237-8f1c-4b52-af06-2491a4afa04e" />

<img width="975" height="175" alt="image" src="https://github.com/user-attachments/assets/513e6daa-16b3-4005-9da0-f84c18b9418a" />

Updated the Ingress.yaml in Streaming-App\streamingapp folder

<img width="975" height="86" alt="image" src="https://github.com/user-attachments/assets/dfd700a0-4c91-468f-982c-fdddc328d714" />


[Uploading image.png…]()

used the reference article for creating "Ingress-nginx-controller"
Ref: https://medium.com/@dikkumburage/how-to-install-nginx-ingress-controller-93a375e8edde

PS D:\DevOps&MultiCloud\Assignment\27_Sep_2026\Streaming-App> helm install ingress-nginx ingress-nginx/ingress-nginx -f "D:\DevOps&MultiCloud\Assignment\27_Sep_2026\Streaming-App\values.yaml" -n ingress-nginx  
<img width="975" height="157" alt="image" src="https://github.com/user-attachments/assets/de1b8ad5-9b79-40d7-a3e6-d0a6de102c2b" />
<img width="975" height="87" alt="image" src="https://github.com/user-attachments/assets/61562e9d-303b-412d-a67b-92ca76d85a88" />

URL: http://a06039c8fa24d4fe494a3b5a0dc596ba-576829364.us-east-1.elb.amazonaws.com   - URL for Streamline:

<img width="975" height="486" alt="image" src="https://github.com/user-attachments/assets/e28f8410-53d6-4758-8005-f131eafe3cac" />

Tried deleting a pods and noticed a new pod gets created right immediately

<img width="975" height="410" alt="image" src="https://github.com/user-attachments/assets/661b78ee-4672-4e15-b006-11f0d00f0130" />


Successfully Completed the project.





















