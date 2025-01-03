pipeline {
    agent any
    environment{
        AWS_ACCESS_KEY='aws_credencials'
        SSH_KEY = credentials('d2250590-e41c-4157-9756-95f9ba817f08')
        DOCKER_HUB_CREDENTIALS= 'docker-hub-credentials'
        DOCKER_FRONTEND = 'safayavatsal/learnersreport_frontend'
        DOCKER_BACKEND = 'safayavatsal/learnersreport_backend'
       
    }

    stages {
        
        stage('CHECKOUT') {
            steps {
                echo 'clone the git code' 
                git branch: 'main', url:'https://github.com/safayavatsal/Orchestration.git'
            }
        }
        
        
        stage('build images') {
            parallel {
                stage('build backend') {
                    steps {
                        script {
                            docker.build("${env.DOCKER_BACKEND}:${env.BUILD_ID}", './learnerReportCS_backend/')
                            echo ("done")
                        }
                    }
                }
                stage('build frontend') {
                    steps {
                        script {
                             docker.build("${env.DOCKER_FRONTEND}:${env.BUILD_ID}", './learnerReportCS_backend/')
                             echo ("done")
                        }
                    }
                }
            }
        }
        
        stage('push to docker'){
            steps{
                script{
                    docker.withRegistry('https://index.docker.io/v1/', 'docker-hub-credentials') {
                        docker.image("${env.DOCKER_BACKEND}:${env.BUILD_ID}").push()
                        docker.image("${env.DOCKER_FRONTEND}:${env.BUILD_ID}").push()
                       echo ("done")
                    }
                }
            }
        }
        
        stage('eks connection'){
            steps{
                script{
                     withCredentials([aws(credentialsId: 'aws_credencials', region:'us-east-1')]) {
                        echo "login success"
                        def eksClusterExists = sh(script: 'aws eks describe-cluster --name poo-learnersreport-eks-cluster-1 --region us-east-1', 
                        returnStatus: true) == 0
                        if(!eksClusterExists)
                        {
                            sh '''
                            eksctl create cluster --name poo-learnersreport-eks-cluster-1 --region us-east-1 --nodegroup-name standard-workers --node-type t3.micro --nodes 2 --nodes-min 1 --nodes-max 3
                            '''
                        }
                        
                        sh "aws eks update-kubeconfig --name poo-learnersreport-eks-cluster-1 --region us-east-1"
                        sh "helm upgrade --install learnreport Learners_helm"
                        
                        
                       
                        
                    }
                }
            }
        }
        
        
        
    }
}
