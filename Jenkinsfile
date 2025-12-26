pipeline {
    agent any

    environment {
        IMAGE_NAME = "achalgothe/frontend-agency"
    }

    stages {

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

        stage('Docker Build') {
            steps {
                echo "🐳 Building Docker image"
                sh 'docker build -t $IMAGE_NAME:latest .'
            }
        }

        stage('Push Image') {
            steps {
                echo "📤 Pushing image to DockerHub"
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    docker push $IMAGE_NAME:latest
                    '''
                }
            }
        }

        stage('Deploy') {
            steps {
                echo "🚀 Deploying on EC2"
                sh '''
                docker rm -f frontend || true
                docker run -d -p 8081:80 --name frontend $IMAGE_NAME:latest
                '''
            }
        }
    }

   
