pipeline {
    // Define a new agent. The pipeline will run inside a Node.js Docker container.
    agent {
        docker {
            image 'node:18-slim'
            // This runs the container as the 'root' user to allow installing software.
            // It also mounts the Docker socket from your computer.
            args '-u root:root -v /var/run/docker.sock:/var/run/docker.sock'
        }
    }

    environment {
        dockerRepo = 'tanmaynadig/video-call-app'
        dockerTag = "${env.BUILD_NUMBER}"
    }

    stages {
        // NEW STAGE: Install the Docker command-line tool
        stage('Install Docker') {
            steps {
                echo 'Installing Docker client...'
                // This command installs the 'docker' software inside our container
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