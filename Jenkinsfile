pipeline {
    agent any

    environment {
        REGISTRY = "wahaduzzaman"
        APP_NAME = "simple-html-app"
        IMAGE_TAG = "${REGISTRY}/${APP_NAME}:${env.BUILD_NUMBER}"
        DOCKER_CREDENTIALS = 'docker-hub-credentials' // Replace with your credentials ID in Jenkins
    }

    stages {
        stage('Checkout Code') {
            steps {
                echo 'Checking out the application code from Git...'
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building the Docker image...'
                script {
                    sh "docker build -t ${IMAGE_TAG} ."
                }
            }
        }

        stage('Test Docker Image') {
            steps {
                echo 'Testing the Docker image...'
                script {
                    // Run a temporary container to test
                    sh "docker run --rm -d -p 8080:80 ${IMAGE_TAG}"
                    sh "curl -f http://192.168.60.147:8080"
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                echo 'Pushing the Docker image to Docker Hub...'
                script {
                    docker.withRegistry('https://index.docker.io/v1/', DOCKER_CREDENTIALS) {
                        sh "docker push ${IMAGE_TAG}"
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying the application...'
                script {
                    // Deploy the container to a production server or an environment
                    sh "docker run -d -p 80:80 ${IMAGE_TAG}"
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up...'
            sh "docker rmi ${IMAGE_TAG} || true"
        }
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
