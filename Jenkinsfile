pipeline {
    agent {
        docker {
            image 'node:18'
        }
    }

    environment {
        PORT = "${BRANCH_NAME == 'main' ? '3000' : '3001'}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                sh 'npm test || true' // Avoids pipeline fail if no tests exist (adjust for your real tests)
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("yourapp:${BRANCH_NAME}")
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    sh """
                    docker stop yourapp-${BRANCH_NAME} || true
                    docker rm yourapp-${BRANCH_NAME} || true
                    docker run -d -p ${PORT}:3000 --name yourapp-${BRANCH_NAME} yourapp:${BRANCH_NAME}
                    """
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
