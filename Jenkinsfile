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
            parallel {
                stage('Install Frontend Dependencies') {
                    steps {
                        dir('frontend/register') {
                            sh '''
                                npm install
                            '''
                        }
                    }
                }
                stage('Install Backend Dependencies') {
                    steps {
                        dir('backend') {
                            sh '''
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
                        dir('frontend/register') {
                            sh '''
                                npm test
                            '''
                        }
                    }
                }
                stage('Run Backend Tests') {
                    steps {
                        dir('backend') {
                            sh '''
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
                        dir('frontend/register') {
                            withSonarQubeEnv('sonarqube') { // Ensure this matches your SonarQube configuration
                                sh '''
                                    npm run sonar -- \
                                    -Dsonar.projectKey=web-mern \
                                    -Dsonar.sources=src \
                                    -Dsonar.host.url=http://localhost:9000 \
                                    -Dsonar.login=$SONAR_TOKEN
                                '''
                            }
                        }
                    }
                }
                
            }
        }
        stage('Build Application') {
            parallel {
                stage('Build Frontend') {
                    steps {
                        dir('frontend/register') {
                            sh '''
                                npm run build
                            '''
                        }
                    }
                }
                stage('Build Backend') {
                    steps {
                        dir('backend') {
                            sh '''
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
