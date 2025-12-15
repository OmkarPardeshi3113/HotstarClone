pipeline {
    agent any
    tools {
        jdk 'jdk17'
        nodejs 'node16'
    }
    environment {
        SCANNER_HOME = tool 'SonarQubeScanner'
    }
    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }
        stage('Checkout from Git') {
            steps {
                git branch: 'main', url: 'https://github.com/OmkarPardeshi3113/HotstarClone.git'
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarCloud') {
                    sh '''
                    $SCANNER_HOME/bin/sonar-scanner \
                    -Dsonar.projectKey=hotstar-clone \
                    -Dsonar.organization=omkarpardeshi3113\
                    -Dsonar.sources=src \
                    -Dsonar.host.url=https://sonarcloud.io
                    '''
                }
            }
        }
        stage('Security Scan (Trivy)') {
            steps {
                sh "trivy fs . > trivy-report.txt"
            }
        }
        stage('Install Dependencies') {
            steps {
                sh "npm install"
            }
        }
        stage('Build & Deploy to Docker') {
            steps {
                script {
                    sh 'docker build -t hotstar-clone .'
                    // Stop old container if running (ignore errors if not existing)
                    sh 'docker stop hotstar-container || true'
                    sh 'docker rm hotstar-container || true'
                    // Run new container on Port 3000
                    sh 'docker run -d -p 3000:80 --name hotstar-container hotstar-clone'
                }
            }
        }
    }
}
