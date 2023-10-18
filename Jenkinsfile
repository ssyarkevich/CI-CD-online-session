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
stage('Scan Docker Image for Vulnerabilities') {
      steps {
        script {
          sh 'mkdir -p reports'
          sh 'curl -sfL https://raw.githubusercontent.com/aquasecurity/trivy/main/contrib/html.tpl > html.tpl'
          def vulnerabilities = sh(script: "trivy image --exit-code 0 --severity HIGH,MEDIUM,LOW --format template --template '@html.tpl' -o reports/image-scan.html --no-progress ${registry}:latest", returnStdout: true).trim()
          echo "Vulnerability Report:\n${vulnerabilities}"
          publishHTML target : [
                    allowMissing: true,
                    alwaysLinkToLastBuild: true,
                    keepAll: true,
                    reportDir: 'reports',
                    reportFiles: 'image-scan.html',
                    reportName: 'Trivy Scan',
                    reportTitles: 'Trivy Scan'
                ]
         sh 'trivy image --ignore-unfixed --exit-code 1 --severity CRITICAL --no-progress ${registry}:latest'
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
