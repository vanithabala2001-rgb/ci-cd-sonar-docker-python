pipeline {
    agent any

    environment {
        IMAGE_NAME  = "python-ci-cd-app"
        DOCKERHUB_REPO = "vanithabala2001/python.py"
        CONTAINER   = "python-ci-cd-container"
    }

    triggers {
        githubPush()
    }

    stages {

        stage('Code Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/vanithabala2001-rgb/ci-cd-sonar-docker-python.git'
            }
        }

        stage('Install Dependencies & Test') {
            steps {
                bat '''
                python --version
                pip install -r requirements.txt
                pytest
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                bat '''
                docker build -t %IMAGE_NAME% .
                '''
            }
        }

        stage('Docker Login & Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'vanitha_dockerhub_token',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    bat '''
                    docker login -u %DOCKER_USER% -p %DOCKER_PASS%
                    docker tag %IMAGE_NAME% %DOCKERHUB_REPO%:latest
                    docker push %DOCKER_REPO%:latest
                    '''
                }
            }
        }

        stage('Deploy Docker Container') {
            steps {
                bat '''
                docker rm -f %CONTAINER% 2>nul
                docker run -d --name %CONTAINER% %DOCKER_REPO%:latest
                '''
            }
        }
    }
}
