pipeline {
    agent any

    environment {
        // Переменная для тега образа — чтобы не дублировать в нескольких местах
        IMAGE_TAG = "my-go-app:build-${BUILD_NUMBER}"
    }

    stages {
        stage('Check Environment') {
            steps {
                echo '=== 1. Проверка окружения ==='
                sh '''
                    export PATH=$PATH:/usr/local/go/bin
                    which go
                    go version
                    which java
                    java -version
                    docker --version
                '''
            }
        }

        stage('List Directory') {
            steps {
                echo '=== 2. Содержание директории ==='
                sh 'ls -la'
            }
        }

        stage('Run Go Tests') {
            steps {
                echo '=== 3. Тест ==='
                sh 'go test -v'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "=== 4. Сборка Docker-образа: ${IMAGE_TAG} ==="
                sh "docker build -t ${IMAGE_TAG} ."
                }
        }
    }

    post {
        always {
            echo '=== Пайплайн завершён ==='
        }
        failure {
            echo '=== СБОЙ: пайплайн не прошёл ==='
        }
    }
}
