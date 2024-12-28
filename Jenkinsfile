pipeline {
    agent any
    tools {
        nodejs 'nodejs-20.11' // Ensure this matches the Node.js version configured in Jenkins
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
            parallel {
                stage('Install Frontend Dependencies') {
                    steps {
                        script {
                            sh '''
                                cd Register/frontend/register
                                npm install
                            '''
                        }
                    }
                }
                stage('Install Backend Dependencies') {
                    steps {
                        script {
                            sh '''
                                cd Register/backend
                                npm install
                            '''
                        }
                    }
                }
            }
        }
        stage('Run Tests') {
            parallel {
                stage('Run Frontend Tests') {
                    steps {
                        script {
                            sh '''
                                cd Register/frontend/register
                                npm test
                            '''
                        }
                    }
                }
                stage('Run Backend Tests') {
                    steps {
                        script {
                            sh '''
                                cd Register/backend
                                npm test
                            '''
                        }
                    }
                }
            }
        }
        stage('SonarQube Analysis') {
            parallel {
                stage('SonarQube - Frontend') {
                    steps {
                        withSonarQubeEnv('sonarqube') { // Ensure this matches your SonarQube configuration in Jenkins
                            sh """
                                cd Register/frontend/register
                                npx sonar-scanner \\
                                -Dsonar.projectKey=web-mern
                                -Dsonar.sources=Register/frontend/register/src
                                -Dsonar.exclusions=**/node_modules/**,**/*.test.js \\
                                -Dsonar.host.url=http://localhost:9000 \\
                                -Dsonar.login=$SONAR_TOKEN
                            """
                        }
                    }
                }
                stage('SonarQube - Backend') {
                    steps {
                        withSonarQubeEnv('sonarqube') { // Ensure this matches your SonarQube configuration in Jenkins
                            sh """
                                cd Register/backend
                                npx sonar-scanner \\
                                -Dsonar.projectKey=web-mern \\
                                -Dsonar.sources=Register/backend/src
                                -Dsonar.exclusions=**/node_modules/**,**/*.test.js \\
                                -Dsonar.host.url=http://localhost:9000 \\
                                -Dsonar.login=$SONAR_TOKEN
                            """
                        }
                    }
                }
            }
        }
        stage('Build Application') {
            parallel {
                stage('Build Frontend') {
                    steps {
                        script {
                            sh '''
                                cd Register/frontend/register
                                npm run build
                            '''
                        }
                    }
                }
                stage('Build Backend') {
                    steps {
                        script {
                            // Backend build command, if applicable
                            sh '''
                                cd Register/backend
                                npm run build
                            '''
                        }
                    }
                }
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
