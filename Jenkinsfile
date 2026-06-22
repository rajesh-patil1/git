pipeline {

    agent any

    environment {

        GIT_REPO = 'https://github.com/Ajayrichard/Jenkinsandjava.git'

        AWS_REGION = 'us-east-1'

        IMAGE_NAME = 'jenkinsecr'

        ECR_URI = 'public.ecr.aws/k0c8q8z5/jenkinsecr'

        IMAGE_TAG = 'latest'
    }

    stages {

        stage('Clone GitHub') {
            steps {
                git branch: 'main',
                    url: "${GIT_REPO}"
            }
        }

        stage('Build Maven') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .
                '''
            }
        }

        stage('Login to Public ECR') {
            steps {
                sh '''
                aws ecr-public get-login-password --region us-east-1 \
                | docker login \
                --username AWS \
                --password-stdin public.ecr.aws
                '''
            }
        }

        stage('Tag Docker Image') {
            steps {
                sh '''
                docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${ECR_URI}:${IMAGE_TAG}
                '''
            }
        }

        stage('Push Image') {
            steps {
                sh '''
                docker push ${ECR_URI}:${IMAGE_TAG}
                '''
            }
        }

    }

    post {

        success {
            echo "Docker image pushed successfully."
        }

        failure {
            echo "Pipeline failed."
        }
    }

}
