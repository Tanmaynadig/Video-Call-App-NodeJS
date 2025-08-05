pipeline {
    agent any

    environment {
        // Defines the name of your Docker image
        dockerImage = 'tanmaynadig/video-call-app'
    }

    stages {
        stage('Install Dependencies') {
            steps {
                echo 'Installing Node.js dependencies...'
                sh 'npm install'
            }
        }
        stage('Build Docker Image') {
            steps {
                echo 'Building the Docker image...'
                // The 'sh' step runs shell commands
                sh "docker build -t ${env.dockerImage} ."
            }
        }
        stage('Push to Docker Hub') {
            steps {
                echo 'Pushing the image to Docker Hub...'
                // Jenkins needs your Docker Hub credentials to do this
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh "docker login -u '${env.DOCKER_USER}' -p '${env.DOCKER_PASS}'"
                    sh "docker push ${env.dockerImage}"
                }
            }
        }
    }
}