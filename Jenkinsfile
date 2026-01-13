pipeline {
    agent any

    tools {
        maven 'maven3.9.12'
        jdk 'openJDK21'
    }

    environment {
        // Define the image name
        IMAGE_NAME = 'nexfiit/demo1'
        // We will handle credentials inside the stage using 'withCredentials'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build & Test') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Docker Build') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} ."
                sh "docker tag ${IMAGE_NAME}:${BUILD_NUMBER} ${IMAGE_NAME}:latest"
            }
        }

        stage('Docker Login & Push') {
            steps {
                // Use withCredentials to bind the specific username and password variables
                withCredentials([usernamePassword(credentialsId: 'docker-hub-cred-id', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh """
                        echo \$DOCKER_PASS | docker login -u \$DOCKER_USER --password-stdin
                        docker push ${IMAGE_NAME}:${BUILD_NUMBER}
                        docker push ${IMAGE_NAME}:latest
                    """
                }
            }
        }
    }

    post {
        always {
            sh 'docker logout'
            // Optional: Clean up images to save disk space
            // sh "docker rmi ${IMAGE_NAME}:${BUILD_NUMBER} ${IMAGE_NAME}:latest || true"
        }
    }
}

