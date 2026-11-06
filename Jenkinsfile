cat > Jenkinsfile <<'EOF'
pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-southeast-2'
        ECR_REPOSITORY = 'pavan-jenkins'
        AWS_ACCOUNT_ID = '770457184062'
        ECR_URI = '770457184062.dkr.ecr.ap-southeast-2.amazonaws.com/pavan-jenkins'
    }

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

        stage('Verify AWS Identity') {
            steps {
                sh '''
                    aws sts get-caller-identity
                '''
            }
        }

        stage('Login to AWS ECR') {
            steps {
                sh '''
                    aws ecr get-login-password \
                    --region "$AWS_REGION" |
                    docker login \
                    --username AWS \
                    --password-stdin "$ECR_URI"
                '''
            }
        }

        stage('Tag Docker Image') {
            steps {
                sh '''
                    docker tag \
                    pavan-jenkins:${BUILD_NUMBER} \
                    ${ECR_URI}:${BUILD_NUMBER}

                    docker tag \
                    pavan-jenkins:${BUILD_NUMBER} \
                    ${ECR_URI}:latest
                '''
            }
        }

        stage('Push Docker Image to ECR') {
            steps {
                sh '''
                    docker push ${ECR_URI}:${BUILD_NUMBER}

                    docker push ${ECR_URI}:latest
                '''
            }
        }
    }

    post {
        success {
            echo 'SUCCESS: Jenkins Docker image pushed to AWS ECR!'
        }

        failure {
            echo 'FAILED: Please check the Jenkins console output.'
        }
    }
}
EOF
