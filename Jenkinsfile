pipeline {
    agent any

    environment {
        // The repository name on Docker Hub
        dockerRepo = 'tanmaynadig/video-call-app'
        // Create a unique tag for each build
        dockerTag = "${env.BUILD_NUMBER}"
    }

    stages {
        stage('Install Dependencies') {
            steps {
                echo 'Installing Node.js dependencies...'
                sh 'npm install'
            }
        }

        // Added a Test stage as per best practices
        stage('Test') {
            steps {
                echo 'Running tests...'
                // This project has no test script, so we'll simulate a pass
                sh 'echo "No tests to run, skipping."'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building the Docker image with tag: ${dockerTag}"
                // Build the image and tag it with the build number
                sh "docker build -t ${env.dockerRepo}:${dockerTag} ."
            }
        }

        stage('Push to Docker Hub') {
            steps {
                echo "Pushing ${env.dockerRepo}:${dockerTag} to Docker Hub..."
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh "docker login -u '${env.DOCKER_USER}' -p '${env.DOCKER_PASS}'"
                    // Push the specifically tagged image
                    sh "docker push ${env.dockerRepo}:${dockerTag}"
                    // Also, tag this build as 'latest' and push it
                    sh "docker tag ${env.dockerRepo}:${dockerTag} ${env.dockerRepo}:latest"
                    sh "docker push ${env.dockerRepo}:latest"
                }
            }
        }
    }

    // This 'post' block runs after all stages are complete
    post {
        always {
            echo 'Pipeline finished. Cleaning up...'
            // Log out of Docker Hub to be safe
            sh 'docker logout'
        }
    }
}