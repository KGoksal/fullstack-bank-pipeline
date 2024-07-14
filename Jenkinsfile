pipeline {
    agent any
    
    tools {
        jdk 'jdk17'
        nodejs 'node16'
    }
    
    // Define environment variables
    environment {
        SCANNER_HOME = tool 'sonar-scanner' // 'sonar-scanner' is configured as a tool in Jenkins
    }
    
    stages {
        // Stage to clone Git repository
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/raphaelalmeidamartins/fullstack-bank.git'
            }
        }
        
        // Stage for OWASP Dependency Check
        stage('OWASP SCAN') {
            steps {
                // Using OWASP Dependency Check plugin
                dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'DC'
            }
        }
        
        // Stage for Trivy Filesystem Scan
        stage('TRIVY FS SCAN') {
            steps {
                // Running Trivy vulnerability scanner on the filesystem
                sh "trivy fs ."
            }
        }
        
        // Stage for SonarQube analysis
        stage('SONARQUBE ANALYSIS') {
            steps {
                // Using SonarQube scanner to analyze the project
                withSonarQubeEnv('sonar') {
                    sh "${SCANNER_HOME}/bin/sonar-scanner -Dsonar.projectName=Bank -Dsonar.projectKey=Bank"
                }
            }
            
        }
        
         stage('Installed Dependencies') {
            steps {
                sh "npm install"
            }
        }
        stage('Backend') {
            steps {
                dir('/root/.jenkins/workspace/Bank/app/backend') {
                    sh "npm install"
                }
            }
        }
        stage('Frontend') {
            steps {
                dir('/root/.jenkins/workspace/Bank/app/frontend') {
                    sh "npm install"
                }
            }
        }
        stage('Deploy to Container') {
            steps {
                sh "npm run compose:up -d"
                }
            }
        }
    }
}
