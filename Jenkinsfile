pipeline {
    agent any

    environment {
        IMAGE_NAME = 'saiganesh1415/grocery-react-main-frontend:latest'
    }

    stages {
        stage('Clone Repo') {
            steps {
                git branch: 'main', url: 'https://github.com/saiganesh1415/Grocery-react-main.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials',  // Use Docker Hub credentials, not GitHub
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    script {
                        sh '''
                            echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                            docker build -t grocery-react-main-frontend .
                            docker logout
                        '''
                    }
                }
            }
        }

        stage('Start Containers') {
            steps {
                sh 'docker-compose up -d --build'
            }
        }

        stage('Test Containers') {
            steps {
                sh 'docker ps'
                sh 'docker-compose logs frontend'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    script {
                        sh """
                            echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                            docker tag grocery-react-main-frontend:latest $IMAGE_NAME
                            docker push $IMAGE_NAME
                            docker logout
                        """
                    }
                }
            }
        }

        stage('Stop and Remove Containers') {
            steps {
                sh 'docker-compose down'
            }
        }
    }

    post {
        always {
            echo "Cleaning up after the build."
        }
        success {
            echo "Pipeline executed successfully!"
        }
        failure {
            echo "Pipeline failed. Please check the logs."
        }
    }
}

