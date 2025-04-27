pipeline {
    agent any

    environment {
        IMAGE_NAME = 'saiganesh1415/grocery-react-main-frontend:latest'
    }

    stages {
        // Stage 1: Clone the repository
        stage('Clone Repo') {
            steps {
                git branch: 'master', url: 'https://github.com/saiganesh1415/nn.git'
            }
        }

        // Stage 2: Build Docker images
        stage('Build Docker Images') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'github-token',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    script {
                        // Use escaped dollar signs to prevent Groovy interpolation of secrets
                        sh """
                            echo \$DOCKER_PASS | docker login -u \$DOCKER_USER --password-stdin
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
                sh 'docker-compose up -d --build'  // Build and start the containers in detached mode
            }
        }

        // Stage 4: Test Docker containers (check container status and logs)
        stage('Test Containers') {
            steps {
                sh 'docker ps'  // Check running containers
                sh 'docker-compose logs frontend'  // Check logs for the frontend service
            }
        }

        // Stage 5: Push Docker image to Docker Hub
        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    script {
                        def imageName = "${env.IMAGE_NAME}"  // Use environment variable for image name
                        sh """
                           // echo \$DOCKER_PASS | docker login -u \$DOCKER_USER --password-stdin
                            docker tag grocery-react-main-frontend:latest ${imageName}  // Tag image with the desired name 
                            docker push ${imageName}  // Push the image to Docker Hub
                            docker logout  // Logout after pushing
                        """
                    }
                }
            }
        }

        // Stage 6: Stop and remove Docker containers
        stage('Stop and Remove Containers') {
            steps {
                sh 'docker-compose down'  // Stops and removes containers, networks, and volumes created by Docker Compose
            }
        }
    }

    post {
        always {
            // Clean up any resources or log information after each pipeline run
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
