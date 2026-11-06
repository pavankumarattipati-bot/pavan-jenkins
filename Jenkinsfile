pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Jenkins Docker Image') {
            steps {
                sh '''
                    docker build \
                    -t pavan-jenkins:${BUILD_NUMBER} \
                    -f jenkins-image/Dockerfile \
                    jenkins-image
                '''
            }
        }
    }

    post {
        success {
            echo 'Custom Jenkins Docker Image Built Successfully!'
        }

        failure {
            echo 'Docker Image Build Failed!'
        }
    }
}
