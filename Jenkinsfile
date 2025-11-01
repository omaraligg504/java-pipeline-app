pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('DockerHub')
        IMAGE_NAME = "omaraligg/python-iti"
    }

    stages {
        stage('Check Build Number') {
            steps {
                script {
                    echo "🔢 Current build number: ${env.BUILD_NUMBER}"
                }
            }
        }

        stage('Build') {
            steps {
                echo "🏗️ Building Java application with Maven..."
                // Grant execute permission before running
                sh 'vn clean package'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "🐳 Building Docker image..."
                sh "docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} ."
            }
        }

        stage('Push Docker Image') {
            steps {
                echo "📤 Pushing image to DockerHub..."
                sh '''
                    echo "${DOCKERHUB_CREDENTIALS_PSW}" | docker login -u "${DOCKERHUB_CREDENTIALS_USR}" --password-stdin
                    docker push ${IMAGE_NAME}:${BUILD_NUMBER}
                '''
            }
        }

        stage('Deploy') {
            steps {
                echo "🚀 Deploying container..."
                sh "docker run -d -p 8081:8080 ${IMAGE_NAME}:${BUILD_NUMBER}"
            }
        }
    }

    post {
        always {
            echo "🧹 Cleaning workspace..."
            cleanWs()
        }
    }
}
