pipeline {
  agent any

  environment {
    VENV = 'venv'
    SAST_SARIF = 'bandit-output.sarif'
  }

  stages {
    stage('Checkout') {
      steps {
        git url: 'https://github.com/nissrinayy/sast-demo-app.git', branch: 'main'
      }
    }

    stage('Install Dependencies') {
      steps {
        sh '''
          python3 -m venv ${VENV}
          ${VENV}/bin/pip install --upgrade pip
          ${VENV}/bin/pip install bandit
          if [ -f requirements.txt ]; then
            ${VENV}/bin/pip install -r requirements.txt
          fi
        '''
      }
    }

    stage('Run Bandit (SARIF)') {
      steps {
        sh """
          ${VENV}/bin/bandit -f sarif -o ${SAST_SARIF} -r . || true
        """
      }
    }

    stage('Publish Issues') {
      steps {
        // pake parser SARIF bawaan Warnings NG
        recordIssues tools: [ sarif(pattern: "${SAST_SARIF}") ],
                     qualityGates: [[threshold: 1, type: 'TOTAL', unstable: true]]
      }
    }
  }

  post {
    always {
      cleanWs()
    }
  }
}
