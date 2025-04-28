pipeline {
    agent {
        docker {
            image 'docker:24.0.7'   // Use official Docker CLI image
            args '-v /var/run/docker.sock:/var/run/docker.sock'  // Allow access to host Docker
        }
    }

    environment {
        IMAGE_NAME = 'saiganesh1415/grocery-react-main-frontend:latest'
    }

    stages {
        // Stage 1: Clone the repository
        stage('Clone Repo') {
            steps {
                git branch: 'main', url: 'https://github.com/saiganesh1415/Grocery-react-main.git'
            }
        }

        // Stage 2: Build Docker images
        stage('Build Docker Images') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'github-token', // ID of Jenkins DockerHub credentials
                    usernameVariable: 'DOCKER_USERNAME',
                    passwordVariable: 'DOCKER_PASSWORD'
                )]) {
                    script {
                        sh """
                            echo \$DOCKER_PASSWORD | docker login -u \$DOCKER_USERNAME --password-stdin
                            docker build -t grocery-react-main-frontend .
                            docker logout
                        """
                    }
                }
            }
        }

        // Stage 3: Start Docker containers using Docker Compose
        stage('Start Containers') {
            steps {
                sh 'docker-compose up -d --build'
            }
        }

        // Stage 4: Test Docker containers
        stage('Test Containers') {
            steps {
                sh 'docker ps'
                sh 'docker-compose logs frontend'
            }
        }

        // Stage 5: Push Docker image to Docker Hub
        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'github-token',  // Same DockerHub credentials
                    usernameVariable: 'DOCKER_USERNAME',
                    passwordVariable: 'DOCKER_PASSWORD'
                )]) {
                    script {
                        sh """
                            echo \$DOCKER_PASSWORD | docker login -u \$DOCKER_USERNAME --password-stdin
                            docker tag grocery-react-main-frontend:latest ${IMAGE_NAME}
                            docker push ${IMAGE_NAME}
                            docker logout
                        """
                    }
                }
            }
        }

        // Stage 6: Stop and remove Docker containers
        stage('Stop and Remove Containers') {
            steps {
                sh 'docker-compose down'
            }
        }
    }

    post {
        always {
            echo "Cleaning up resources after pipeline execution."
        }
        success {
            echo "Pipeline completed successfully!"
        }
        failure {
            echo "Pipeline failed. Please check the logs above."
        }
    }
}
