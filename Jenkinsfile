pipeline {
    // Run on any available agent
    agent any

    // Use the NodeJS tool configured in Jenkins
    tools {
        nodejs 'NodeJs'
    }

    stages {
        // STAGE 1: Manually check out the code
        stage('Checkout Code') {
            steps {
                // Clean the workspace and clone the repository
                cleanWs()
                git url: 'https://github.com/Tanmaynadig/Video-Call-App-NodeJS.git', branch: 'main'
            }
        }

        // STAGE 2: Install the Docker command-line tool
        stage('Install Docker Client') {
            steps {
                echo 'Installing Docker client...'
                sh 'apt-get update && apt-get install -y docker.io'
            }
        }

        // STAGE 3: Install Node.js dependencies
        stage('Install Dependencies') {
            steps {
                echo 'Installing Node.js dependencies...'
                sh 'npm install'
            }
        }

        // STAGE 4: Build and Push the Docker Image
        stage('Build & Push Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    // Define the image name inside the step
                    def imageName = "tanmaynadig/video-call-app"
                    
                    // Login, Build, Tag, and Push
                    sh "docker login -u '${DOCKER_USER}' -p '${DOCKER_PASS}'"
                    sh "docker build -t ${imageName}:${env.BUILD_NUMBER} ."
                    sh "docker push ${imageName}:${env.BUILD_NUMBER}"
                    sh "docker tag ${imageName}:${env.BUILD_NUMBER} ${imageName}:latest"
                    sh "docker push ${imageName}:latest"
                }
            }
        }
    }
    post {
        always {
            echo 'Pipeline finished. Logging out of Docker Hub...'
            sh 'docker logout'
        }
    }
}