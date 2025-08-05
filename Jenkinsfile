pipeline {
    // Tell Jenkins which tools to prepare for this pipeline
    agent any
    tools {
        // The name 'NodeJS-18' must match the name you gave it in the Tools configuration
        nodejs 'NodeJS'
    }

    environment {
        dockerRepo = 'tanmaynadig/video-call-app'
        dockerTag = "${env.BUILD_NUMBER}"
    }

    stages {
        stage('Install Dependencies') {
            steps {
                echo 'Installing Node.js dependencies...'
                // Now the 'npm' command will be available
                sh 'npm install'
            }
        }

        // ... rest of your stages ...
        stage('Test') {
            steps {
                echo 'Running tests...'
                sh 'echo "No tests to run, skipping."'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building the Docker image with tag: ${dockerTag}"
                sh "docker build -t ${env.dockerRepo}:${dockerTag} ."
            }
        }

        stage('Push to Docker Hub') {
            steps {
                echo "Pushing ${env.dockerRepo}:${dockerTag} to Docker Hub..."
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh "docker login -u '${env.DOCKER_USER}' -p '${env.DOCKER_PASS}'"
                    sh "docker push ${env.dockerRepo}:${dockerTag}"
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