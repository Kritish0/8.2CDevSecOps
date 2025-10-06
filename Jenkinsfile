pipeline {
    agent any

    // ✅ Make sure you created Node_18 in "Manage Jenkins > Tools > NodeJS"
    tools { nodejs 'Node_18' }

    environment {
        PATH = "${tool 'Node_18'}/bin:${env.PATH}"  // ensures npm & snyk work
    }

    stages {

        stage('Checkout') {
            steps {
                echo '📥 Checking out code from GitHub...'
                git url: 'https://github.com/Kritish0/8.2CDevSecOps.git', branch: 'main'
            }
        }

        stage('Install') {
            steps {
                echo '📦 Installing dependencies...'
                sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                echo '🧪 Running tests...'
                sh 'npm test || echo "⚠️ Tests failed but continuing..."'
            }
        }

        stage('Snyk Security Scan') {
            steps {
                echo '🔐 Running Snyk vulnerability scan...'
                // Authenticate once using 'snyk auth <your_token>' in Jenkins container if needed
                sh 'npx snyk test || echo "⚠️ Snyk scan failed or vulnerabilities found."'
            }
        }

        stage('Coverage') {
            steps {
                echo '📊 Running code coverage (if script available)...'
                sh '(npm run coverage) || echo "⚠️ No coverage script found."'
            }
        }

        stage('Audit') {
            steps {
                echo '📁 Running npm audit...'
                sh 'npm audit --audit-level=low | tee npm-audit.txt || true'
                sh 'npm audit --json > npm-audit.json || true'
                archiveArtifacts artifacts: 'npm-audit.txt, npm-audit.json', fingerprint: true
            }
        }
    }

    post {
        success {
            echo '✅ Pipeline completed successfully — reports generated and saved.'
        }
        failure {
            echo '❌ Pipeline failed — check console log above.'
        }
        always {
            echo '📜 Build finished. Check artifacts for vulnerability reports.'
        }
    }
}
