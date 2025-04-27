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
                    docker build -t grocery-react-main-frontend .
                    docker logout
                """
            }
        }
        }
        }

        stage('Start Containers') {
            steps {
                sh 'docker-compose up -d'
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
                        def imageName = "grocery-react-main-frontend:latest" // or use "reactjs_portfolio:${env.BUILD_NUMBER}"
                        sh """
                            echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                            docker tag grocery-react-main-frontend:latest ${imageName}
                            docker push ${imageName}
                            docker logout
                        """
                    }
                }
            }
        }
    }

   
}
