pipeline {
    agent any

    environment {
        IMAGE_NAME = 'pavansai2205/portfolio_docker-frontend:latest'
    }

    stages {
        stage('Clone Repo') {
            steps {
                git branch: 'master', url: 'https://github.com/pavansai2205/reactjs-project.git'
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
                    docker build -t portfolio_docker-frontend .
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
                        def imageName = "pavansai2205/reactjs_portfolio:latest" // or use "reactjs_portfolio:${env.BUILD_NUMBER}"
                        sh """
                            echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                            docker tag portfolio_docker-frontend:latest ${imageName}
                            docker push ${imageName}
                            docker logout
                        """
                    }
                }
            }
        }
    }

   
}