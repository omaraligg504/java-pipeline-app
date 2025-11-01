pipeline {
    agent any

    stages {
        stage('Checkout Code') {
            steps {
                git 'https://github.com/Hassan-Eid-Hassan/python-iti.git'
            }
        }

        stage('Check Build Number') {
            steps {
                script {
                    if (env.BUILD_NUMBER.toInteger() < 5) {
                        error("Build number is less than 5 — failing intentionally.")
                    }
                }
            }
        }

        stage('Build') {
            steps {
                sh 'mvnw clean package'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t omaraligg/python-iti:${BUILD_NUMBER} .'
            }
        }

        stage('Push Docker Image') {
            steps {
                sh '''
                docker login -u omaraligg -p sergio_ramos-92:45
                docker push omaraligg/python-iti:${BUILD_NUMBER}
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh 'docker run -d -p 8081:8080 omaraligg/python-iti:${BUILD_NUMBER}'
            }
        }
    }

    post {
        always {
            echo "Cleaning workspace..."
            cleanWs()
        }
    }
}

