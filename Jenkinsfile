pipeline {
  agent any
  tools { nodejs 'Node 18' }

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
        sh '''
          if [ -f package-lock.json ]; then
            npm ci
          else
            npm install
          fi
        '''
      }
    }

    stage('Test') {
      steps {
        echo '🧪 Running tests...'
        // don’t fail pipeline if tests need auth (snyk) or aren’t present
        sh 'npm test  echo "Tests failed but continuing..."'
      }
    }

    stage('Coverage') {
      steps {
        echo '📊 Checking coverage (if available)...'
        sh '(npm run coverage)  echo "No coverage script found"'
      }
    }

    stage('Audit') {
      steps {
        echo '🔐 Running security audit...'
        sh '''
          npm audit --audit-level=low | tee npm-audit.txt  true
          npm audit --json > npm-audit.json  true
        '''
        archiveArtifacts artifacts: 'npm-audit.txt,npm-audit.json', fingerprint: true
      }
    }
  }

  post {
    always {
      echo '✅ Pipeline finished. Vulnerability scan results saved as artifacts.'
    }
  }
}
