pipeline {
    agent any

    environment {
        IMAGE_NAME = "netflix-clone"
        TMDB_V3_API_KEY = credentials('tmdb-api-key')
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/YOUR_GITHUB_USERNAME/Netflix-clone.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                docker build \
                  --build-arg TMDB_V3_API_KEY=$TMDB_V3_API_KEY \
                  -t $IMAGE_NAME .
                '''
            }
        }

        stage('Run Container (Docker Compose)') {
            steps {
                sh '''
                docker compose down || true
                docker compose up -d --build
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
