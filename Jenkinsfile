pipeline {
    agent any
    tools {
        nodejs 'NodeJS 20' // Ensure this matches the Node.js version configured in Jenkins
    }
    environment {
        SONAR_TOKEN = credentials('Sonarqube-token') // Replace with your SonarQube token credentials ID
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }
        stage('Run Tests') {
            steps {
                sh 'npm test'
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonarqube') { // Ensure this matches your SonarQube configuration in Jenkins
                    sh """
                        npx sonar-scanner 
                        -Dsonar.projectKey=mern-project 
                        -Dsonar.sources=src 
                        -Dsonar.exclusions=**/node_modules/**,**/*.test.js \\
                        -Dsonar.host.url=http://localhost:9000 
                        -Dsonar.login=$SONAR_TOKEN
                    """
                }
            }
        }
        stage('Build Application') {
            steps {
                sh 'npm run build'
            }
        }
    }
    post {
        success {
            echo 'Pipeline completed successfully.'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}
