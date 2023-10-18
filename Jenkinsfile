pipeline {
  agent any
  stages {
    stage('Check Code Quality'){
      steps{
      script{
        docker.image('python:3.9').inside { c->
          sh'''
          python -m venv .venv
          . .venv/bin/activate
          pip install pylint
          pip install -r requirements.txt
          pylint --exit-zero --report=y --output-format=json:pylint-report.json,colorized ./*.py
          '''
          publishHTML target : [
                    allowMissing: true,
                    alwaysLinkToLastBuild: true,
                    keepAll: true,
                    reportDir: './',
                    reportFiles: 'pylint-report.json',
                    reportName: 'pylint Scan',
                    reportTitles: 'pylint Scan'
                ]
        }
      }
    }
  }   
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
stage('unit-test'){
  steps{
    script {
      docker.image("${registry}:latest").inside{ c->
        sh 'python app_test.py'}
      }

    }

  }   
stage('http-test'){
      steps{
        script {
          docker.image("${registry}:latest").withRun('-p 9005:9000') {c ->
          sh "sleep 5; curl -i http://localhost:9005/test_string"}
        }

      }

    }
        
        stage('Publish') {
          steps {
            script {
              docker.withRegistry('','docker-hub'){
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
