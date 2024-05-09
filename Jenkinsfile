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
                    // Define application name based on branch
                    def appName = env.BRANCH_NAME == 'main' ? 'nodemain:v1.0' : 'nodedev:v1.0'
                    // Build the Docker image using Docker Pipeline plugin
                    docker.build("${appName}")
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // Define port based on branch
                    def appPort = env.BRANCH_NAME == 'main' ? 3000 : 3001
                    // Define container name based on branch
                    def containerName = env.BRANCH_NAME
                    // Define application name based on branch for tagging
                    def appName = env.BRANCH_NAME == 'main' ? 'nodemain:v1.0' : 'nodedev:v1.0'
                    
                    // Stop and remove any existing container
                    sh "docker rm -f ${containerName} || true"
                    // Run the new container
                    docker.run(
                        "--name ${containerName} -d -p ${appPort}:${appPort} ${appName}",
                        'cat' // Use 'cat' to keep the container running
                    )
                }
            }
        }
    }
}
