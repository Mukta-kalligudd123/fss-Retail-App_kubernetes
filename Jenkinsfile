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

                # Debug: show current kube config and context
                kubectl config view
                kubectl config current-context

                # Check nodes connectivity
                kubectl get nodes

                # Apply Kubernetes manifests without validation
                kubectl apply --validate=false -f mongodb-deployment.yml
                kubectl apply --validate=false -f mongodb-service.yml
                kubectl apply --validate=false -f usernode-js-service.yml
                kubectl apply --validate=false -f userprofile-deployment.yml

                # Wait for deployments to roll out
                kubectl rollout status deployment/mongodb || true
                kubectl rollout status deployment/userprofile || true
            '''
        }
    }
}
    }
}
