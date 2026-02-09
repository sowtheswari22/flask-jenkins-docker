pipeline {
    agent any

    environment {
        APP_SERVER     = "mohancbe5202@34.45.94.20"   // SSH to application server
        IMAGE_NAME     = "flask-gunicorn-app"         // Docker image name
        CONTAINER_NAME = "flask-gunicorn-container"  // Docker container name
        APP_DIR        = "/home/mohancbe5202/flask-app" // App directory on server
        HOST_PORT      = "5000"                       // Port exposed on host
        CONTAINER_PORT = "5000"                       // Port inside container
    }

    triggers {
        // Polling every minute for demo; in real setup, use webhook
        pollSCM('* * * * *')
    }

    stages {

        stage('Checkout Latest Code') {
            steps {
                echo "Pulling latest code from GitHub (public repo)..."
                git branch: 'main',
                    url: 'https://github.com/sowtheswari22/flask-jenkins-docker.git'
            }
        }

        stage('Deploy to Application Server') {
            steps {
                echo "Deploying latest code on the remote server..."
                sshagent(['app-server-ssh']) {
                    sh """
                    ssh -o StrictHostKeyChecking=no ${APP_SERVER} '
                        set -e
                        cd ${APP_DIR}

                        echo "Stopping any old containers of this image..."
                        docker ps -a -q --filter "ancestor=${IMAGE_NAME}" | xargs -r docker rm -f || echo "No old containers found"

                        echo "Building new Docker image..."
                        docker build -t ${IMAGE_NAME}:latest .

                        echo "Starting new container..."
                        docker run -d --restart always -p ${HOST_PORT}:${CONTAINER_PORT} --name ${CONTAINER_NAME} ${IMAGE_NAME}:latest

                        echo "Deployment completed successfully!"
                    '
                    """
                }
            }
        }
    }

    post {
        success {
            echo "Pipeline finished successfully ✅"
        }
        failure {
            echo "Pipeline failed ❌"
        }
    }
}
