pipeline {
    agent any
    environment {
        AWS_ACCOUNT_ID = "<AWS_ACCOUNT_ID>"
        ECR_REPO = "my-nodejs-app"
        IMAGE_TAG = "latest"
        KUBECONFIG = credentials('kubeconfig')
    }
    stages {
        stage('Build') {
            steps {
                script {
                    sh "docker build -t $AWS_ACCOUNT_ID.dkr.ecr.us-east-1.amazonaws.com/$ECR_REPO:$IMAGE_TAG ."
                }
            }
        }
        stage('Push to ECR') {
            steps {
                script {
                    sh "aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin $AWS_ACCOUNT_ID.dkr.ecr.us-east-1.amazonaws.com"
                    sh "docker push $AWS_ACCOUNT_ID.dkr.ecr.us-east-1.amazonaws.com/$ECR_REPO:$IMAGE_TAG"
                }
            }
        }
        stage('Deploy with Helm') {
            steps {
                script {
                    sh "helm upgrade --install my-nodejs-app ./my-nodejs-chart --set image.tag=$IMAGE_TAG"
                }
            }
        }
    }
}
