pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-login') // Jenkins credentials ID
        IMAGE_NAME = "silver595/java-docker-automation"               // Docker Hub repo
        IMAGE_TAG = "latest"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/SlayerAdi/Dockerjava.git'
            }
        }

        stage('Clean Docker Old Images') {
            steps {
                // Clean old images before building
                bat "docker system prune -af"
            }
        }

        stage('Build JAR') {
            steps {
                bat 'mvn clean package'
            }
        }

        stage('Build Docker Image') {
            steps {
                // Hardcode the repo name to avoid env expansion issues
                bat 'docker build -t silver595/java-docker-automation:latest .'
            }
        }

        stage('Login to DockerHub') {
            steps {
                // Use --password-stdin for safer login
                bat """echo %DOCKERHUB_CREDENTIALS_PSW% | docker login -u %DOCKERHUB_CREDENTIALS_USR% --password-stdin"""
            }
        }

        stage('Push Image') {
            steps {
                bat 'docker push silver595/java-docker-automation:latest'
            }
        }
    }

    post {
        success {
            echo 'Docker Image pushed successfully!'
        }
        failure {
            echo 'Build failed! Check logs for details.'
        }
    }
}
