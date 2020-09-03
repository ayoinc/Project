pipeline {
  agent {
    docker {
      image 'ubuntu'
      args 'apt update'
    }

  }
  stages {
    stage('error') {
      steps {
        sh 'apt update'
      }
    }

  }
}