pipeline {
  agent any
  tools {nodejs "node-12.18.3"}
  environment {
    CI = 'true'
    apiImage = ''
    appImage = ''
  }

  stages {
    stage('Git Checkout') {
      steps {
        git branch: "${PR_BRANCH}", changelog: false, poll: false, url: 'https://github.com/zensurance/zen.git'
      }
    }

    stage('Build Docker Images') {
      parallel {
        stage('Build API Images') {
          steps {
            echo 'building Zen API images'
            script {
              apiImage = docker.build("zensurance/api", "-f dockerfile.api .")
            }
          }
        }
        stage('Build APP Images') {
          steps {
            echo 'building Zen APP images'
            script {
              appImage = docker.build("zensurance/app", "-f dockerfile.app .")
            }
          }
        }
      }
    }

    stage('Publish Docker Images') {
      parallel {
        stage('Publish API Images') {
          steps {
            echo 'publishing Zen API images'
            script {
              docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
                apiImage.push("latest")
              }
            }
          }
        }
        stage('Publish App Images') {
          steps {
            echo 'publishing Zen APP images'
            script {
              docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
                appImage.push("latest")
              }
            }
          }
        }
      }

    }
  }
}