pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "deeeeepal/flask-app"
        DOCKER_CONTAINER_NAME = "flask-app"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', credentialsId: 'your-git-credentials-id', url: 'https://github.com/Deepal05/flask-cd-pipeline.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    bat 'docker build -t $DOCKER_IMAGE .'
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    bat 'docker run --rm $DOCKER_IMAGE pytest tests/'
                }
            }
        }

        stage('Push Image to Docker Hub') {
            when {
                expression { return env.BRANCH_NAME == 'main' }
            }
            steps {
                script {
                    withDockerRegistry([credentialsId: 'docker-hub-credentials', url: '']) {
                        bat 'docker push $DOCKER_IMAGE'
                    }
                }
            }
        }

        stage('Deploy Application') {
            steps {
                script {
                    bat '''
                    docker stop $DOCKER_CONTAINER_NAME || true
                    docker rm $DOCKER_CONTAINER_NAME || true
                    docker run -d -p 5000:5000 --name $DOCKER_CONTAINER_NAME $DOCKER_IMAGE
                    '''
                }
            }
        }
    }
}
