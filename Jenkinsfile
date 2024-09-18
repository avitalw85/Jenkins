pipeline {
    agent any

    environment {
        // Define environment variables here if needed
        DOCKER_IMAGE_NAME = 'my-python-app'
        DOCKER_REGISTRY = 'my-docker-registry'
        K8S_NAMESPACE = 'default'
        K8S_DEPLOYMENT_NAME = 'my-python-app-deployment'
        K8S_SERVICE_NAME = 'my-python-app-service'
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    // Checkout the code from GitHub
                    checkout scm
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    // Build Docker image
                    bat '''
                        docker build -t %DOCKER_IMAGE_NAME% .
                        docker tag my-python-app my-docker-registry/my-python-app:latest
                        docker push my-docker-registry/my-python-app:latest
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Deploy to Kubernetes
                    bat '''
                        kubectl config set-context --current --namespace=%K8S_NAMESPACE%
                        kubectl set image deployment/%K8S_DEPLOYMENT_NAME% %K8S_DEPLOYMENT_NAME%=%DOCKER_REGISTRY%/%DOCKER_IMAGE_NAME%:latest
                        kubectl rollout status deployment/%K8S_DEPLOYMENT_NAME%
                    '''
                }
            }
        }

        stage('Expose Service') {
            steps {
                script {
                    // Expose service on port 443
                    bat '''
                        kubectl expose deployment %K8S_DEPLOYMENT_NAME% --type=LoadBalancer --name=%K8S_SERVICE_NAME% --port=443 --target-port=80
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
