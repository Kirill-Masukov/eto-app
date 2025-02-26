pipeline {
    agent any

    environment {
        BACKEND_IMAGE = "kirill-masukov/eto-backend:latest"
        FRONTEND_IMAGE = "kirill-masukov/eto-frontend:latest"
        UVICORN_PORT = "8000"
        BACKEND_VERSION = "1.0.0"
        REACT_APP_FRONT_VERSION = "1.0.0"
        REACT_APP_BACKEND_URL = "http://localhost:8000"
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    sh 'rm -rf eto-app' // Удаляем старую версию
                    sh 'git clone https://github.com/Kirill-Masukov/eto-app.git'
                    dir('eto-app') {
                        sh 'git checkout main'
                    }
                }
            }
        }

        stage('Load Credentials') {
            steps {
                script {
                    env.POSTGRES_PASSWORD = credentials('POSTGRES_PASSWORD')
                    env.DB_CONN = credentials('DB_CONN')
                    
                    withCredentials([usernamePassword(credentialsId: 'POSTGRES_CREDENTIALS', usernameVariable: 'POSTGRES_USER', passwordVariable: 'POSTGRES_PASSWORD')]) {
                        sh '''
                        echo "POSTGRES_PASSWORD=${POSTGRES_PASSWORD}" > .env
                        echo "POSTGRES_USER=${POSTGRES_USER}" >> .env
                        echo "POSTGRES_DB=etoapp" >> .env

                        echo "DB_CONN=${DB_CONN}" > backend/.env
                        echo "UVICORN_PORT=${UVICORN_PORT}" >> backend/.env
                        echo "BACKEND_VERSION=${BACKEND_VERSION}" >> backend/.env

                        echo "REACT_APP_FRONT_VERSION=${REACT_APP_FRONT_VERSION}" > frontend/.env
                        echo "REACT_APP_BACKEND_URL=${REACT_APP_BACKEND_URL}" >> frontend/.env
                        '''
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    sh '''
                    docker-compose down
                    docker-compose up -d
                    '''
                }
            }
        }
    }
}
