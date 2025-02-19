pipeline {
    agent any

    environment {
        BACKEND_IMAGE = "kirill-masukov/eto-backend:latest"
        FRONTEND_IMAGE = "kirill-masukov/eto-frontend:latest"
        POSTGRES_PASSWORD = "password"
        POSTGRES_USER = "etoapp"
        POSTGRES_DB = "etoapp"
        DB_CONN = "sqlite:///database.db"
        UVICORN_PORT = "8000"
        BACKEND_VERSION = "1.0.0"
        REACT_APP_FRONT_VERSION = "1.0.0"
        REACT_APP_BACKEND_URL = "http://localhost:8000"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm // Используем встроенную команду для получения исходников
            }
        }

        stage('Prepare .env files') {
            steps {
                script {
                    sh """
                    # Создаём .env в корне
                    echo 'POSTGRES_PASSWORD=${POSTGRES_PASSWORD}' > .env
                    echo 'POSTGRES_USER=${POSTGRES_USER}' >> .env
                    echo 'POSTGRES_DB=${POSTGRES_DB}' >> .env

                    # Создаём .env для backend
                    echo 'DB_CONN=${DB_CONN}' > backend/.env
                    echo 'UVICORN_PORT=${UVICORN_PORT}' >> backend/.env
                    echo 'BACKEND_VERSION=${BACKEND_VERSION}' >> backend/.env

                    # Создаём .env для frontend
                    echo 'REACT_APP_FRONT_VERSION=${REACT_APP_FRONT_VERSION}' > frontend/.env
                    echo 'REACT_APP_BACKEND_URL=${REACT_APP_BACKEND_URL}' >> frontend/.env
                    """
                }
            }
        }

        stage('Build Backend') {
            steps {
                script {
                    sh """
                    cd backend
                    docker build -t ${BACKEND_IMAGE} .
                    """
                }
            }
        }

        stage('Build Frontend') {
            steps {
                script {
                    sh """
                    cd frontend
                    docker build -t ${FRONTEND_IMAGE} .
                    """
                }
            }
        }

        stage('Run Application') {
            steps {
                sh 'docker compose up -d --no-build'
            }
        }

        stage('Post-Deploy Check') {
            steps {
                script {
                    sh 'sleep 10' // Даем время контейнерам стартовать
                    sh 'curl -f http://localhost:8000/api/healthcheck'
                    sh 'curl -f http://localhost:3000'
                }
            }
        }
    }

    post {
        always {
            sh 'docker ps'
        }
        success {
            echo '🎉 Deployment successful!'
        }
        failure {
            echo '❌ Deployment failed! Checking logs...'
            sh 'docker logs $(docker ps -q --filter "name=eto-app")'
        }
    }
}
