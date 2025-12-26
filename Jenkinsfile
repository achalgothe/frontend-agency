pipeline {
    agent any

    environment {
        IMAGE_NAME = "achalgothe/frontend-agency"
        IMAGE_TAG  = "latest"
    }

    stages {

        stage('Checkout Source Code') {
            steps {
                echo '📥 Cloning GitHub repository...'
                git url: 'https://github.com/StartBootstrap/startbootstrap-agency.git',
                    branch: 'master'
            }
        }

        stage('Verify Template') {
            steps {
                echo '🔍 Verifying static frontend files...'
                sh 'ls -la'
                sh 'test -f index.html'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo '🐳 Building Docker image...'
                sh '''
                docker build -t $IMAGE_NAME:$IMAGE_TAG .
                '''
            }
        }

        stage('Push Docker Image') {
            steps {
                echo '📤 Pushing image to DockerHub...'
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

        stage('Deploy on EC2') {
            steps {
                echo '🚀 Deploying container on EC2...'
                sh '''
                docker rm -f frontend || true
                docker run -d -p 80:80 --name frontend $IMAGE_NAME:$IMAGE_TAG
                '''
            }
        }
    }

    post {
        success {
            echo '✅ CI/CD Pipeline completed successfully!'
        }
        failure {
            echo '❌ Pipeline failed. Check logs.'
        }
    }
}
