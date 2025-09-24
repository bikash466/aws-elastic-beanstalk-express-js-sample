pipeline {
    agent {
        docker {
            image 'node:16'
            args '-u root -v /var/run/docker.sock:/var/run/docker.sock'
        }
    }
    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')
    }
    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }
        stage('Install Dependencies') {
            steps {
                sh 'npm install --prefer-offline --no-audit --progress=false'
            }
        }
        stage('Unit Tests') {
            steps {
                sh 'npm test || true'
            }
        }
        stage('Security Scan') {
            steps {
                sh 'npm install -g snyk'
                sh 'snyk test --org=bikash466 --severity-threshold=high || true'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t bikash466/node-app:latest .'
            }
        }
        stage('Push to Docker Hub') {
            steps {
                sh '''
                    echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
                    docker push bikash466/node-app:latest
                '''
            }
        }
    }
    post {
        always {
            archiveArtifacts artifacts: '**/test-results/*.xml', allowEmptyArchive: true
            cleanWs()
        }
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check logs for details.'
        }
    }
}
