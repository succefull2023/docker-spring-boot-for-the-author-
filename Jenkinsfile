pipeline {
    agent any

    environment {
        registry = "827648740654.dkr.ecr.us-east-1.amazonaws.com/my-docker-repo"
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
        // }
        
        // stage ("Helm package") {
        //     steps {
        //             sh "helm package springboot"
        //         }
            }
                
        stage ("Helm install") {
            steps {
                    sh "helm upgrade myrelease-21 springboot-0.1.0.tgz"
                }
            }
    }
}
