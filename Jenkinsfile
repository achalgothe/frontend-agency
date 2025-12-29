pipeline {
    agent any

    environment {
        IMAGE_NAME = "achalgothe/frontend-agency"
    }

    stages {

        stage('Checkout') {
            steps {
                echo "📥 Checkout source code"
                git branch: 'main',
                    url: 'https://github.com/achalgothe/frontend-agency.git',
                    credentialsId: 'github-creds'
            }
        }

        stage('Build') {
            steps {
                echo "📦 Build stage"
                sh 'ls -la'
            }
        }

        stage('Test') {
            steps {
                echo "🧪 Testing build output"
                sh 'test -f dist/index.html'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                echo "🔍 Running SonarQube analysis"
                withSonarQubeEnv('sonarqube') {
                    sh '''
                      echo "Using SonarScanner from: $SONAR_SCANNER_HOME"
                      export PATH=$PATH:$SONAR_SCANNER_HOME/bin

                      sonar-scanner \
                        -Dsonar.projectKey=frontend-agency \
                        -Dsonar.projectName=frontend-agency \
                        -Dsonar.sources=src
                    '''
                }
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

    }

    post {
        success {
            echo "✅ Pipeline successful"
        }
        failure {
            echo "❌ Pipeline failed"
        }
    }
}
