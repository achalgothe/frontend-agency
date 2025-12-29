pipeline {
    agent any

    tools {
        sonarScanner 'sonar-scanner'
    }

    stage('SonarQube Analysis') {
    steps {
        echo "🔍 Running SonarQube analysis"
        withSonarQubeEnv('sonarqube') {
            sh '''
              export PATH=$PATH:/var/lib/jenkins/tools/hudson.plugins.sonar.SonarRunnerInstallation/sonar-scanner/bin
              sonar-scanner \
                -Dsonar.projectKey=frontend-agency \
                -Dsonar.projectName=frontend-agency \
                -Dsonar.sources=src
            '''
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
