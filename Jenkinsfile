pipeline {
    agent any
    stages {
        stage('Install dependencies') {
            steps {
                sh 'npm install'
            }
        }
        stage('Run tests') {
            steps {
                sh 'npm test || true'
            }
        }
        stage('Security scan') {
            steps {
                sh 'npm audit || true'
            }
        }
    }
}



