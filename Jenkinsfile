pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-cred-id') // Jenkins credential ID
        DOCKER_IMAGE = "your-dockerhub-username/your-app"
        DEPLOY_SERVER = "ubuntu@your-ec2-public-ip"
    }

    tools {
        maven 'Maven3'
        jdk 'Java17'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com//your-repo.git'
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
                    docker build -t $DOCKER_IMAGE:latest .
                '''
            }
        }

        stage('Push Docker Image to Hub') {
            steps {
                sh '''
                    echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
                    docker push $DOCKER_IMAGE:latest
                '''
            }
        }

        stage('Deploy to EC2') {
            steps {
                sh '''
                    ssh -o StrictHostKeyChecking=no $DEPLOY_SERVER '
                        docker pull $DOCKER_IMAGE:latest &&
                        docker stop myapp || true &&
                        docker rm myapp || true &&
                        docker run -d --name myapp -p 8080:8080 $DOCKER_IMAGE:latest
                    '
                '''
            }
        }
    }
}

