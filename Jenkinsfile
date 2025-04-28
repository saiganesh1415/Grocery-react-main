pipeline {
    agent any

    environment {
        IMAGE_NAME = 'saiganesh1415/grocery-react-main-frontend:latest'
    }

    stages {
        // Stage 1: Install Docker CLI (optional if already installed)
        stage('Install Docker CLI') {
            steps {
                sh '''
                    if ! command -v docker &> /dev/null
                    then
                        echo "Docker not found, installing..."
                        apk update
                        apk add docker-cli
                    else
                        echo "Docker is already installed."
                    fi
                '''
            }
        }

        // Stage 2: Clone the repository
        stage('Clone Repo') {
            steps {
                git branch: 'main', url: 'https://github.com/saiganesh1415/Grocery-react-main.git'
            }
        }

        // Stage 3: Build Docker image
        stage('Build Docker Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'github-token',
                    usernameVariable: 'DOCKER_USERNAME',
                    passwordVariable: 'DOCKER_PASSWORD'
                )]) {
                    script {
                        sh '''
                            echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
                            docker build -t grocery-react-main-frontend .
                            docker logout
                        '''
                    }
                }
            }
        }

        // Stage 4: Start Docker containers
        stage('Start Containers') {
            steps {
                sh 'docker-compose up -d --build'
            }
        }

        // Stage 5: Test Docker containers
        stage('Test Containers') {
            steps {
                sh 'docker ps'
                sh 'docker-compose logs frontend'
            }
        }

        // Stage 6: Push Docker image to DockerHub
        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'github-token',
                    usernameVariable: 'DOCKER_USERNAME',
                    passwordVariable: 'DOCKER_PASSWORD'
                )]) {
                    script {
                        sh '''
                            echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
                            docker tag grocery-react-main-frontend:latest $IMAGE_NAME
                            docker push $IMAGE_NAME
                            docker logout
                        '''
                    }
                }
            }
        }

        // Stage 7: Stop and remove Docker containers
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
