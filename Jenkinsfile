
pipeline {
    agent any

    environment {
        IMAGE_NAME = "faeizanaba/flask-hello"
    }

    stages {
        stage('Clone') {
            steps {
                git branch: 'main', url: 'https://github.com/FaeizHamdard22/Dev.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build --no-cache -t $IMAGE_NAME ."
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials',
                    usernameVariable: 'DOCKER_HUB_USER',
                    passwordVariable: 'DOCKER_HUB_PASS'
                )]) {
                    sh '''
                        echo $DOCKER_HUB_PASS | docker login -u $DOCKER_HUB_USER --password-stdin
                        docker push $IMAGE_NAME
                    '''
                }
            }
        }
    }
}
