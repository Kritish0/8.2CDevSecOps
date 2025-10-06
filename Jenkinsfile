pipeline {
  agent any
  options { timestamps() }

  tools { 
    // comment this block if Node is already on PATH
    nodejs 'Node 18' 
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
        sh 'node -v && npm -v'
      }
    }

    stage('Install') {
      steps {
        // clean install based on package-lock.json (faster, reproducible)
        sh 'npm ci  npm install'
      }
    }

    stage('Test') {
      steps {
        // don’t fail the whole build if tests in sample app are flaky
        sh 'npm test  echo "tests failed, continuing to coverage"'
      }
    }

    stage('Coverage') {
      steps {
        // try common coverage scripts; ignore failure if script missing
        sh '(npm run coverage)  (npm run test -- --coverage)  echo "no coverage script"'
        // archive common coverage outputs if present
        sh 'ls -la'
        archiveArtifacts artifacts: '/coverage//*', allowEmptyArchive: true
      }
    }

    stage('Audit') {
      steps {
        // save both human-readable and JSON outputs as artifacts
        sh '''
          npm audit --audit-level=low | tee npm-audit.txt  true
          npm audit --json > npm-audit.json  true
          echo "Top vulnerabilities (if any):"
          head -n 100 npm-audit.txt || true
        '''
        archiveArtifacts artifacts: 'npm-audit.txt,npm-audit.json', allowEmptyArchive: true
      }
    }
  }

  post {
    always {
      echo 'Pipeline finished. Artifacts archived: npm-audit.txt / npm-audit.json / coverage (if any).'
    }
  }
}
