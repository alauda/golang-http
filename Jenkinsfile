pipeline {
    agent {
        label "golang"
    }
    environment {
      PROJECT = 'project-k'
      FOLDER = 'src/github.com/danielfbm/golang-http'
      GOPATH = "${WORKSPACE}"
    }
    stages {
      stage('Checkout') {
        steps {
          var scmVar = checkout scm
        }
      }
      stage('CI') {
        parallel {
          stage('Unit Tests') {
            steps {
              dir(FOLDER) {
                container('golang') {
                  sh "go test -cover -v -json > test.json"
                  sh "go test -v -coverprofile=coverage.out -covermode=count ."
                }
                container('tools') {
                  withSonarQubeEnv('sonarqube') {
                    sh "sonar-scanner -X"
                  }
                }
              }
            }
          }
          stage('Build') {
            steps {
              dir(FOLDER) {
                container('golang') {
                  sh "go build -v -obin/golang-http"
                }
                container('tools') {
                  sh "docker build -t ${PROJECT}/golang-http:build-${BUILD_ID} ."
                }
              }
            }
          }
        }
      }
    }
  }
