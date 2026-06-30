pipeline {

    agent any

    environment {
        DOCKER_USERNAME = "iswaryasundaramoorthy"
        IMAGE_NAME = "food-website"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE_NAME} ."
            }
        }

        stage('Push to DEV Repository') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub',
                        usernameVariable: 'USER',
                        passwordVariable: 'PASS'
                    )
                ]) {

                    sh """
                    echo \$PASS | docker login -u \$USER --password-stdin

                    docker tag ${IMAGE_NAME} \
                    ${DOCKER_USERNAME}/dev:latest

                    docker push \
                    ${DOCKER_USERNAME}/dev:latest

                    docker logout
                    """
                }
            }
        }
    }
}
