pipeline {
    agent {
        docker {
            image 'node:16'
            args '-u root'
        }
    }
    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')
        SNYK_TOKEN = credentials('snyk-token')
    }
    stages {
        stage('Install Dependencies') {
            steps {
                sh 'npm install --save'
            }
        }
        stage('Unit Tests') {
            steps {
                sh 'npm test'
            }
        }
        stage('Security Scan') {
            steps {
                sh 'npm install -g snyk --unsafe-perm'
                sh 'snyk auth $SNYK_TOKEN'
                sh 'snyk test --org=ed7c4df3-5b22-4f58-a6af-b1b01c5f3ea2 --severity-threshold=high'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t bikash466/node-app:latest .'
            }
        }
        stage('Push to Registry') {
            steps {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                sh 'docker push bikash466/node-app:latest'
            }
        }
    }
    post {
        always {
            archiveArtifacts artifacts: '**/test-results/*.xml', allowEmptyArchive: true
            cleanWs()
        }
    }
}
