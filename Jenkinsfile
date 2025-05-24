pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('docker-cred') // Jenkins DockerHub credentials ID
        DOCKER_IMAGE = "mukta178/frontend-app"
        IMAGE_TAG = "latest"
        KUBECONFIG_CREDENTIALS = credentials('kubeconfig-cred') // Kubeconfig as Jenkins secret file
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

        stage('Push Frontend Image') {
            steps {
                echo 'Pushing frontend-app image to Docker Hub'
                script {
                    sh "echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin"
                    sh "docker push ${DOCKER_IMAGE}:${IMAGE_TAG}"
                }
            }
        }

        stage('Run App (Optional)') {
            steps {
                echo 'Starting application with docker-compose up -d'
                sh 'docker-compose up -d'
            }
        }

        stage('Deploy to Kubernetes') {
    steps {
        echo 'Deploying resources to Kubernetes'
        withCredentials([file(credentialsId: 'kubeconfig-cred', variable: 'KUBECONFIG')]) {
            sh '''
                export KUBECONFIG=$KUBECONFIG
                kubectl apply -f mongodb-deployment.yml
                kubectl apply -f mongodb-service.yml
                kubectl apply -f usernode-js-service.yml
                kubectl apply -f userprofile-deployment.yml

                kubectl rollout status deployment/mongodb || true
                kubectl rollout status deployment/userprofile || true
            '''
        }
    }
}

    }
}
