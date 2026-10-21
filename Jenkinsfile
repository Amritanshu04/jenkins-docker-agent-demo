
pipeline {
    agent {
        docker {
            image 'node:22-alpine'
        }
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Check Node.js') {
            steps {
                sh 'node --version'
                sh 'npm --version'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm ci'
            }
        }

        stage('Test') {
            steps {
                sh 'npm test'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t jenkins-docker-agent-demo:latest .'
            }
        }

        stage('Run Container Test') {
            steps {
                sh '''
                    docker rm -f jenkins-demo-test 2>/dev/null || true
                    docker run -d --name jenkins-demo-test jenkins-docker-agent-demo:latest
                    sleep 2
                    docker logs jenkins-demo-test
                    docker rm -f jenkins-demo-test
                '''
            }
        }
    }
}
