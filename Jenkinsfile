pipeline {
    agent any

    environment {
        IMAGE_NAME = "mahbeer/dotnet-image"
        IMAGE_TAG  = "latest"
        CONTAINER  = "dotnet-image"
    }

    stages {

        stage('Clone Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                bat """
                docker build -t %IMAGE_NAME%:%IMAGE_TAG% .
                """
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DH_USER',
                    passwordVariable: 'DH_PASS'
                )]) {
                    bat """
                    docker login -u %DH_USER% -p %DH_PASS%
                    """
                }
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                bat """
                docker push %IMAGE_NAME%:%IMAGE_TAG%
                """
            }
        }

        stage('Stop Old Container (If Exists)') {
            steps {
                bat """
                docker stop %CONTAINER% || exit 0
                docker rm %CONTAINER% || exit 0
                """
            }
        }

        stage('Pull Image from Docker Hub') {
            steps {
                bat """
                docker pull %IMAGE_NAME%:%IMAGE_TAG%
                """
            }
        }

        stage('Run Container Locally') {
            steps {
                bat """
                docker run -d ^
                --name %CONTAINER% ^
                -p 3000:8080 ^
                %IMAGE_NAME%:%IMAGE_TAG%
                """
            }
        }
    }
}
