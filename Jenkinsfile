pipeline {
    agent any
    environment {
        SSH_KEY = credentials('d2250590-e41c-4157-9756-95f9ba817f08')
        DOCKER_HUB_CREDENTIALS = credentials('docker-hub-credentials')
        DOCKER_FRONTEND = 'safayavatsal/learnersreport_frontend'
        DOCKER_BACKEND = 'safayavatsal/learnersreport_backend'
    }

    stages {
        stage('CHECKOUT') {
            steps {
                echo 'Cloning the Git repository'
                git branch: 'main', url: 'https://github.com/safayavatsal/Orchestration.git'
            }
        }

        stage('Build Images') {
            parallel {
                stage('Build Backend') {
                    steps {
                        script {
                            docker.build("${DOCKER_BACKEND}:${BUILD_ID}", './learnerReportCS_backend/')
                            echo 'Backend build completed'
                        }
                    }
                }
                stage('Build Frontend') {
                    steps {
                        script {
                            docker.build("${DOCKER_FRONTEND}:${BUILD_ID}", './learnerReportCS_frontend/')
                            echo 'Frontend build completed'
                        }
                    }
                }
            }
        }

        stage('Push to Docker') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', "${DOCKER_HUB_CREDENTIALS}") {
                        docker.image("${DOCKER_BACKEND}:${BUILD_ID}").push()
                        docker.image("${DOCKER_FRONTEND}:${BUILD_ID}").push()
                        echo 'Docker images pushed successfully'
                    }
                }
            }
        }

        stage('EKS Connection') {
            steps {
                script {
                    withAWS(credentials: 'aws_credencials', region: 'us-east-1') {
                        echo 'AWS login successful'
                        def eksClusterExists = sh(
                            script: 'aws eks describe-cluster --name poo-learnersreport-eks-cluster-1 --region us-east-1',
                            returnStatus: true
                        ) == 0

                        if (!eksClusterExists) {
                            echo 'EKS cluster does not exist. Creating a new cluster...'
                            sh '''
                                eksctl create cluster --name poo-learnersreport-eks-cluster-1 \
                                --region us-east-1 --nodegroup-name standard-workers \
                                --node-type t3.micro --nodes 2 --nodes-min 1 --nodes-max 3
                            '''
                        }

                        sh 'aws eks update-kubeconfig --name poo-learnersreport-eks-cluster-1 --region us-east-1'
                        sh 'helm upgrade --install learnreport ./Learners_helm'
                        echo 'EKS deployment completed'
                    }
                }
            }
        }
    }
}
