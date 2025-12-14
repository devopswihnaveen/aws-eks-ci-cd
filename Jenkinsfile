pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1'
        ECR_REGISTRY = credentials('ecr-registry')
        EKS_CLUSTER = 'my-eks-cluster'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/devopswihnaveen/JavaApp.git'
            }
        }

        stage('Build') {
            steps {
                script {
                    sh 'docker build -t my-app:${BUILD_NUMBER} .'
                }
            }
        }

        stage('Push to ECR') {
            steps {
                script {
                    sh '''
                        aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${ECR_REGISTRY}
                        docker tag my-app:${BUILD_NUMBER} ${ECR_REGISTRY}/my-app:${BUILD_NUMBER}
                        docker push ${ECR_REGISTRY}/my-app:${BUILD_NUMBER}
                    '''
                }
            }
        }

        stage('Deploy to EKS') {
            steps {
                script {
                    sh '''
                        aws eks update-kubeconfig --name ${EKS_CLUSTER} --region ${AWS_REGION}
                        kubectl set image deployment/my-app my-app=${ECR_REGISTRY}/my-app:${BUILD_NUMBER}
                    '''
                }
            }
        }
    }
}