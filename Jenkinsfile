pipeline {
    agent any

    environment {
        DOCKER_CREDENTIALS = 'dockerhub-credss' // Jenkins credentials ID for DockerHub
        DOCKER_IMAGE = 'arunkhrishnadocker/arundockerimage' // Updated image name
        DOCKER_TAG = 'latest'
        GIT_REPO = 'https://github.com/ArunKhrishna/pythonflaskminikube.git' // GitHub repository
        GIT_BRANCH = 'main' // Branch to deploy
        KUBECONFIG = 'C:/ProgramData/Jenkins/.kube/config' // Important for Minikube access
    }

    stages {
        stage('Checkout Code') {
            steps {
                git url: "${GIT_REPO}", branch: "${GIT_BRANCH}"
                bat 'dir /s' // To confirm folder structure after checkout
            }
        }

        // Debugging stage to check workspace structure
        stage('Debug Workspace') {
            steps {
                bat 'dir /s'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: "${DOCKER_CREDENTIALS}",
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {
                    bat """
                        docker login -u ${DOCKER_USER} -p ${DOCKER_PASSWORD}
                        docker push ${DOCKER_IMAGE}:${DOCKER_TAG}
                    """
                }
            }
        }

        stage('Deploy to Minikube') {
            steps {
                bat 'kubectl config use-context minikube'
                // Use the correct relative path from the repository root to your YAML files
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
