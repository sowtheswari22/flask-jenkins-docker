pipeline {
    agent any

    environment {
        APP_SERVER     = "mohancbe5202@34.45.94.20"
        IMAGE_NAME     = "flask-gunicorn-app"
        CONTAINER_NAME = "flask-gunicorn-container"
        APP_DIR        = "/home/mohancbe5202/flask-app"
        HOST_PORT      = "5000"  // Change if needed to avoid port conflicts
        CONTAINER_PORT = "5000"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/sowtheswari22/flask-jenkins-docker.git'
            }
        }

        stage('Deploy to Application Server') {
            steps {
                sshagent(['app-server-ssh']) {
                    sh """
                    ssh -o StrictHostKeyChecking=no ${APP_SERVER} << 'EOF'
                        set -e
                        cd ${APP_DIR}

                        echo "Stopping any old containers of this image..."
                        docker ps -a -q --filter "ancestor=${IMAGE_NAME}" | xargs -r docker rm -f || echo "No old containers"

                        echo "Building new Docker image..."
                        docker build -t ${IMAGE_NAME}:latest .

                        echo "Starting container..."
                        docker run -d \\
                          --restart always \\
                          -p ${HOST_PORT}:${CONTAINER_PORT} \\
                          --name ${CONTAINER_NAME} \\
                          ${IMAGE_NAME}:latest
                    EOF
                    """
                }
            }
        }
    }
}

