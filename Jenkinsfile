pipeline {
    agent any

    environment {
        // Configuration for your Docker Hub image
        // Make sure 'nexfiit' is your actual Docker Hub username
        IMAGE_NAME = 'nexfiit/demo1'
        // REPLACE 'docker-hub-cred-id' with your actual Jenkins Credential ID
        DOCKER_HUB_CREDS = credentials('docker-hub-cred') 
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out code...'
                checkout scm
            }
        }

        stage('Build & Test') {
            steps {
                echo 'Building Maven project...'
                sh 'mvn clean install'
            }
        }

        stage('Docker Build') {
            steps {
                echo 'Building Docker Image...'
                // Tag with Jenkins Build Number and 'latest'
                sh "docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} ."
                sh "docker tag ${IMAGE_NAME}:${BUILD_NUMBER} ${IMAGE_NAME}:latest"
            }
        }

        stage('Docker Login') {
            steps {
                echo 'Logging into Docker Hub...'
                // Login using the credentials bound in the environment block
                sh "echo ${DOCKER_HUB_CREDS_PSW} | docker login -u ${DOCKER_HUB_CREDS_USR} --password-stdin"
            }
        }

        stage('Docker Push') {
            steps {
                echo 'Pushing Image to Docker Hub...'
                sh "docker push ${IMAGE_NAME}:${BUILD_NUMBER}"
                sh "docker push ${IMAGE_NAME}:latest"
            }
        }
    }

    post {
        always {
            echo 'Cleaning up...'
            // Logout from Docker Hub
            sh 'docker logout'
            // Optional: Clean up local images to save space
            // sh "docker rmi ${IMAGE_NAME}:${BUILD_NUMBER} ${IMAGE_NAME}:latest || true"
        }
    }
}

