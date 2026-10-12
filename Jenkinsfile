
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

