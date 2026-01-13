pipeline {
    agent any

    tools {
        maven 'maven3.9.12'
        jdk 'openJDK21'
    }

    environment {
        // Define the image name
        IMAGE_NAME = 'nexfiit/demo1'
        // Define the credential ID (this binds the credential object)
        DOCKER_HUB_CREDS = credentials('docker-hub-cred') 
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
                // The variables DOCKER_HUB_CREDS_USR and DOCKER_HUB_CREDS_PSW 
                // are automatically injected here by Jenkins.
                script {
                    sh "echo ${DOCKER_HUB_CREDS_PSW} | docker login -u ${DOCKER_HUB_CREDS_USR} --password-stdin"
                }
                
                sh "docker push ${IMAGE_NAME}:${BUILD_NUMBER}"
                sh "docker push ${IMAGE_NAME}:latest"
            }
        }
    }

    post {
        always {
            sh 'docker logout'
            // Optional: Clean up images
            // sh "docker rmi ${IMAGE_NAME}:${BUILD_NUMBER} ${IMAGE_NAME}:latest || true"
        }
    }
}
