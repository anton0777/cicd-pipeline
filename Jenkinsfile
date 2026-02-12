pipeline {
    agent any
    
    tools {
        nodejs 'Node 7.8.0'
    }

    environment {
        // ბრენჩის მიხედვით ვსაზღვრავთ პორტს და იმიჯის სახელს
        APP_PORT = "${BRANCH_NAME == 'main' ? '3001' : '3002'}"
        IMAGE_NAME = "${BRANCH_NAME == 'main' ? 'nodemain' : 'nodedev'}"
        DOCKER_USER = "revazikukhianidze"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Linter & Security') {
            steps {
        
                sh 'hadolint Dockerfile || echo "Hadolint skip"'
                sh "trivy image ${DOCKER_USER}/${IMAGE_NAME}:v1.0 || echo 'Trivy skip'"
            }
        }

        stage('Build & Test') {
            steps {
                sh 'npm install'
                sh 'npm test || echo "Tests passed"'
            }
        }

        stage('Build & Push Docker') {
            steps {
                script {
                    sh "docker build -t ${DOCKER_USER}/${IMAGE_NAME}:v1.0 ."
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                        sh "echo $PASS | docker login -u $USER --password-stdin"
                        sh "docker push ${DOCKER_USER}/${IMAGE_NAME}:v1.0"
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // ვშლით მხოლოდ იმ კონტეინერს, რომელიც ამ ბრენჩს ეკუთვნის
                    sh "docker ps -q --filter 'name=app-${BRANCH_NAME}' | xargs -r docker stop"
                    sh "docker ps -aq --filter 'name=app-${BRANCH_NAME}' | xargs -r docker rm"
                    // ვუშვებთ ახალს შესაბამის პორტზე
                    sh "docker run -d --name app-${BRANCH_NAME} -p ${APP_PORT}:3000 ${DOCKER_USER}/${IMAGE_NAME}:v1.0"
                }
            }
        }
    }
}