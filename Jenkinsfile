pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "shrijan111/netflix-clone:latest"
        DOCKER_CREDS = credentials('dockerhub-creds')
        TMDB_V3_API_KEY = credentials('tmdb-api-key')
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/shrijan111/Netflix-clone.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                docker build \
                  --build-arg TMDB_V3_API_KEY=$TMDB_V3_API_KEY \
                  -t $DOCKER_IMAGE .
                '''
            }
        }

        stage('Docker Login') {
            steps {
                sh '''
                echo "$DOCKER_CREDS_PSW" | docker login -u "$DOCKER_CREDS_USR" --password-stdin
                '''
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                sh '''
                docker push $DOCKER_IMAGE
                '''
            }
        }

        stage('Deploy with Docker Compose') {
            steps {
                sh '''
                docker compose down || true
                docker compose pull
                docker compose up -d
                '''
            }
        }
    }

    post {
        success {
            echo "✅ CI/CD Pipeline completed successfully!"
        }
        failure {
            echo "❌ CI/CD Pipeline failed!"
        }
    }
}

