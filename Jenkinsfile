pipeline {
    agent any

    environment {
        AWS_ACCESS_KEY_ID = credentials('aws-creds')   // AWS Credentials ID in Jenkins
        AWS_SECRET_ACCESS_KEY = credentials('aws-creds')
    }

    stages {
        stage('Checkout Code') {
            steps {
                git 'https://github.com/vinothkumar-250/Coimbatore-travels-1.git'
            }
        }

        stage('Build WAR') {
            steps {
                sh 'mvn clean package'
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

        stage('Deploy to AWS EB') {
            steps {
                script {
                    sh """
                    eb init Coimbatore-travels-1 --platform "Tomcat 10 with Corretto 21 running on 64bit Amazon Linux 2023" --region eu-north-1
                    eb deploy Test-jenkins-env --label ${env.VERSION_LABEL}
                    """
                }
            }
        }
    }
}
