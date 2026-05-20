pipeline {
    agent any

    options {
        ansiColor('xterm')
    }

    stages {

        stage('Create Playwright Image') {
            steps {
                script {

                    writeFile file: 'Dockerfile.playwright', text: '''
FROM node:22-bookworm

WORKDIR /app

COPY package*.json ./

RUN npm ci

RUN npx playwright install --with-deps chromium

COPY . .
'''

                    sh 'docker build -t my-playwright -f Dockerfile.playwright .'
                }
            }
        }

        stage('build') {
            agent {
                docker {
                    image 'my-playwright'
                    args '--ipc=host --init --user root'
                    reuseNode true
                }
            }
            steps {
                sh 'npm run build'
            }
        }

        stage('unit tests') {
            agent {
                docker {
                    image 'my-playwright'
                    args '--ipc=host --init --user root'
                    reuseNode true
                }
            }
            steps {
                sh 'npx vitest run --reporter=verbose'
            }
        }

        stage('e2e') {
            agent {
                docker {
                    image 'my-playwright'
                    args '--ipc=host --init --user root'
                    reuseNode true
                }
            }
            environment {
                CI = 'true'
                E2E_BASE_URL = 'https://spanish-cards.netlify.app/'
            }
            steps {
                sh 'npx playwright test --workers=1'
            }
        }
    }

    post {
        always {
            sh 'docker image rm -f my-playwright || true'
            sh 'rm -f Dockerfile.playwright || true'
        }
    }
}