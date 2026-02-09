pipeline {
    agent any

    environment {
        APP_SERVER     = "mohancbe5202@34.45.94.20"   // Application server SSH
        IMAGE_NAME     = "flask-gunicorn-app"         // Docker image name
        CONTAINER_NAME = "flask-gunicorn-container"  // Docker container name
        APP_DIR        = "/home/mohancbe5202/flask-app" // App directory on server
        HOST_PORT      = "5000"                       // Port exposed on host
        CONTAINER_PORT = "5000"                       // Port inside container
    }

    stages {

        stage('Checkout Code') {
            steps {
                echo "Cloning the project from GitHub..."
                git branch: 'main',
                    url: 'https://github.com/sowtheswari22/flask-jenkins-docker.git',
                    credentialsId: 'github-creds'
            }
        }

        stage('Deploy to Application Server') {
            steps {
                echo "Deploying application on remote server..."
                sshagent(['app-server-ssh']) {
                    sh """
                    ssh -o StrictHostKeyChecking=no ${APP_SERVER} '
                        set -e
                        cd ${APP_DIR}

                        echo "Step 1: Stop any old containers of this image"
                        docker ps -a -q --filter "ancestor=${IMAGE_NAME}" | xargs -r docker rm -f || echo "No old containers found"

                        echo "Step 2: Build new Docker image"
                        docker build -t ${IMAGE_NAME}:latest .

                        echo "Step 3: Start new container"
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


