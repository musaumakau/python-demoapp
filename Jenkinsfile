pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('24ba2201-0af5-43cf-9150-50a5a2576379')  // Replace with your Docker Hub credentials ID
        IMAGE_REPO_NAME = "python-app"
        IMAGE_TAG = "latest"
        DOCKERHUB_REPO = "5936/${IMAGE_REPO_NAME}"  // Replace with your Docker Hub username
    }

    stages {
        stage('Cloning Git') {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/main']],
                    doGenerateSubmoduleConfigurations: false,
                    extensions: [],
                    submoduleCfg: [],
                    userRemoteConfigs: [[
                        credentialsId: '',
                        url: 'https://github.com/musaumakau/python-demoapp.git/'
                    ]]
                ])
            }
        }

        stage('Building image') {
            steps {
                script {
                    dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"
                }
            }
        }

        stage('Tagging image') {
            steps {
                script {
                    sh "docker tag ${IMAGE_REPO_NAME}:${IMAGE_TAG} ${DOCKERHUB_REPO}:${IMAGE_TAG}"
                }
            }
        }

        stage('Logging into Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', DOCKERHUB_CREDENTIALS) {
                        sh 'echo "Logged in to Docker Hub"'
                    }
                }
            }
        }

        stage('Pushing to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', DOCKERHUB_CREDENTIALS) {
                        sh "docker push ${DOCKERHUB_REPO}:${IMAGE_TAG}"
                    }
                }
            }
        }

        stage('Remove Docker Image') {
            steps {
                script {
                    sh "docker rmi ${IMAGE_REPO_NAME}:${IMAGE_TAG}"
                }
            }
        }
    }
}
