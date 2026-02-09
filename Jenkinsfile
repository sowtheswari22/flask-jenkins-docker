pipeline {
    agent any

    environment {
        APP_SERVER     = "mohancbe5202@34.45.94.20"
        IMAGE_NAME     = "flask-gunicorn-app"
        CONTAINER_NAME = "flask-gunicorn-container"
        APP_DIR        = "/home/mohancbe5202/flask-app"
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

                        echo "Stopping old container if exists..."
                        docker ps -q --filter "name=${CONTAINER_NAME}" | grep -q . && docker rm -f ${CONTAINER_NAME} || echo "No container running"

                        echo "Killing any old Gunicorn processes..."
                        pkill -f "gunicorn.*app:app" || echo "No Gunicorn process running"

                        echo "Building new Docker image..."
                        docker build -t ${IMAGE_NAME}:latest .

                        echo "Starting container..."
                        docker run -d \\
                          --restart always \\
                          -p 5000:5000 \\
                          --name ${CONTAINER_NAME} \\
                          ${IMAGE_NAME}:latest
                    EOF
                    """
                }
            }
        }
    }
}


