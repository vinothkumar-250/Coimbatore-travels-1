pipeline {
    agent any

    environment {
        AWS_ACCESS_KEY_ID = credentials('aws-creds')
        AWS_SECRET_ACCESS_KEY = credentials('aws-creds')
        AWS_REGION = "eu-north-1"
        APP_NAME = "Coimbatore-travels-1"
        ENV_NAME = "Test-jenkins-env"
        ZIP_FILE = "application.zip"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git 'https://github.com/vinothkumar-250/Coimbatore-travels-1.git'
            }
        }

        stage('Create Deployment Package') {
            steps {
                script {
                    sh "zip -r ${ZIP_FILE} . -x '*.git*' 'Jenkinsfile'"
                }
            }
        }

        stage('Deploy to AWS EB') {
            steps {
                script {
                    sh """
                    aws elasticbeanstalk create-application-version \
                        --application-name ${APP_NAME} \
                        --version-label "build-${env.BUILD_NUMBER}" \
                        --source-bundle S3Bucket="elasticbeanstalk-${AWS_REGION}-${APP_NAME}",S3Key="${ZIP_FILE}" \
                        --region ${AWS_REGION}

                    aws elasticbeanstalk update-environment \
                        --application-name ${APP_NAME} \
                        --environment-name ${ENV_NAME} \
                        --version-label "build-${env.BUILD_NUMBER}" \
                        --region ${AWS_REGION}
                    """
                }
            }
        }
    }

    post {
        success {
            echo "Deployment successful!"
        }
        failure {
            echo "Deployment failed! Check logs."
        }
    }
}
