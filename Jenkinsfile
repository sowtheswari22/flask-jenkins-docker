pipeline {
    agent any

    environment {
        APP_SERVER     = "mohancbe5202@34.45.94.20"
        IMAGE_NAME     = "flask-gunicorn-app"
        CONTAINER_NAME = "flask-gunicorn-container"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/sowtheswari22/flask-jenkins-docker.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:latest .'
            }
        }

        stage('Deploy to Application Server') {
            steps {
                sshagent(['app-server-ssh']) {
                    sh """
                    ssh -o StrictHostKeyChecking=no $APP_SERVER '
                        docker rm -f $CONTAINER_NAME || true
                        docker run -d \
                          --restart always \
                          -p 5000:5000 \
                          --name $CONTAINER_NAME \
                          $IMAGE_NAME:latest
                    '
                    """
                }
            }
        }
    }
}

