pipeline {
    agent any

    tools {
        // Use the NodeJS tool you configured in Jenkins
        nodejs 'NodeJs'
    }

    environment {
        dockerRepo = 'tanmaynadig/video-call-app'
        dockerTag  = "build-${env.BUILD_NUMBER}"
    }

    stages {
        // NEW STAGE: Install the Docker command-line tool
        stage('Install Docker Client') {
            steps {
                echo 'Installing Docker client...'
                // This command installs the 'docker' software
                sh 'apt-get update && apt-get install -y docker.io'
            }
        }

        stage('Install Dependencies') {
            steps {
                echo 'Installing Node.js dependencies...'
                sh 'npm install'
            }
        }

        stage('Build & Push Image') {
            steps {
                echo "Building and pushing image: ${env.dockerRepo}:${dockerTag}"
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    // Login, Build, Tag, and Push
                    sh "docker login -u '${env.DOCKER_USER}' -p '${env.DOCKER_PASS}'"
                    sh "docker build -t ${env.dockerRepo}:${dockerTag} ."
                    sh "docker push ${env.dockerRepo}:${dockerTag}"
                    sh "docker tag ${env.dockerRepo}:${dockerTag} ${env.dockerRepo}:latest"
                    sh "docker push ${env.dockerRepo}:latest"
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