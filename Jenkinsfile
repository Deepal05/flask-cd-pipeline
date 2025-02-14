pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')
        DOCKER_IMAGE = "deeeeepal/flask-app"
        REMOTE_SSH = credentials('remote-server-ssh')
    }

    stages {
        // Stage 1: Checkout Code
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        // Stage 2: Build Docker Image
        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}:${env.BUILD_ID}")
                }
            }
        }

        // Stage 3: Run Tests
        stage('Run Tests') {
            steps {
                script {
                    docker.image("${DOCKER_IMAGE}:${env.BUILD_ID}").inside {
                        bat 'python -m pytest tests/'
                    }
                }
            }
        }

        // Stage 4: Push Image to Docker Hub
        stage('Push Image') {
            when {
                expression { currentBuild.result == null || currentBuild.result == 'SUCCESS' }
            }
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', DOCKERHUB_CREDENTIALS) {
                        docker.image("${DOCKER_IMAGE}:${env.BUILD_ID}").push()
                    }
                }
            }
        }

        // Stage 5: Deploy Application
        stage('Deploy') {
            steps {
                script {
                    sshagent([REMOTE_SSH]) {
                        bat """
                            ssh -o StrictHostKeyChecking=no ${REMOTE_SSH_USR}@your-server-ip '
                                docker-compose down && \
                                docker-compose pull && \
                                docker-compose up -d
                            '
                        """
                    }
                }
            }
        }
    }
}
