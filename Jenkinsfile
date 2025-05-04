pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/nissrinayy/sast-demo-app.git', branch: 'main'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                    python3 -m venv venv
                    venv/bin/pip install --upgrade pip
                    venv/bin/pip install bandit
                    if [ -f requirements.txt ]; then venv/bin/pip install -r requirements.txt; fi
                '''
            }
        }

        stage('SAST Analysis') {
            steps {
                sh '''
                    venv/bin/bandit -f xml -o bandit-output.xml -r . || true
                '''
                recordIssues tools: [bandit(pattern: 'bandit-output.xml')], qualityGates: [[threshold: 1, type: 'TOTAL', unstable: true]]
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
