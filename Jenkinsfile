pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('docker-cred') // Jenkins DockerHub credentials ID
        DOCKER_IMAGE = "mukta178/frontend-app"
        IMAGE_TAG = "latest"
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out source code'
                git 'https://github.com/Mukta-kalligudd123/fss-Retail-App_kubernetes.git'
            }
        }

        stage('Build with Docker Compose') {
            steps {
                echo 'Building Docker images using docker-compose'
                sh 'docker-compose build'
            }
        }

        stage('Tag and Push Frontend Image') {
            steps {
                echo 'Tagging and pushing frontend-app image to Docker Hub'
                script {
                    // Tag the built frontend-app image with your Docker Hub repo and tag
                    sh "docker tag fss-retail-app_kubernetes_frontend-app ${DOCKER_IMAGE}:${IMAGE_TAG}"
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub-cred-id') {
                        sh "docker push ${DOCKER_IMAGE}:${IMAGE_TAG}"
                    }
                }
            }
        }

        // Optional stage to run the app using docker-compose (can be removed if not needed)
        stage('Run App (Optional)') {
            steps {
                echo 'Starting application with docker-compose up -d'
                sh 'docker-compose up -d'
            }
        }
    }
}
