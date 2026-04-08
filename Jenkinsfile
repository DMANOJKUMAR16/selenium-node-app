pipeline {
    agent any

    stages {

        stage('Checkout from GitHub') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/laxmi916/node-k8s-app.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        // ✅ ADD THIS STAGE
        stage('Run Selenium Tests') {
            steps {
                sh 'npm test'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                docker build -t my-app:${BUILD_NUMBER} .
                docker tag my-app:${BUILD_NUMBER} manojkumar947/my-app:latest
                '''
            }
        }

        stage('Push Docker Image') {
            steps {
                sh 'docker push manojkumar947/my-app:latest'
            }
        }

        stage('Start Minikube if not running') {
            steps {
                sh '''
                if ! minikube status | grep -q "apiserver: Running"; then
                    echo "Minikube is not running. Starting now..."
                    minikube start --driver=docker --memory=2048 --cpus=2
                fi
                '''
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                minikube image load manojkumar947/my-app:latest
                minikube kubectl -- apply -f k8s/deployment.yaml
                minikube kubectl -- apply -f k8s/service.yaml
                minikube service my-app-service
                '''
            }
        }
    }
}