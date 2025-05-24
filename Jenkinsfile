stage('Checkout') {
    steps {
        checkout([
            $class: 'GitSCM',
            branches: [[name: '*/master']],
            userRemoteConfigs: [[
                url: 'https://github.com/Mukta-kalligudd123/fss-Retail-App_kubernetes.git',
                credentialsId: 'github-pat' // the ID you used in Jenkins credentials
            ]]
        ])
    }
}
