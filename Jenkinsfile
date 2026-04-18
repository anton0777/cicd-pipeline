pipeline {
    agent any
    
    environment {
        // Определяем параметры в зависимости от ветки
        APP_PORT = "${BRANCH_NAME == 'main' ? '3000' : '3001'}"
        IMAGE_NAME = "${BRANCH_NAME == 'main' ? 'nodemain' : 'nodedev'}"
        IMAGE_TAG = "v1.0"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                // Используем NodeJS, настроенный в Global Tool Configuration
                nodejs('Node 7.8.0') {
                    sh 'npm install'
                }
            }
        }

        stage('Test') {
            steps {
                nodejs('Node 7.8.0') {
                    sh 'npm test'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // Остановка и удаление старого контейнера (минимизация downtime)
                    sh "docker stop ${IMAGE_NAME} || true"
                    sh "docker rm ${IMAGE_NAME} || true"
                    
                    // Запуск нового
                    // Обратите внимание: внутренний порт приложения 3000, 
                    // внешний меняется согласно APP_PORT
                    sh "docker run -d --name ${IMAGE_NAME} -p ${APP_PORT}:3000 ${IMAGE_NAME}:${IMAGE_TAG}"
                }
            }
        }
    }
}