pipeline {
    agent any
    environment {
        DOCKER_HUB_USERNAME = "devopseasylearning"
        ALPHA_APPLICATION_01_REPO = "alpha-application-01"
        ALPHA_APPLICATION_02_REPO = "alpha-application-02"
        DOCKER_CREDENTIAL_ID = 's8-test-docker-hub-auth'
    }
    parameters {
        string(name: 'BRANCH_NAME', defaultValue: 'dev', description: 'Branch name')
        string(name: 'APP1_TAG', defaultValue: 'dev', description: 'Image name for Application 01')
        string(name: 'APP2_TAG', defaultValue: 'latest', description: 'Image name for Application 02')
        string(name: 'PORT_ON_DOCKER_HOST_APP_1', defaultValue: '', description: 'Port number for Application 01')
        string(name: 'PORT_ON_DOCKER_HOST_APP_2', defaultValue: '', description: 'Port number for Application 02')
    }
    stages {
        stage('Clone Repository') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: "${params.BRANCH_NAME}"]], userRemoteConfigs: [[url: 'https://github.com/s8ernest/Ernestsonar.git']]])
            }
        }
        stage('Checking the code') {
            steps {
                script {
                    sh "ls -l"
                }
            }
        }
        stage('Building application 01') {
            steps {
                script {
                    sh """
                        docker build -t "${env.DOCKER_HUB_USERNAME}/${env.ALPHA_APPLICATION_01_REPO}:${params.APP1_TAG}" -f application-01.Dockerfile .
                        docker images | grep ${params.APP1_TAG}
                    """
                }
            }
        }
        stage('Building application 02') {
            steps {
                script {
                    sh """
                        docker build -t "${env.DOCKER_HUB_USERNAME}/${env.ALPHA_APPLICATION_02_REPO}:${params.APP2_TAG}" -f application-02.Dockerfile .
                        docker images | grep ${params.APP2_TAG}
                    """
                }
            }
        }
        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: "${env.DOCKER_CREDENTIAL_ID}", usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    sh "docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD"
                }
            }
        }
        stage('Pushing application 01 to DockerHub') {
            steps {
                script {
                    sh "docker push ${env.DOCKER_HUB_USERNAME}/${env.ALPHA_APPLICATION_01_REPO}:${params.APP1_TAG}"
                }
            }
        }
        stage('Pushing application 02 to DockerHub') {
            steps {
                script {
                    sh "docker push ${env.DOCKER_HUB_USERNAME}/${env.ALPHA_APPLICATION_02_REPO}:${params.APP2_TAG}"
                }
            }
        }
        stage('Deploying the application 01') {
            steps {
                script {
                    sh """
                        docker run -itd -p ${params.PORT_ON_DOCKER_HOST_APP_1}:80 ${env.DOCKER_HUB_USERNAME}/${env.ALPHA_APPLICATION_01_REPO}:${params.APP1_TAG}
                        sleep 5
                        docker ps
                    """
                }
            }
        }
        stage('Deploying the application 02') {
            steps {
                script {
                    sh """
                        docker run -itd -p ${params.PORT_ON_DOCKER_HOST_APP_2}:80 ${env.DOCKER_HUB_USERNAME}/${env.ALPHA_APPLICATION_02_REPO}:${params.APP2_TAG}
                        sleep 5
                        docker ps
                    """
                }
            }
        }
    }
}
