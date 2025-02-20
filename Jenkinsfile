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
                git branch: 'main', url: 'git@github.com:your-repo/your-project.git'
            }
        }

        stage('Create .env files') {
            steps {
                script {
                    sh '''
                    echo "POSTGRES_PASSWORD=${POSTGRES_PASSWORD}" > .env
                    echo "POSTGRES_USER=${POSTGRES_USER}" >> .env
                    echo "POSTGRES_DB=${POSTGRES_DB}" >> .env

                    echo "DB_CONN=${DB_CONN}" > backend/.env
                    echo "UVICORN_PORT=${UVICORN_PORT}" >> backend/.env
                    echo "BACKEND_VERSION=${BACKEND_VERSION}" >> backend/.env

                    echo "REACT_APP_FRONT_VERSION=${REACT_APP_FRONT_VERSION}" > frontend/.env
                    echo "REACT_APP_BACKEND_URL=${REACT_APP_BACKEND_URL}" >> frontend/.env
                    '''
                }
            }
        }

        stage('Build and Push Docker Images') {
            steps {
                script {
                    sh '''
                    docker build -t ${BACKEND_IMAGE} ./backend
                    docker build -t ${FRONTEND_IMAGE} ./frontend

                    docker push ${BACKEND_IMAGE}
                    docker push ${FRONTEND_IMAGE}
                    '''
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    sh '''
                    docker-compose down
                    docker-compose up -d --build
                    '''
                }
            }
        }
    }
}
