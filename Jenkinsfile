pipeline {
    agent any

    triggers {
        pollSCM('H/5 * * * *')
    }

    environment {
        BACKEND_DIR = 'backend'
        FRONTEND_DIR = 'frontend'
        DB_NAME = 'event_registration'
        MYSQL = 'C:\\xampp\\mysql\\bin\\mysql.exe'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Backend') {
            steps {
                dir("${BACKEND_DIR}") {
                    bat 'npm ci'
                }
            }
        }

        stage('Install Frontend') {
            steps {
                dir("${FRONTEND_DIR}") {
                    bat 'npm ci'
                }
            }
        }

        stage('Prepare Database') {
            steps {
                bat '"%MYSQL%" -u root -e "CREATE DATABASE IF NOT EXISTS %DB_NAME%;"'
            }
        }

        stage('Backend Test') {
            steps {
                dir("${BACKEND_DIR}") {
                    bat 'npm test'
                }
            }
        }

        stage('Frontend Build') {
            steps {
                dir("${FRONTEND_DIR}") {
                    bat 'npm run build'
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully.'
        }

        failure {
            echo 'Pipeline failed. Check the stage logs above.'
        }

        always {
            echo 'Pipeline finished.'
        }
    }
}