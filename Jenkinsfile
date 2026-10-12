
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
                sh 'test -f sonar-project.properties'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('NutriFlow-SonarQube') {
                    withCredentials([string(
                        credentialsId: 'sonarqube-token',
                        variable: 'SONAR_AUTH_TOKEN'
                    )]) {
                        sh '''
                            /usr/local/bin/sonar-scanner \
                                -Dsonar.token="$SONAR_AUTH_TOKEN"
                        '''
                    }
                }
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

        stage('Push Images to Amazon ECR') {
            steps {
                sh '''
                    set -eu

                    AWS_REGION="ap-south-1"
                    AWS_ACCOUNT_ID="003720987833"
                    ECR_REGISTRY="${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com"

                    echo "Logging in to Amazon ECR..."
                    aws ecr get-login-password --region "$AWS_REGION" |
                        docker login --username AWS --password-stdin "$ECR_REGISTRY"

                    echo "Tagging backend image..."
                    docker tag nutriflow-backend:latest \
                        "$ECR_REGISTRY/nutriflow-backend:latest"

                    echo "Tagging frontend image..."
                    docker tag nutriflow-frontend:latest \
                        "$ECR_REGISTRY/nutriflow-frontend:latest"

                    echo "Pushing backend image..."
                    docker push "$ECR_REGISTRY/nutriflow-backend:latest"

                    echo "Pushing frontend image..."
                    docker push "$ECR_REGISTRY/nutriflow-frontend:latest"

                    echo "Both NutriFlow images pushed to ECR successfully!"
                '''
            }
        }

        stage('Verify ECR Images') {
            steps {
                sh '''
                    set -eu

                    aws ecr describe-images \
                        --repository-name nutriflow-backend \
                        --region ap-south-1

                    aws ecr describe-images \
                        --repository-name nutriflow-frontend \
                        --region ap-south-1
                '''
            }
        }
    }

    post {
        success {
            echo 'NutriFlow CI, SonarQube analysis, Docker builds, and ECR pushes passed!'
        }
        failure {
            echo 'Pipeline failed. Check the Jenkins Console Output.'
        }
    }
}

