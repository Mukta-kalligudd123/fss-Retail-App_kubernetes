pipeline {
    agent any

    environment {
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

        stage('Check Tools') {
            steps {
                sh 'aws --version'
                sh 'kubectl version --client'
                sh 'docker --version'
            }
        }

        stage('Push Frontend Image') {
            steps {
                echo 'Pushing frontend-app image to Docker Hub'
                withCredentials([
                    usernamePassword(
                        credentialsId: 'docker-cred',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push ${DOCKER_IMAGE}:${IMAGE_TAG}
                    '''
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
            usernamePassword(
                credentialsId: 'aws-eks-creds',
                usernameVariable: 'AWS_ACCESS_KEY_ID',
                passwordVariable: 'AWS_SECRET_ACCESS_KEY'
            )
        ]) {
            sh '''
                export AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID
                export AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY
                export AWS_DEFAULT_REGION=eu-west-1

                export KUBECONFIG=/tmp/kubeconfig
                aws eks update-kubeconfig --region eu-west-1 --name mukta-cluster --kubeconfig $KUBECONFIG

                # Optional: Check token generation
                aws eks get-token --region eu-west-1 --cluster-name mukta-cluster

                kubectl --kubeconfig=$KUBECONFIG get nodes

                kubectl --kubeconfig=$KUBECONFIG apply --validate=false -f mongodb-deployment.yml
                kubectl --kubeconfig=$KUBECONFIG apply --validate=false -f mongodb-service.yml
                kubectl --kubeconfig=$KUBECONFIG apply --validate=false -f usernode-js-service.yml
                kubectl --kubeconfig=$KUBECONFIG apply --validate=false -f userprofile-deployment.yml

                kubectl --kubeconfig=$KUBECONFIG rollout status deployment/mongodb || true
                kubectl --kubeconfig=$KUBECONFIG rollout status deployment/userprofile || true
            '''
        }
    }
}
    }
}
