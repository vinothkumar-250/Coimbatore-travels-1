pipeline {
    agent any

    environment {
        GIT_REPO = 'https://github.com/vinothkumar-250/Coimbatore-travels-1.git'
        BRANCH = 'main'
        AWS_REGION = 'eu-north-1'
        EB_APP_NAME = 'test'
        EB_ENV_NAME = 'test-env'
        S3_BUCKET_NAME = 'test-bucket-98941'
        VERSION_LABEL = "build-${env.BUILD_NUMBER}-${env.currentBuild.number}-${env.JOB_NAME}"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: "${BRANCH}", url: "${GIT_REPO}"
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Upload to S3') {
            steps {
                sh "aws s3 cp target/*.jar s3://${S3_BUCKET_NAME}/app-${VERSION_LABEL}.jar"
            }
        }

        stage('Deploy to Elastic Beanstalk') {
            steps {
                sh """
                eb init -p tomcat8 --region ${AWS_REGION} ${EB_APP_NAME}
                eb use ${EB_ENV_NAME}
                aws elasticbeanstalk create-application-version \
                    --application-name ${EB_APP_NAME} \
                    --version-label "${VERSION_LABEL}" \
                    --source-bundle S3Bucket=${S3_BUCKET_NAME},S3Key=app-${VERSION_LABEL}.jar
                aws elasticbeanstalk update-environment \
                    --application-name ${EB_APP_NAME} \
                    --environment-name ${EB_ENV_NAME} \
                    --version-label "${VERSION_LABEL}"
                """
            }
        }
    }

    post {
        always {
            cleanWs()
        }
        success {
            echo 'Build and deployment successful!'
        }
        failure {
            echo 'Build or deployment failed!'
        }
    }
}
