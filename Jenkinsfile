pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "la000la/netflix-clone:latest"
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

        stage('Docker Login & Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                    echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                    docker push $DOCKER_IMAGE
                    '''
                }
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
