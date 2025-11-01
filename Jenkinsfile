pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('DockerHub')  // The ID of your DockerHub credentials in Jenkins
        IMAGE_NAME = "omaraligg/python-iti"
    }

    stages {
        stage('Checkout Code') {
            steps {
                echo "📦 Cloning project repository..."
                git 'https://github.com/omaraligg504/java-pipeline-app.git'
            }
        }

        stage('Check Build Number') {
            steps {
                script {
                    echo "🔢 Current build number: ${env.BUILD_NUMBER}"
                    // if (env.BUILD_NUMBER.toInteger() < 5) {
                    //     error("❌ Build number is less than 5 — failing intentionally.")
                    // } else {
                    //     echo "✅ Build number is valid."
                    // }
                }
            }
        }

        stage('Build') {
            steps {
                echo "🏗️ Building Java application with Maven..."
                sh './mvnw clean package'
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
