pipeline {
    // Run on any available agent
    agent any

    stages {
        // This stage prepares the workspace and checks out the code
        stage('Preparation') {
            steps {
                // This is a crucial step: it cleans the workspace of any old files
                // or corrupted directories before starting the build.
                cleanWs()

                // Explicitly check out the code from the configured SCM (Git)
                echo 'Checking out source code...'
                checkout scm
                echo 'Checkout complete.'
            }
        }

        // This stage builds and pushes the Docker image
        stage('Build and Push') {
            steps {
                // The 'script' block allows us to use the docker commands
                script {
                    echo "Building Docker image..."
                    // Build the image from the Dockerfile in the current directory
                    def customImage = docker.build('tanmaynadig/video-call-app')

                    // Securely log in to Docker Hub and push the image
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub-credentials') {
                        // Push the image with a unique tag (e.g., 'video-call-app:15')
                        echo "Pushing image with tag: ${env.BUILD_NUMBER}"
                        customImage.push("${env.BUILD_NUMBER}")

                        // Also push the 'latest' tag
                        echo "Pushing image with tag: latest"
                        customImage.push('latest')
                    }
                }
            }
        }
    }
    post {
        always {
            echo 'Pipeline finished.'
        }
    }
}