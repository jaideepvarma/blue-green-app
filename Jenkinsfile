pipeline {
    agent any

    environment {
        IMAGE = "jaideepvarma/bluegreen:v1"
        CONTAINER_BLUE = "blue-app"
        CONTAINER_GREEN = "green-app"
    }

    stages {
        stage('Checkout') {
    steps {
        git branch: 'main', url: 'https://github.com/jaideepvarma/blue-green-app.git'
    }
}

        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t $IMAGE ."
            }
        }

        stage('Push Image to DockerHub') {
            steps {
                withCredentials([string(credentialsId: 'dockerhub-token', variable: 'DOCKER_HUB_TOKEN')]) {
                    sh "echo $DOCKER_HUB_TOKEN | docker login -u jaideepvarma --password-stdin"
                    sh "docker push $IMAGE"
                }
            }
        }

        stage('Deploy to Green') {
            steps {
                sh "docker stop $CONTAINER_GREEN || true"
                sh "docker rm $CONTAINER_GREEN || true"
                sh "docker run -d -p 3001:3000 --name $CONTAINER_GREEN -e VERSION=GREEN $IMAGE"
            }
        }

        stage('Switch Traffic Blue -> Green') {
            steps {
                sh "docker stop $CONTAINER_BLUE || true"
                sh "docker rm $CONTAINER_BLUE || true"
                sh "docker run -d -p 3000:3000 --name $CONTAINER_BLUE -e VERSION=BLUE $IMAGE"
            }
        }
    }
}
