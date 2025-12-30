pipeline {
    agent any

    environment {
        // DockerHub details
        IMAGE_NAME = "achalgothe/frontend-agency"
        IMAGE_TAG  = "latest"

        // SonarQube
        SONAR_PROJECT_KEY = "frontend-agency"
        SONAR_PROJECT_NAME = "frontend-agency"
        SONAR_HOST_URL = "http://<SONARQUBE-IP>:9000"

        // Jenkins credentials IDs
        DOCKER_CREDS = "dockerhub-creds"
        SONAR_TOKEN  = "sonar-token"

        // Deployment
        EC2_USER = "ec2-user"
        EC2_HOST = "<EC2-PUBLIC-IP>"
    }

    stages {

        stage('Checkout Source Code') {
            steps {
                echo "📥 Checking out source code"
                git branch: 'main',
                    url: 'https://github.com/achalgothe/frontend-agency.git'
            }
        }

        stage('SonarQube Code Analysis') {
            steps {
                echo "🔍 Running SonarQube analysis"
                withSonarQubeEnv('SonarQube') {
                    sh """
                    sonar-scanner \
                    -Dsonar.projectKey=${SONAR_PROJECT_KEY} \
                    -Dsonar.projectName=${SONAR_PROJECT_NAME} \
                    -Dsonar.sources=. \
                    -Dsonar.host.url=${SONAR_HOST_URL} \
                    -Dsonar.login=${SONAR_TOKEN}
                    """
                }
            }
        }

        stage('Quality Gate Check') {
            steps {
                echo "🚦 Checking Quality Gate"
                timeout(time: 2, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Docker Build') {
            steps {
                echo "🐳 Building Docker image"
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
            }
        }

        stage('Docker Push') {
            steps {
                echo "📤 Pushing image to DockerHub"
                withCredentials([usernamePassword(
                    credentialsId: DOCKER_CREDS,
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh """
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    docker push ${IMAGE_NAME}:${IMAGE_TAG}
                    """
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                echo "🚀 Deploying application to EC2"
                sh """
                ssh -o StrictHostKeyChecking=no ${EC2_USER}@${EC2_HOST} << EOF
                  docker pull ${IMAGE_NAME}:${IMAGE_TAG}
                  docker stop frontend || true
                  docker rm frontend || true
                  docker run -d --name frontend -p 80:80 ${IMAGE_NAME}:${IMAGE_TAG}
                EOF
                """
            }
        }
    }

    post {
        success {
            echo "✅ CI/CD Pipeline completed successfully!"
        }
        failure {
            echo "❌ Pipeline failed! Check logs & SonarQube Quality Gate."
        }
    }
}
