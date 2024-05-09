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

                    // Check if the container exists using Docker Pipeline commands
                    def existingContainer = docker.listImages(nameFilter: "${containerName}").size() != 0
                    if (existingContainer) {
                        // Remove the existing container
                        docker.image("${containerName}").remove(force: true)
                    }

                    // Run the new container
                    docker.image(appName).run(
                        "--name ${containerName} -d -p ${appPort}:${appPort}"
                    )
                }
            }
        }
    }
}
