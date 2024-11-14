pipeline {
    agent any

    environment {
        registry = "827648740654.dkr.ecr.us-east-1.amazonaws.com/my-docker-repo"
        AWS_REGION = "us-east-1"
        CLUSTER_NAME = "demo-eks"
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/succefull2023/docker-spring-boot-for-the-author-.git',credentialsId:'github-pat']])
            }
        }
        
        stage ("Build JAR") {
            steps {
                sh "mvn clean install"
            }
        }
        
        stage ("Build Image") {
            steps {
                script {
                    docker.build registry
                }
            }
        }
        
        stage ("Push to ECR") {
            steps {
                script {
                    sh "aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 827648740654.dkr.ecr.us-east-1.amazonaws.com"
                    sh "docker push 827648740654.dkr.ecr.us-east-1.amazonaws.com/my-docker-repo:latest"
                    
                }
            }
         }
        
         stage ("Authenticate to EKS") {
             steps {
                     sh """
                     aws sts get-caller-identity
                    aws eks --region $AWS_REGION update-kubeconfig --name $CLUSTER_NAME
                    """
                 }
            }
                
        stage ("Helm install") {
            steps {
               script{
                    sh "helm upgrade --install mychart ./mychart --set image.repository=827648740654.dkr.ecr.us-east-1.amazonaws.com/my-docker-repo,image.tag=15 --namespace helm-deployment"




                }
            }
    }
}
}
