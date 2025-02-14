pipeline {
    agent any

    environment {
        AWS_ACCESS_KEY_ID = credentials('aws-creds')   // AWS Credentials ID in Jenkins
        AWS_SECRET_ACCESS_KEY = credentials('aws-creds')
        AWS_REGION = "eu-north-1"
        APP_NAME = "Coimbatore-travels-1"
        ENV_NAME = "Test-jenkins-env"
        S3_BUCKET = "test-bucket-98941"   // Your S3 Bucket Name
    }

    stages {
        stage('Checkout Code') {
            steps {
                git 'https://github.com/vinothkumar-250/Coimbatore-travels-1.git'
            }
        }

        stage('Build WAR') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Prepare Version') {
            steps {
                script {
                    env.VERSION_LABEL = "build-${env.BUILD_NUMBER}"
                    echo "Deploying version: ${env.VERSION_LABEL}"
                }
            }
        }

        stage('Upload to S3') {
            steps {
                script {
                    sh """
                    aws s3 cp target/*.war s3://${S3_BUCKET}/${env.VERSION_LABEL}.war
                    """
                }
            }
        }

        stage('Create EB Version') {
            steps {
                script {
                    sh """
                    aws elasticbeanstalk create-application-version \
                        --application-name ${APP_NAME} \
                        --version-label ${env.VERSION_LABEL} \
                        --source-bundle S3Bucket="${S3_BUCKET}",S3Key="${env.VERSION_LABEL}.war" \
                        --region ${AWS_REGION}
                    """
                }
            }
        }

        stage('Deploy to AWS EB') {
            steps {
                script {
                    sh """
                    aws elasticbeanstalk update-environment \
                        --application-name ${APP_NAME} \
                        --environment-name ${ENV_NAME} \
                        --version-label ${env.VERSION_LABEL} \
                        --region ${AWS_REGION}
                    """
                }
            }
        }
    }
}
