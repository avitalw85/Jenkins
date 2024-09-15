pipeline {
    agent any

    environment {
        // Define environment variables here if needed
        DOCKER_IMAGE_NAME = 'python-app'
        DOCKER_REGISTRY = 'docker-registry'
        K8S_NAMESPACE = 'default'
        K8S_DEPLOYMENT_NAME = 'python-app-deployment'
        K8S_SERVICE_NAME = 'python-app-service'
    }

    stages {
        stage('Build') {
            steps {
                script {
                    // Checkout the code from GitHub
                    checkout scm

                    // Build Docker image
                    sh '''
                        docker build -t $DOCKER_IMAGE_NAME .
                        docker tag $DOCKER_IMAGE_NAME $DOCKER_REGISTRY/$DOCKER_IMAGE_NAME:latest
                        docker push $DOCKER_REGISTRY/$DOCKER_IMAGE_NAME:latest
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Deploy to Kubernetes
                    sh '''
                        kubectl config set-context --current --namespace=$K8S_NAMESPACE
                        kubectl set image deployment/$K8S_DEPLOYMENT_NAME $K8S_DEPLOYMENT_NAME=$DOCKER_REGISTRY/$DOCKER_IMAGE_NAME:latest
                        kubectl rollout status deployment/$K8S_DEPLOYMENT_NAME
                    '''
                }
            }
        }

        stage('Expose Service') {
            steps {
                script {
                    // Expose service on port 443
                    sh '''
                        kubectl expose deployment $K8S_DEPLOYMENT_NAME --type=LoadBalancer --name=$K8S_SERVICE_NAME --port=443 --target-port=80
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
