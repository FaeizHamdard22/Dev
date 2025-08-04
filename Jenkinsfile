pipeline {
    agent any

    environment {
        IMAGE_NAME = "faeizhamdard97/flask-hello"
    }

    stages {
        stage('Clone Repo') {
            steps {
                git 'https://github.com/FaeizHamdard22/Dev.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${IMAGE_NAME}")
                }
            }
        }

        stage('Login to DockerHub and Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_HUB_USER', passwordVariable: 'DOCKER_HUB_PASS')]) {
                    script {
                        sh "echo $DOCKER_HUB_PASS | docker login -u $DOCKER_HUB_USER --password-stdin"
                        sh "docker push ${IMAGE_NAME}"
                    }
                }
            }
        }
    }
}
