pipeline {
    // This agent block tells Jenkins to run the pipeline inside a Docker container
    agent {
        docker {
            image 'node:18-slim'
            // This runs the container as the 'root' user to allow installing software
            args '-u root:root'
        }
    }

    environment {
        dockerRepo = 'tanmaynadig/video-call-app'
        dockerTag = "${env.BUILD_NUMBER}"
    }

    stages {
        // NEW STAGE: Install the Docker command-line tool inside our agent
        stage('Install Docker Client') {
            steps {
                echo 'Installing Docker client...'
                sh 'apt-get update && apt-get install -y docker.io'
            }
        }

        stage('Install Dependencies') {
            steps {
                echo 'Installing Node.js dependencies...'
                sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                sh 'echo "No tests to run, skipping."'
            }
        }

        stage('Build & Push to Docker Hub') {
            steps {
                echo "Building and pushing image: ${env.dockerRepo}:${dockerTag}"
                // Use the credentials we stored in Jenkins
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    // Login to Docker Hub
                    sh "docker login -u '${env.DOCKER_USER}' -p '${env.DOCKER_PASS}'"
                    // Build the image
                    sh "docker build -t ${env.dockerRepo}:${dockerTag} ."
                    // Push the uniquely tagged image
                    sh "docker push ${env.dockerRepo}:${dockerTag}"
                    // Also tag this build as 'latest' and push it
                    sh "docker tag ${env.dockerRepo}:${dockerTag} ${env.dockerRepo}:latest"
                    sh "docker push ${env.dockerRepo}:latest"
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished. Cleaning up...'
            sh 'docker logout'
        }
    }
}