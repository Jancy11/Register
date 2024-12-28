pipeline {
    agent any
    tools {
        nodejs 'nodejs-20.11' // Ensure this matches the Node.js version configured in Jenkins
    }
    environment {
        SONAR_TOKEN = credentials('Sonarqube-token') // Replace with your credentials ID for the SonarQube token
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Install Dependencies') {
            steps {
                dir('frontend/register') {
                    sh 'npm install'
                }
            }
        }
        stage('Run Tests') {
            steps {
                dir('frontend/register') {
                    sh 'npm test -- --passWithNoTests'
                }
            }
        }
        stage('SonarQube Analysis') {
            steps {
                dir('frontend/register') {
                    withSonarQubeEnv('sonarqube') { // Ensure this matches your SonarQube configuration
                        sh '''
                            npm run sonar -- \
                            -Dsonar.projectKey=frontend-web-mern \
                            -Dsonar.sources=src \
                            -Dsonar.host.url=http://localhost:9000 \
                            -Dsonar.login=$SONAR_TOKEN
                        '''
                    }
                }
            }
        }
        stage('Build Application') {
            steps {
                dir('frontend/register') {
                    sh 'npm run build'
                }
            }
        }
    }
    post {
        success {
            echo 'Frontend pipeline completed successfully.'
        }
        failure {
            echo 'Frontend pipeline failed.'
        }
    }
}
