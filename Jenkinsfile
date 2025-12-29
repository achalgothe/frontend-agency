pipeline {
    agent any

    tools {
        sonarScanner 'sonar-scanner'
    }

    stages {

        stage('SonarQube Analysis') {
            steps {
                echo "🔍 Running SonarQube analysis"
                withSonarQubeEnv('sonarqube') {
                    sh '''
                      sonar-scanner \
                      -Dsonar.projectKey=frontend-agency \
                      -Dsonar.projectName=frontend-agency \
                      -Dsonar.sources=src
                    '''
                }
            }
        }
    }

    post {
        failure {
            echo "❌ Pipeline failed"
        }
        success {
            echo "✅ Pipeline successful"
        }
    }
}
