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
        stage('Debug Info') {
            steps {
                script {
                    sh 'echo "=== WHOAMI ==="'
                    sh 'whoami'
                    sh 'echo "=== PWD ==="'
                    sh 'pwd'
                    sh 'echo "=== LS -LA ==="'
                    sh 'ls -la'
                }
            }
        }

        stage('Checkout') {
            steps {
                script {
                    sh 'rm -rf eto-app' // Удаляем старый код, если был
                    sh 'git clone https://github.com/Kirill-Masukov/eto-app.git'
                    dir('eto-app') {
                        sh 'git checkout main' // Убедимся, что на main
                    }
                }
            }
        }

        stage('Prepare .env files') {
            steps {
                script {
                    sh """
                    echo 'POSTGRES_PASSWORD=${POSTGRES_PASSWORD}' > .env
                    echo 'POSTGRES_USER=${POSTGRES_USER}' >> .env
                    echo 'POSTGRES_DB=${POSTGRES_DB}' >> .env

                    echo 'DB_CONN=${DB_CONN}' > eto-app/backend/.env
                    echo 'UVICORN_PORT=${UVICORN_PORT}' >> eto-app/backend/.env
                    echo 'BACKEND_VERSION=${BACKEND_VERSION}' >> eto-app/backend/.env

                    echo 'REACT_APP_FRONT_VERSION=${REACT_APP_FRONT_VERSION}' > eto-app/frontend/.env
                    echo 'REACT_APP_BACKEND_URL=${REACT_APP_BACKEND_URL}' >> eto-app/frontend/.env
                    """
                }
            }
        }

        stage('Build Backend') {
            steps {
                script {
                    sh """
                    cd eto-app/backend
                    docker build -t ${BACKEND_IMAGE} .
                    """
                }
            }
        }

        stage('Build Frontend') {
            steps {
                script {
                    sh """
                    cd eto-app/frontend
                    docker build -t ${FRONTEND_IMAGE} .
                    """
                }
            }
        }

        stage('Run Application') {
            steps {
                script {
                    echo "Running Docker Compose..."
                    sh 'cd eto-app && docker-compose up -d --no-build'
                }
            }
        }

        stage('Post-Deploy Check') {
            steps {
                script {
                    sh 'sleep 10'
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
