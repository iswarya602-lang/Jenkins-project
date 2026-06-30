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
                    echo "BRANCH_NAME = ${env.BRANCH_NAME}"
                    echo "GIT_BRANCH = ${env.GIT_BRANCH}"
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
                    return env.GIT_BRANCH?.contains('dev')
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
                    return env.GIT_BRANCH?.contains('main')
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
