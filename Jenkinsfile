pipeline {
  agent any

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Run Tests') {
      steps {
        sh 'pytest'
      }
    }

    stage('Build Docker Image') {
      steps {
        sh 'docker build -t fastapi-cicd-app .'
      }
    }
  }
}

