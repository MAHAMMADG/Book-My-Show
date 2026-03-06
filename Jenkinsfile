pipeline {
    agent any

    environment {
        SCANNER_HOME = tool 'sonar-scanner'
        DOCKER_IMAGE = "mahammad7/bookmyshow"
    }

    stages {

        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout Code from GitHub') {
            steps {
                git branch: 'mahi-branch', url: 'https://github.com/MAHAMMADG/Book-My-Show.git'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh """
                    ${SCANNER_HOME}/bin/sonar-scanner \
                    -Dsonar.projectKey=first-project \
                    -Dsonar.projectName=first-project
                    """
                }
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE:latest ./bookmyshow-app'
            }
        }

        stage('Docker Push') {
            steps {
                withDockerRegistry(credentialsId: 'docker-cred', url: '') {
                    sh 'docker push $DOCKER_IMAGE:latest'
                }
            }
        }

        stage('Deploy to Docker Container') {
            steps {
                sh '''
                docker stop bms-container || true
                docker rm bms-container || true
                docker run -d -p 3000:3000 --name bms-container $DOCKER_IMAGE:latest
                '''
            }
        }

    }

    post {
        success {
            emailext(
                subject: "SUCCESS: Build and Deployment Completed",
                body: "The CI/CD pipeline completed successfully.",
                to: "mahammadghouseb@gmail.com"
            )
        }

        failure {
            emailext(
                subject: "FAILED: CI/CD Pipeline",
                body: "The CI/CD pipeline failed. Check Jenkins for details.",
                to: "mahammadghouseb@gmail.com"
            )
        }
    }
}
