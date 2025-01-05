pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        sh './scripts/build.sh'
      }
    }

    stage('Test') {
      steps {
        sh './scripts/test.sh'
      }
    }

    stage('Docker build') {
      steps {
        sh 'docker build -t 4ina/mybuildimage'
      }
    }

    stage('Docker push') {
      steps {
        sh 'docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD'
        sh 'docker push 4ina/mybuildimage'
      }
    }

  }
  environment {
    DOCKER_USERNAME = '4ina'
    DOCKER_PASSWORD = 'todram-vysfy3-govdoP'
  }
}