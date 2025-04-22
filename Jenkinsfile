pipeline {
    agent any

    environment {
        DOCKER_CREDENTIALS = 'dockerhub-creds'  // Jenkins credentials ID for DockerHub
        DOCKER_IMAGE = 'jefrinpeter/jefrindockerimage'
        DOCKER_TAG = 'latest'
        GIT_REPO = 'https://github.com/Jeffy287/k8s-deployment.git'
        GIT_BRANCH = 'main'
        KUBECONFIG = 'C:/ProgramData/Jenkins/.kube/config'  // 👈 Important for Minikube access
    }

    stages {
        stage('Checkout Code') {
            steps {
                git url: "${GIT_REPO}", branch: "${GIT_BRANCH}"
            }
        }

        stage('Build Docker Image') {
            steps {
                bat "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: "${DOCKER_CREDENTIALS}", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASSWORD')]) {
                    bat """
                        echo ${DOCKER_PASSWORD} | docker login -u ${DOCKER_USER} --password-stdin
                        docker push ${DOCKER_IMAGE}:${DOCKER_TAG}
                    """
                }
            }
        }

        stage('Deploy to Minikube') {
            steps {
                bat 'kubectl config use-context minikube'
                bat 'kubectl apply -f k8s/deployment.yaml'
                bat 'kubectl apply -f k8s/service.yaml'
            }
        }

        stage('Rollout Restart Deployment') {
            steps {
                bat 'kubectl rollout restart deployment website-deployment'
            }
        }

        stage('Notify Deployment Success') {
            steps {
                echo "✅ Successfully deployed to Minikube!"
            }
        }
    }

    post {
        always {
            bat 'docker logout'
        }
    }
}
