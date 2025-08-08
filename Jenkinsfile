pipeline {
    agent any

    environment {
        APP_ENV = 'testing'
        DB_CONNECTION = 'mysql'
        DB_DATABASE = 'test'
        DB_USERNAME = 'root'
        DB_PASSWORD = 'rootpass'
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Install PHP Dependencies') {
            steps {
                sh 'composer install --no-interaction --prefer-dist --optimize-autoloader'
            }
        }

        stage('Copy .env') {
            steps {
                sh 'cp .env.example .env'
            }
        }

        stage('Generate App Key') {
            steps {
                sh 'php artisan key:generate'
            }
        }

        stage('Run Migrations') {
            steps {
                sh 'php artisan migrate --force'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'php artisan test'
            }
        }

        stage('Build Docker Image') {
            when {
                expression { fileExists('Dockerfile') }
            }
            steps {
                script {
                    dockerImage = docker.build("my-app:${env.BUILD_NUMBER}")
                }
            }
        }

        // Optional deploy stage
        stage('Deploy') {
            when {
                branch 'main'
            }
            steps {
                echo 'Deploying to production...'
            }
        }
    }

    post {
        success {
            echo 'Build and deploy successful!'
        }
        failure {
            echo 'Build failed!'
        }
    }
}
