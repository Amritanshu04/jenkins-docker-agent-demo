
pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Check Node.js') {
            steps {
                sh '''
                    docker run --rm \
                      -v "$WORKSPACE:/app" \
                      -w /app \
                      node:22-alpine \
                      sh -c 'node --version && npm --version'
                '''
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                    docker run --rm \
                      -v "$WORKSPACE:/app" \
                      -w /app \
                      node:22-alpine \
                      npm ci
                '''
            }
        }

        stage('Test') {
            steps {
                sh '''
                    docker run --rm \
                      -v "$WORKSPACE:/app" \
                      -w /app \
                      node:22-alpine \
                      npm test
                '''
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

    post {
        always {
            sh 'docker image ls jenkins-docker-agent-demo:latest || true'
        }
    }
}
