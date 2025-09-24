pipeline {
    agent {
        docker {
            image 'node:16'
            args '-u root --network=project2-compose_default -v /var/run/docker.sock:/var/run/docker.sock'
        }
    }
    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')
    }
    stages {
        stage('Install Dependencies') {
            steps {
                sh 'npm install --save'
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
