pipeline {
    agent any

    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    // Build Docker image with a unique tag based on build number
                    dockerImage = docker.build("wilsonbolledula/maven:${env.BUILD_NUMBER}", ".")
                }
            }
        }

        stage('Push to DockerHub') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        // Push Docker image to Docker Hub
                        dockerImage.push()
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Deploy the Docker image to Kubernetes
                    echo 'Deploying application to Kubernetes...'

                    // Use the dynamically tagged image for deployment
                    def imageName = "wilsonbolledula/maven:${env.BUILD_NUMBER}"

                    // Apply the Kubernetes deployment YAML
                    bat 'kubectl apply -f node-app-deployment.yaml'
                    
                    // Set the image in the Kubernetes deployment
                    bat "kubectl set image deployment/my-deployment my-container=${imageName} --record"
                }
            }
        }
    }
}