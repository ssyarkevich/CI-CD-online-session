pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        script {
          checkout scm
          def customImage = docker.build("${registry}:latest")
        }

      }
    }

  }
  environment {
    registry = 'ssyarkevich/flask_app'
  }
}