pipeline {
    agent any
    environment {
        // Replace with your Docker Hub username and repository name
        DOCKER_IMAGE = 'your-username/maven-app'
        REGISTRY_CREDS = 'docker-hub-creds' 
    }
    stages {
        stage('Build Artifact') {
            steps {
                // Compile and package the Java application into a JAR
                sh 'mvn clean package -DskipTests' 
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    // Builds the image using the local Dockerfile
                    appImage = docker.build("${DOCKER_IMAGE}:${env.BUILD_ID}")
                }
            }
        }
        stage('Push to Registry') {
            steps {
                script {
                    // Authenticates and pushes the image
                    docker.withRegistry('', REGISTRY_CREDS) {
                        appImage.push()
                        appImage.push('latest')
                    }
                }
            }
        }
    }
    post {
        always {
            // Optional: Clean up local images to save disk space
            sh "docker rmi ${DOCKER_IMAGE}:${env.BUILD_ID} || true"
        }
    }
}
