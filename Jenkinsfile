pipeline {
  agent any

  tools {
        nodejs 'node'
    }
  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Build') {
      steps {
        sh 'npm install'
      }
    }

    stage('Test') {
      steps {
        sh 'npm test'
      }
    }

    stage('Build Docker Image') {
      steps {
        script {
          def appPort = env.BRANCH_NAME == 'main' ? 3000 : 3001
          def appName = env.BRANCH_NAME == 'main' ? 'nodemain:v1.0' : 'nodedev:v1.0'
          sh """
          docker build -t ${appName} .
          docker tag ${appName} ${appName}
          """
        }

      }
    }

    stage('Deploy') {
      steps {
        script {
          def appPort = env.BRANCH_NAME == 'main' ? 3000 : 3001
          sh """
          docker rm -f ${env.BRANCH_NAME} || true
          docker run -d -p ${appPort}:${appPort} --name ${env.BRANCH_NAME} ${env.BRANCH_NAME == 'main' ? 'nodemain:v1.0' : 'nodedev:v1.0'}
          """
        }

      }
    }

  }
}
