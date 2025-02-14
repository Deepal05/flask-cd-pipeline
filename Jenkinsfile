pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'deeeeepal/flask-app'
        DOCKER_TAG = 'latest'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/Deepal05/flask-cd-pipeline.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t $DOCKER_IMAGE:$DOCKER_TAG .'
            }
        }

        stage('Run Tests') {
            steps {
                bat 'docker run --rm $DOCKER_IMAGE:$DOCKER_TAG python -m unittest discover'
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                withDockerRegistry([credentialsId: 'docker-hub-credentials', url: '']) {
                    bat 'docker push $DOCKER_IMAGE:$DOCKER_TAG'
                }
            }
        }

        stage('Deploy Application') {
            steps {
                bat '''
                docker-compose down
                docker-compose pull
                docker-compose up -d
                '''
            }
        }
    }
}
