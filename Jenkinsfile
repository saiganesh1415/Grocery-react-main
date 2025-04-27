pipeline {
    agent any

    environment {
        IMAGE_NAME = 'grocery-react-main-frontend-1'
        DOCKER_REPO = 'saiganesh1415/grocery-react-main-frontend-1'
    }

    stages {
        stage('Clone Repo') {
            steps {
                git branch: 'main', url: 'https://github.com/saiganesh1415/nn.git'
            }
        }

        stage('Build Docker Images') {
            steps {
                script {
                    // Building the Docker image
                    sh """
                        docker build -t $IMAGE_NAME .
                    """
                }
            }
        }

        stage('Start Containers') {
            steps {
                // Running containers in detached mode
                sh 'docker-compose up -d --build'
            }
        }

        stage('Test Containers') {
            steps {
                // Checking running containers
                sh 'docker ps'
                // View frontend logs
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
                        // Docker login, tag, and push to Docker Hub
                        def dockerHubImage = "${DOCKER_REPO}:latest"
                        sh """
                            echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                            docker tag $IMAGE_NAME $dockerHubImage
                            docker push $dockerHubImage
                            docker logout
                        """
                    }
                }
            }
        }
    }
}
