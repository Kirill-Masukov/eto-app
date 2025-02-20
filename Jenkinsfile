pipeline {
    agent any

    environment {
        GIT_EXECUTABLE = "/usr/bin/git"
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    echo "Git version check:"
                    sh "$GIT_EXECUTABLE --version"
                    checkout scm
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    echo "Running build..."
                    sh "echo 'Building the application...'"
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    echo "Running tests..."
                    sh "echo 'Executing tests...'"
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    echo "Deploying application..."
                    sh "echo 'Deployment process initiated...'"
                }
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully!"
        }
        failure {
            echo "Pipeline failed!"
        }
    }
}
