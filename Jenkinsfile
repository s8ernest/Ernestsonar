pipeline {
    agent { label "SERVER02" } 
    environment {
        DOCKER_HUB_USERNAME="devopseasylearning"
        ALPHA_APPLICATION_01_REPO="alpha-application-01"
        ALPHA_APPLICATION_02_REPO="alpha-application-02"
        DOCKER_CREDENTIAL_ID = 's8-test-docker-hub-auth'
    }
    parameters {
        string(name: 'BRANCH_NAME', defaultValue: 'dev', description: 'add branch name')
        string(name: 'APP1_TAG', defaultValue: 'dev', description: 'add image name')
        string(name: 'APP2_TAG', defaultValue: 'latest', description: '')
        string(name: 'PORT_ON_DOCKER_HOST_APP_1', defaultValue: '', description: 'port number')
        string(name: 'PORT_ON_DOCKER_HOST_APP_2', defaultValue: '', description: 'port number')
    }
    stages {
        stage('Clone Repository') {
            steps {
                script {
                        url: 'https://github.com/s8ernest/Ernestsonar.git',
                        branch: "${params.BRANCH_NAME}"
                }
            }
        }
        stage('Checking the code') {
            steps {
                script {
                    sh """
                        ls -l
                    """ 
                }
            }
        }
        stage('Building application 01') {
            steps {
                script {
                    sh """
                        docker build -t "${env.DOCKER_HUB_USERNAME}/${env.ALPHA_APPLICATION_01_REPO}:${params.APP1_TAG}" -f application-01.Dockerfile .
                        docker images |grep ${params.APP1_TAG}
                    """ 
                }
            }
        }
        stage('Building application 02') {
            steps {
                script {
                    sh """
                        pwd
                        ls -l
                        docker build -t "${env.DOCKER_HUB_USERNAME}"/"${env.ALPHA_APPLICATION_02_REPO}":"${params.APP2_TAG}" -f application-02.Dockerfile .
                        docker images |grep ${params.APP2_TAG}
                    """ 
                }
            }
        }
        stage('Login into') {
            steps {
                script {
                    // Login to Docker Hub
                    withCredentials([usernamePassword(credentialsId: "s8-test-docker-hub-auth", 
                    usernameVariable: 'DOCKER_USERNAME', 
                    passwordVariable: 'DOCKER_PASSWORD')]) {
                        // Use Docker CLI to login
                        sh "docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD"
                    }
                }
            }
        }
        stage('Pushing application 01 into DockerHub') {
            steps {
                script {
                    sh """
                        docker push ${env.DOCKER_HUB_USERNAME}/${env.ALPHA_APPLICATION_01_REPO}:${params.APP1_TAG}
                    """
                }
            }
        }
        stage('Pushing application 02 into DockerHub') {
            steps {
                script {
                    sh """
                        docker push ${env.DOCKER_HUB_USERNAME}/${env.ALPHA_APPLICATION_02_REPO}:${params.APP2_TAG}
                    """
                }
            }
        }
        stage('Deploying the application 01') {
            steps {
                script {
                    sh """
                        docker run -itd -p ${params.PORT_ON_DOCKER_HOST_APP_1}:80  ${env.DOCKER_HUB_USERNAME}/${env.ALPHA_APPLICATION_01_REPO}:${params.APP1_TAG}
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
                        docker run -itd -p ${params.PORT_ON_DOCKER_HOST_APP_2}:80  ${env.DOCKER_HUB_USERNAME}/${env.ALPHA_APPLICATION_02_REPO}:${params.APP2_TAG}
                        sleep 5
                        docker ps 
                    """ 
                }
            }
        }
    }
}


pipeline {
    agent {
        label "master"
    }
    stages {
        stage('Displaying Environment Variables') {
            steps {
                echo "The build number is: ${BUILD_NUMBER}"
                echo "The job name is: ${JOB_NAME}"
                echo "The Jenkins home directory is: ${JENKINS_HOME}"
                echo "The Jenkins URL is: ${JENKINS_URL}"
                echo "The build URL is: ${BUILD_URL}"
                echo "The job URL is: ${JOB_URL}"
                echo "The workspace is: ${WORKSPACE}" 
                echo "BUILD_DISPLAY_NAME: ${BUILD_DISPLAY_NAME}" 
                // echo "BUILD_TIMESTAMP: ${BUILD_TIMESTAMP}" 
                echo "JOB_DISPLAY_URL: ${JOB_DISPLAY_URL}" 
                echo "BUILD_URL_ANCHOR: ${BUILD_URL}/console"  
                echo "WORKSPACE: ${WORKSPACE}"  
            }
        }
    }
}


pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh "printenv"
            }
        }
    }
}
