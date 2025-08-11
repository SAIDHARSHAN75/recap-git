pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-cred-id')
        DOCKER_IMAGE = "saidharshan706/your-app"
        DEPLOY_SERVER = "ubuntu@3.82.222.215"
    }

    tools {
        maven 'Maven3'
        jdk 'java-17'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/SAIDHARSHAN75/your-repo.git'
            }
        }

        stage('Build & Test with Maven') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    docker build -t $DOCKER_IMAGE:$APP_VERSION -t $DOCKER_IMAGE:latest .
                '''
            }
        }

        stage('Push Docker Image to Hub') {
            steps {
                sh '''
                    echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
                    docker push $DOCKER_IMAGE:$APP_VERSION
                    docker push $DOCKER_IMAGE:latest
                '''
            }
        }

        stage('Deploy to EC2') {
            steps {
                sh '''
                    ssh -o StrictHostKeyChecking=no $DEPLOY_SERVER '
                        docker pull $DOCKER_IMAGE:$APP_VERSION &&
                        docker stop myapp || true &&
                        docker rm myapp || true &&
                        docker run -d --name myapp -p 8080:8080 $DOCKER_IMAGE:$APP_VERSION
                    '
                '''
            }
        }
    }
}


