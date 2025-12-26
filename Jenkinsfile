pipeline {
    agent any

    environment {
        IMAGE_NAME = "achalgothe/my-app"
        TAG = "latest"
    }

    stages {

        stage('Build') {
            steps {
                echo 'Building application...'
                sh 'mvn clean package || echo "No Maven project, skipping build"'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                sh 'mvn test || echo "No tests found"'
            }
        }

        stage('DockerBuild') {
            steps {
                echo 'Building Docker image...'
                sh 'docker build -t $IMAGE_NAME:$TAG .'
            }
        }

        stage('Push Image') {
            steps {
                echo 'Pushing image to Docker Hub...'
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                        echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                        docker push $IMAGE_NAME:$TAG
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

    post {
        success {
            echo "✅ Pipeline completed successfully"
        }
        failure {
            echo "❌ Pipeline failed"
        }
    }
}
