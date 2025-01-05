pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        sh 'script ./scripts/build.sh'
      }
    }

    stage('Test') {
      steps {
        sh 'script ./test.sh'
      }
    }

  }
}