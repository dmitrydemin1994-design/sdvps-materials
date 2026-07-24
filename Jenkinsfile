pipeline {
    agent any

    environment {
        // Переменная для тега образа — чтобы не дублировать в нескольких местах
        IMAGE_TAG = "my-go-app:build-${BUILD_NUMBER}"
        GO_CMD = "/usr/local/go/bin/go"
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
                sh '${GO_CMD} test -v'
            }
        }

        stages {
        stage('Build Go binary') {
            steps {
                sh '''
                    export GOPATH=/tmp/go
                    mkdir -p $GOPATH
                    cd $WORKSPACE
                    # Команда взята из стадии builder твоего Dockerfile
                    CGO_ENABLED=0 GOOS=linux go build -a -installsuffix nocgo -o myapp .
                '''
            }
        }
        
        stage('Upload to Nexus') {
            environment {
                NEXUS_URL = 'http://192.168.0.229:8081/repository/go-binaries/'
            }
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'nexus-creds',
                    usernameVariable: 'NEXUS_USER',
                    passwordVariable: 'NEXUS_PASS'
                )]) {
                    sh '''
                        curl -u "${NEXUS_USER}:${NEXUS_PASS}" \
                             -T myapp \
                             "${NEXUS_URL}myapp"
                    '''
                }
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
