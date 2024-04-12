
pipeline {
    agent any

    environment {
        DOCKER_HUB_USER = "${env.DOCKERHUB_USERNAME}" 
        DOCKER_HUB_PASSWORD = "${env.DOCKERHUB_PASSWORD}" 
        IMAGE_REPO_NAME = "python-app"
        IMAGE_TAG = "latest"
        DOCKERHUB_REPO = "5936/${IMAGE_REPO_NAME}"
        ECS_CLUSTER_NAME = "example-cluster"
        ECS_SERVICE_NAME = "python-app-service"
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

        stage('Pytest') {
            steps {
                sh("${JENKINS_HOME}/scripts/pytest.sh ${WORKSPACE}")
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
                sh 'echo "$DOCKER_HUB_PASSWORD" | docker login -u "$DOCKER_HUB_USER" --password-stdin'
            }
        }

        stage('Debug') {
            steps {
                script {
                    sh "echo DOCKERHUB_REPO: ${DOCKERHUB_REPO}"
                    sh "echo IMAGE_TAG: ${IMAGE_TAG}"
                }
            }
        }

        stage('Pushing to Docker Hub') {
            steps {
                sh "docker push ${DOCKERHUB_REPO}:${IMAGE_TAG}"
            }
        }
    stage('Deploy to ECS') {
            steps {
                script {
                    // Update ECS service with new Docker image
                    withAWS(credentials: '78c0c37d-ae65-44f6-ac4f-2c484b25afbb', region: 'eu-west-1') {
                        sh "aws ecs update-service --cluster ${ECS_CLUSTER_NAME} --service ${ECS_SERVICE_NAME} --force-new-deployment"
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
