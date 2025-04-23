pipeline {
    agent any

    environment {
        DOCKER_CREDENTIALS = 'dockerhub-credentials' // Jenkins stored Docker Hub credentials
        DOCKER_IMAGE_FRONTEND = 'frontend:latest'
        DOCKER_IMAGE_BACKEND = 'backend:latest'
    }

    stages {
        stage('Checkout') {
    steps {
        git branch: 'main', url: 'https://github.com/ArcaneNova/Delhi-Electricity-Demand.git'
    }
}

        stage('Build Docker Images') {
            steps {
                script {
                    // Build frontend Docker image
                    docker.build("frontend", "./frontend")
                    
                    // Build backend Docker image
                    docker.build("backend", "./backend")
                }
            }
        }

        stage('Test Docker Images') {
            steps {
                script {
                    // Run container tests (mock tests if none)
                    sh 'docker run --rm ${DOCKER_IMAGE_FRONTEND} npm run test'
                    sh 'docker run --rm ${DOCKER_IMAGE_BACKEND} pytest tests'
                }
            }
        }

        stage('Push Docker Images to Docker Hub') {
            steps {
                script {
                    // Login to Docker Hub
                    docker.withRegistry('', DOCKER_CREDENTIALS) {
                        // Push frontend Docker image
                        docker.image("frontend").push()
                        
                        // Push backend Docker image
                        docker.image("backend").push()
                    }
                }
            }
        }

        stage('Deploy to Local Server') {
            steps {
                script {
                    // Deploy to a local machine using Docker Compose
                    sh 'docker-compose down'
                    sh 'docker-compose up -d'
                }
            }
        }

        stage('Clean up') {
            steps {
                // Remove old Docker images if needed
                sh 'docker system prune -f'
            }
        }
    }

    post {
        success {
            echo 'Deployment Successful!'
        }
        failure {
            echo 'Build/Deployment Failed!'
        }
    }
}