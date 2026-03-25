pipeline {
    agent any
 
    environment {
        IMAGE_NAME     = "demo-app"
        CONTAINER_NAME = "demo-app-running"
    }
 
    stages {
 
        stage('Checkout') {
            steps {
                checkout scm
                echo "Code checked out"
            }
        }
 
        stage('Build') {
            steps {
                echo "Building Docker image..."
                sh "docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} ."
                sh "docker tag ${IMAGE_NAME}:${BUILD_NUMBER} ${IMAGE_NAME}:latest"
            }
        }
 
        stage('Test') {
             steps {
                   echo "Running tests..."
                   sh "docker run --rm ${IMAGE_NAME}:latest npm test"
            }
        } 
        stage('Deploy') {
            steps {
                echo "Deploying..."
                sh "docker stop ${CONTAINER_NAME} || true"
                sh "docker rm   ${CONTAINER_NAME} || true"
                sh """
                    docker run -d \
                      --name ${CONTAINER_NAME} \
                      -p 3000:3000 \
                      ${IMAGE_NAME}:latest
                """
                echo "Live at http://localhost:3000"
            }
        }
    }
 
    post {
        success { echo "All stages passed!" }
        failure { echo "Pipeline failed — check logs above" }
        always  { sh "docker image prune -f || true" }
    }
}
