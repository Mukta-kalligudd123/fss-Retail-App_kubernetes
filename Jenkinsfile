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
        withCredentials([
            usernamePassword(credentialsId: 'aws-eks-creds', usernameVariable: 'AWS_ACCESS_KEY_ID', passwordVariable: 'AWS_SECRET_ACCESS_KEY'),
            file(credentialsId: 'kubeconfig-cred', variable: 'KUBECONFIG')
        ]) {
            sh '''
                export AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID
                export AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY
                export AWS_DEFAULT_REGION=eu-west-1

                export KUBECONFIG=$KUBECONFIG

                # Check cluster connectivity
                kubectl config view
                kubectl get nodes

                # Apply Kubernetes manifests
                kubectl apply --validate=false -f mongodb-deployment.yml
                kubectl apply --validate=false -f mongodb-service.yml
                kubectl apply --validate=false -f usernode-js-service.yml
                kubectl apply --validate=false -f userprofile-deployment.yml

                # Wait for rollouts
                kubectl rollout status deployment/mongodb || true
                kubectl rollout status deployment/userprofile || true
            '''
        }
    }
}
    }
}
