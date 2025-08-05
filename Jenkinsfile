pipeline {
    // This pipeline will run on the main Jenkins agent
    agent any

    environment {
        // Define the Docker Hub repository name
        DOCKER_REPO = 'tanmaynadig/video-call-app'
    }

    stages {
        stage('Build and Push Docker Image') {
            steps {
                // This 'script' block allows us to use more advanced logic
                script {
                    // Step 1: Build the Docker image using the Dockerfile in our repository.
                    // Jenkins will automatically tag it with the repository name and build number.
                    echo "Building Docker image..."
                    def customImage = docker.build(DOCKER_REPO)

                    // Step 2: Use the credentials stored in Jenkins to log in to a Docker registry
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub-credentials') {

                        // Step 3: Push the image with a unique tag (e.g., 'video-call-app:15')
                        echo "Pushing image with tag: ${env.BUILD_NUMBER}"
                        customImage.push("${env.BUILD_NUMBER}")

                        // Step 4: Also push the 'latest' tag
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