pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "deeeeepal/flask-app"
        DOCKER_CREDENTIALS_ID = "docker-hub-credentials"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git credentialsId: 'github-credentials', url: 'https://github.com/Deepal05/flask-cd-pipeline.git', branch: 'main'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    bat "docker build -t $DOCKER_IMAGE:latest ."
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    bat "docker run --rm $DOCKER_IMAGE:latest pytest test_app.py"
                }
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', DOCKER_CREDENTIALS_ID) {
                        bat "docker push $DOCKER_IMAGE:latest"
                    }
                }
            }
        }

        stage('Deploy Application') {
            steps {
                withCredentials([sshUserPrivateKey(credentialsId: 'your-server-ssh-key', keyFileVariable: 'SSH_KEY')]) {
                    script {
                        bat """
                            set SSH_KEY_PATH=%SSH_KEY%
                            ssh -o StrictHostKeyChecking=no -i %SSH_KEY_PATH% user@your-server 'docker pull $DOCKER_IMAGE:latest &&
                            docker-compose up -d'
                        """
                    }
                }
            }
        }
    }

    post {
        success {
            echo "Deployment Successful!"
        }
        failure {
            echo "Deployment Failed!"
        }
    }
}
