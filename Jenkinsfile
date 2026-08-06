pipeline {

    agent any

    environment {
        IMAGE = "rajeshpatil1/nginx-demo"
        TAG = "${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/rajesh-patil1/git.git'
            }
        }

        stage('Build') {
            steps {
                sh "docker build -t ${IMAGE}:${TAG} ."
            }
        }

        stage('Login') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'DockeHub',
                        usernameVariable: 'USER',
                        passwordVariable: 'PASS'
                    )
                ]) {
                    sh '''
                    echo $PASS | docker login -u $USER --password-stdin
                    '''
                }
            }
        }

        stage('Push') {
            steps {
                sh """
                docker push ${IMAGE}:${TAG}
                docker tag ${IMAGE}:${TAG} ${IMAGE}:latest
                docker push ${IMAGE}:latest
                """
            }
        }

        stage('Deploy') {
            steps {
                sh """
                sed -i "s|image:.*|image: ${IMAGE}:${TAG}|g" k8s/deployment.yaml

                kubectl apply -f k8s/deployment.yaml
                kubectl apply -f k8s/nodeport.yaml
                kubectl apply -f k8s/HPA.yaml
                kubectl apply -f k8s/metrics-server.yaml
                """
            }
        }
    }
}
