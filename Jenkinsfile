pipeline {
  agent any

  stages {
    stage('Tool Install') {
      steps {
        script {
          def nodeHome = tool name: 'Node_18', type: 'jenkins.plugins.nodejs.tools.NodeJSInstallation'
          env.PATH = "${nodeHome}/bin:${env.PATH}"
        }
      }
    }

    stage('Checkout') {
      steps {
        echo '📥 Checking out code from GitHub...'
        // CHANGE this URL to your repo if needed
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

    stage('Test & Snyk') {
      steps {
        echo '🧪 Running tests and security scan...'
        sh '''
          if npm run | grep -q "^  test"; then
            npm test  echo "Tests failed but continuing..."
          else
            echo "No test script found."
          fi

          # Snyk security test
          snyk test  echo "⚠️ Vulnerabilities found. See Snyk output above."
        '''
      }
    }

    stage('Coverage') {
      steps {
        echo '📊 Checking coverage...'
        sh '(npm run coverage)  echo "No coverage script found"'
      }
    }

    stage('Audit') {
      steps {
        echo '🔐 Running npm audit...'
        sh '''
          npm audit --audit-level=low | tee npm-audit.txt  true
          npm audit --json > npm-audit.json || true
        '''
        archiveArtifacts artifacts: 'npm-audit.txt,npm-audit.json', fingerprint: true
      }
    }
  }

  post {
    always {
      echo '✅ Pipeline finished. Reports saved as artifacts.'
    }
    failure {
      echo '❌ Pipeline failed. Check the console log above.'
    }
  }
}
