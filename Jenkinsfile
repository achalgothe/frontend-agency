pipeline {
    agent any

    environment {
        IMAGE_NAME = "achalgothe/frontend-agency"
        IMAGE_TAG  = "latest"
    }

    stages {

        stage('Build') {
            steps {
                echo '📦 Build stage (static frontend)'
                sh 'ls -la'
            }
        }

        stage('Test') {
            steps {
                echo '🧪 Testing frontend files'
                sh 'test -f index.html'
            }
        }

        stage('Docker Build') {
            steps {
                echo '🐳 Building Docker image'
                sh 'docker build -t $IMAGE_NAME:$IMAGE_TAG .'
            }
        }

        stage('Push Image') {
            steps {
                echo '📤 Pushing image to DockerHub'
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    docker push $IMAGE_NAME:$IMAGE_TAG
                    '''
                }
            }
        }

        stage('Deploy') {
            steps {
                echo '🚀 Deploying container on EC2'
                sh '''
                docker rm -f frontend || true
                docker run -d -p 80:80 --name frontend $IMAGE_NAME:$IMAGE_TAG
                '''
            }
        }
    }

    post {
        success {
            echo '✅ All 5 stages completed successfully'
        }
        failure {
            echo '❌ Pipeline failed'
        }
    }
}
