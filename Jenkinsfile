pipeline {
  agent any
 
  tools {nodejs "node-12.18.4"}

  environment {
    CI = 'true'
  }

  stages {
    stage('Git Checkout') {
      steps {
        git 'https://github.com/tamv/simple-node-js-react-npm-app.git'
      }
    }
    
    stage('Install NPM') {
      steps {
          sh 'npm ci'
      }
    }

    stage('Build & Test') {
      parallel {
        stage('Build web application') {
          steps {
            sh 'npm run build'
          }
        }
        stage('run test') {
          steps {
            sh 'npm run test'
          }
        }
      }
    }

    stage('Build Docker Image') {
      steps {
        echo 'build app docker image'

        script {
          def img = docker.build("zen-app", "-f Dockerfile.app")
          docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
            img.push()
        }
        }
      }
    }
  }
}