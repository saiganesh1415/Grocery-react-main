pipeline {
    agent any

    environment {
        IMAGE_NAME = 'saiganesh1415/grocery-react-main-frontend:latest'
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
                    credentialsId: 'github-token',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    script {
                        // Use a script block to avoid string interpolation warnings
                        sh """
                            echo \$DOCKER_PASS | docker login -u \$DOCKER_USER --password-stdin
                            docker build -t grocery-react-main-frontend .
                            docker logout
                        """
                    }
                }
            }
        }

        // Other stages...
    }
}
