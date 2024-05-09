pipeline {
  agent any

  tools {
        nodejs 'node'
    }
  stages {
    stage('Checkout') {
        steps {
            script {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: "*/${env.BRANCH_NAME}"]],
                    userRemoteConfigs: [[
                        url: 'git@github.com:asqarsakenov/epm-ci-cd-lab3.git'
                        credentialsId: 'b878bc33-60e4-4ff3-b570-d4ccdbb50dcb'
                    ]]
                ])
            }
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
