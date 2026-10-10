
pipeline {
    agent any

    stages {
        stage('Verify Checkout') {
            steps {
                echo 'NutriFlow repository checked out successfully'
                sh 'pwd'
                sh 'git rev-parse --short HEAD'
            }
        }

        stage('Inspect Project') {
            steps {
                sh 'ls -la'
            }
        }
    }

    post {
        success {
            echo 'NutriFlow CI test passed!'
        }
        failure {
            echo 'Check the Jenkins Console Output for errors.'
        }
    }
}
