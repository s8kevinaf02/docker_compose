
pipeline {
    agent any
    environment {
        DOCKER_HUB_USERNAME="s8kevinaf02"
        ALPHA_APPLICATION_01_REPO="alpha-application-01"
        ALPHA_APPLICATION_02_REPO="alpha-application-02"
        DOCKER_CREDENTIAL_ID = 'docker-hub-credentials'
    }
    parameters {
        string(name: 'BRANCH_NAME', defaultValue: 'main', description: '')
        string(name: 'APP1_TAG', defaultValue: 'latest', description: '')
        string(name: 'APP2_TAG', defaultValue: 'latest', description: '')
        string(name: 'PORT_ON_DOCKER_HOST', defaultValue: '', description: '')
    }
    stages {
        stage('Clone Repository') {
            steps {
                script {
                    git credentialsId: 'jenkins-agent-node',
                        url: 'https://github.com/s8kevinaf02/docker_compose.git',
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
                        docker build -t ${env.DOCKER_HUB_USERNAME}/${env.ALPHA_APPLICATION_01_REPO}:${params.APP1_TAG} .
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
                    withCredentials([usernamePassword(credentialsId: "docker-hub-credentials", 
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
        // stage('Login into s8marjorie DockerHub') {
        //     steps {
        //         script {
        //             sh """
        //                 
        //             """
        //         }
        //     }
        // }
        // stage('Pushing into s8marjorie DockerHub') {
        //     steps {
        //         script {
        //             sh """
        //                 docker tag devopseasylearning/alpha-application-01:app1.1.1.0 thedevopslady/alpha-application-01:app1.1.1.0

        //                 docker tag devopseasylearning/alpha-application-02:app2.1.1.0 thedevopslady/alpha-application-02:app2.1.1.0

        //                 docker push thedevopslady/alpha-application-01:app1.1.1.0
        //                 docker push thedevopslady/alpha-application-02:app2.1.1.0
        //             """
        //         }
        //     }
        // }
        // stage('Deploying the application') {
        //     steps {
        //         script {
        //             sh """
        //                 docker run -itd -p ${params.PORT_ON_DOCKER_HOST}:80 --name ${params.CONTAINER_NAME} ${params.IMAGE_NAME}-application-02
        //                 docker ps |grep ${params.CONTAINER_NAME}
        //             """ 
        //         }
        //     }
        // }
    }
}
