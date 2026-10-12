
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
                sh 'test -f backend/package.json'
                sh 'test -f frontend/package.json'
            }
        }

        stage('Build Backend Image') {
            steps {
                sh 'docker build -t nutriflow-backend:latest ./backend'
            }
        }

        stage('Build Frontend Image') {
            steps {
                sh 'docker build -t nutriflow-frontend:latest ./frontend'
            }
        }

        stage('Verify Docker Images') {
            steps {
                sh 'docker image inspect nutriflow-backend:latest'
                sh 'docker image inspect nutriflow-frontend:latest'
            }
        }
    }

    post {
        success {
            echo 'NutriFlow CI and Docker image builds passed!'
        }
        failure {
            echo 'Pipeline failed. Check the Jenkins Console Output.'
        }
    }
}

