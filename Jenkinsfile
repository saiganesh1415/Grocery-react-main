pipeline {
    agent any

    environment {
        IMAGE_NAME = 'grocery-react-main-frontend:latest'
    }

    stages {
        stage('Clone Repo') {
            steps {
                git branch: 'main', url: 'https://github.com/saiganesh1415/nn.git'
            }
        }

        stage('Build Docker Images') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    script {
                        sh """
                            echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                            docker build -t $IMAGE_NAME .
                        """
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
                        def dockerHubImage = "${DOCKER_USER}/grocery-react-main-frontend:latest"
                        sh """
                            echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                            docker tag $IMAGE_NAME ${dockerHubImage}
                            docker push ${dockerHubImage}
                            docker logout
                        """
                    }
                }
            }
        }
    }
}
