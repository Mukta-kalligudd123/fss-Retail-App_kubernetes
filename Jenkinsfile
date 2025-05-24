pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out code'
                git 'https://github.com/Mukta-kalligudd123/fss-Retail-App_kubernetes.git'
            }
        }

        stage('Build') {
            steps {
                echo 'Building...'
                // your build steps here
            }
        }
    }
}
