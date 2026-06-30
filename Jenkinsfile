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

        stage('Check Branch') {
            steps {
                script {
                    env.GIT_BRANCH_NAME = sh(
                        script: "git rev-parse --abbrev-ref HEAD",
                        returnStdout: true
                    ).trim()

                    echo "Current Branch: ${env.GIT_BRANCH_NAME}"
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE_NAME} ."
            }
        }

        stage('Push to DEV Repository') {

            when {
                expression {
                    env.GIT_BRANCH_NAME == "dev"
                }
            }

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
                    """
                }
            }
        }

        stage('Push to PROD Repository') {

            when {
                expression {
                    env.GIT_BRANCH_NAME == "main"
                }
            }

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
                    ${DOCKER_USERNAME}/prod:latest

                    docker push \
                    ${DOCKER_USERNAME}/prod:latest
                    """
                }
            }
        }
    }
}
