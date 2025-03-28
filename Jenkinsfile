pipeline {
    agent any
 
    environment {
        DOCKER_IMAGE = "yogirajdj/my-web-app"
        DOCKER_TAG = "latest"
    }
 
    stages {
        stage('Checkout') {
            steps {
                git branch: 'master', url: 'https://github.com/yogirajdj/my-web-app.git', credentialsID: 'github-credentials'
            }
        }
 
        stage('Build Maven Project') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }
 
        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}:${DOCKER_TAG}")
                }
            }
        }
 
        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'docker-hub-credentials') {
                        docker.image("${DOCKER_IMAGE}:${DOCKER_TAG}").push()
                    }
                }
            }
        }
 
        stage('Deploy Docker Container') {
            steps {
                sh '''
                    docker stop webapp || true
                    docker rm webapp || true
                    docker run -d -p 8081:8081 --name webapp ${DOCKER_IMAGE}:${DOCKER_TAG}
                '''
            }
        }
 
        stage('Cleanup Workspace') {
            steps {
                sh 'rm -rf *'
            }
        }
    }
}
