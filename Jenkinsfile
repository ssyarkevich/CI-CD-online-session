pipeline {
  agent any
  stages {
    stage('Build') {
      parallel {
        stage('Build') {
          steps {
            script {
              checkout scm
              def customImage = docker.build("${registry}:latest")
            }

          }
        }

        stage('Publish') {
          steps {
            script {
              docker.withRegistry('','dockerhub'){
                docker.image("${registry}:latest").push('latest')
              }
            }

          }
        }

      }
    }

  }
  environment {
    registry = 'ssyarkevich/flask_app'
  }
}