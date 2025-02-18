pipeline {
    agent any

    environment {
        GIT_REPO = 'https://github.com/vinothkumar-250/Coimbatore-travels-1.git'
        BRANCH = 'main'
        AWS_REGION = 'eu-north-1'
        EB_APP_NAME = 'test'
        EB_ENV_NAME = 'test-env'
        S3_BUCKET_NAME = 'test-bucket-98941'
        VERSION_LABEL = "build-${env.BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    echo 'Cloning Git repository...'
                    git branch: "${BRANCH}", url: "${GIT_REPO}"
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    echo 'Building project using Maven...'
                    sh 'mvn clean install'  // Modify if using another build tool
                }
            }
        }

        stage('Upload to S3') {
            steps {
                script {
                    echo 'Uploading build artifacts to S3...'
                    sh "aws s3 cp target/*.jar s3://${S3_BUCKET_NAME}/${VERSION_LABEL}/"
                }
            }
        }

        stage('Deploy to Elastic Beanstalk') {
            steps {
                script {
                    echo 'Deploying to AWS Elastic Beanstalk...'
                    sh 'eb init -p tomcat8 --region ${AWS_REGION} ${EB_APP_NAME}'
                    sh 'eb use ${EB_ENV_NAME}'
                    sh "eb deploy --label ${VERSION_LABEL}"
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up workspace...'
            cleanWs()
        }
        success {
            echo 'Build and deployment were successful!'
        }
        failure {
            echo 'Build or deployment failed!'
        }
    }
}
