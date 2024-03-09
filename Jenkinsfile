pipeline {
    agent any
    environment {
        AWS_ACCOUNT_ID= "453702019477"
        AWS_ACCESS_KEY_ID =  "AKIAWTIV7NGK2ODJOSGL"
        AWS_SECRET_ACCESS_KEY = "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"
        AWS_DEFAULT_REGION= "eu-west-1" 
        IMAGE_REPO_NAME="python-app"
        IMAGE_TAG="latest"
        REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"  
    }
    stages {
        stage('Logging into AWS ECR') {
            steps {
                script {
                    script {
    
                        def ecr_login = sh(script: "aws ecr get-login-password --region ${AWS_DEFAULT_REGION}", returnStdout: true).trim()
                        sh "docker login --username AWS --password ${ecr_login} ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com"
              }
            }
        }
        
        stage('Cloning Git') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://github.com/musaumakau/python-demoapp.git/']]])     
            }
        }
        
        // Building Docker images
        stage('Building image') {
            steps {
                script {
                    dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"
                }
            }
        }
   
        // Uploading Docker images into AWS ECR
        stage('Pushing to ECR') {
            steps {  
                script {
                    sh """docker tag ${IMAGE_REPO_NAME}:${IMAGE_TAG} ${REPOSITORY_URI}:$IMAGE_TAG"""
                    sh """docker push ${REPOSITORY_URI}:${IMAGE_TAG}"""
                }
            }
        }
        
        stage('Remove Docker Image') {
            steps {
                script {
                    // Remove the Docker image
                    sh "docker rmi ${IMAGE_REPO_NAME}:${IMAGE_TAG}"
                }
            }
        }
    }
}
