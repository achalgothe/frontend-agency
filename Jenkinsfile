pipeline {
    agent any

    environment {
        IMAGE_NAME = "achalgothe/my-app"
        IMAGE_TAG  = "latest"
        DOCKERHUB_CREDENTIALS = "dockerhub-creds"
    }

    stages {

        stage('Checkout') {
            steps {
                echo "Checking out code..."
                git url: 'https://github.com/achalgothe/my-docker-app.git', branch: 'main'
            }
        }

        stage('Build') {
            steps {
                echo "Building application..."
                sh 'echo "Build completed"'   // yaha actual build command dal sakte ho
            }
        }

        stage('Test') {
            steps {
                echo "Running tests..."
                sh 'echo "Tests passed"'     // yaha pytest / npm test / mvn test
            }
        }

        stage('Docker Build & Push') {
            steps {
                script {
                    echo "Building Docker image..."
                    sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."

                    echo "Pushing image to DockerHub..."
                    withCredentials([usernamePassword(
                        credentialsId: DOCKERHUB_CREDENTIALS,
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )]) {
                        sh """
                        echo \$DOCKER_PASS | docker login -u \$DOCKER_USER --password-stdin
                        docker push ${IMAGE_NAME}:${IMAGE_TAG}
                        """
                    }
                }
            }
        }

       stage('Deploy') {
    steps {
        echo "Deploying application..."
        sh '''
        docker stop my-app || true
        docker rm my-app || true
        docker run -d --name my-app -p 8080:80 achalgothe/my-app:latest
        '''
    }
}


    post {
        success {
            echo "✅ Pipeline completed successfully!"
        }
        failure {
            echo "❌ Pipeline failed. Check logs."
        }
        always {
            echo "🧹 Cleaning workspace..."
            cleanWs()
        }
    }
}
