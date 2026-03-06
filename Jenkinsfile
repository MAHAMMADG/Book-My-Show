pipeline {
    agent any

    tools {
        nodejs 'node23'
    }

    stages {

        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout Code') {
            steps {
                git branch: 'mahi-branch', url: 'https://github.com/MAHAMMADG/Book-My-Show.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                dir('bookmyshow-app') {
                    sh 'npm install'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                dir('bookmyshow-app') {
                    withSonarQubeEnv('sonarqube') {
                        sh '''
                        ${SONAR_SCANNER_HOME}/bin/sonar-scanner \
                        -Dsonar.projectKey=bookmyshow \
                        -Dsonar.sources=.
                        '''
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                dir('bookmyshow-app') {
                    sh 'docker build -t bookmyshow-app .'
                }
            }
        }

        stage('Deploy Docker Container') {
            steps {
                sh '''
                docker stop bookmyshow || true
                docker rm bookmyshow || true
                docker run -d -p 3000:3000 --name bookmyshow bookmyshow-app
                '''
            }
        }

    }

    post {

        success {
            emailext(
                subject: "Jenkins Build SUCCESS",
                body: """
Pipeline executed successfully.

Project: BookMyShow
Build Number: ${env.BUILD_NUMBER}
Job Name: ${env.JOB_NAME}

Application deployed successfully.
""",
                to: "mahammadghouseb@gmail.com"
            )
        }

        failure {
            emailext(
                subject: "Jenkins Build FAILED",
                body: """
Pipeline execution FAILED.

Project: BookMyShow
Build Number: ${env.BUILD_NUMBER}
Job Name: ${env.JOB_NAME}

Check Jenkins console logs.
""",
                to: "mahammadghouseb@gmail.com"
            )
        }

        always {
            echo "Pipeline finished."
        }
    }
}