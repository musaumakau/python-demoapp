 
pipeline {
    agent any
    environment {
    AWS_ACCOUNT_ID= 453702019477
    AWS_DEFAULT_REGION= "eu-west-1" 
    IMAGE_REPO_NAME="python-app"
    IMAGE_TAG="latest"
    REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"  
    }

 
   stages {
     stage('Logging into AWS ECR') {
      steps {
         script {
             sh "aws ecr get-login-password - region ${AWS_DEFAULT_REGION} | docker login -u AWS -p $(aws ecr get-login-password --region eu-west-1) 453702019477.dkr.ecr.eu-west-1.amazonaws.com/python-app
       }
 
    }
 }
 
     stage('Cloning Git') {
      steps {
        checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://gitlab.com/test-juan/python-demoapp.git']]]) 
    }
 }
 
  // Building Docker images
     stage('Building image') {
      steps{
        script {
          dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"
       }
    }
 }
 
    // Uploading Docker images into AWS ECR
    stage('Pushing to ECR') {
     steps{ 
        script {
            sh "docker tag ${IMAGE_REPO_NAME}:${IMAGE_TAG} ${REPOSITORY_URI}:$IMAGE_TAG"
            sh "docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${IMAGE_TAG}"
        }
        }
  }
 }
}
