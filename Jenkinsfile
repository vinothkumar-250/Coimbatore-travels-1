pipeline {
    agent any

    stages {
        stage('Manual Trigger') {
            steps {
                script {
                    input message: "Do you want to proceed with the build?", ok: "Yes, Proceed"
                }
            }
        }
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/vinothkumar-250/Coimbatore-travels-1.git'
            }
        }
        stage('Build') {
            steps {
                sh 'echo "Build stage: Add your build commands here"'
            }
        }
        stage('Test') {
            steps {
                sh 'echo "Test stage: Run unit tests here"'
            }
        }
    }
}
