pipeline {
    agent any

    options {
        ansiColor('xterm')
    }

    stages {

        stage('build') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.54.2-jammy'
                    args '--ipc=host --init --user root'
                    reuseNode true
                }
            }
            steps {
                sh 'npm ci'
                sh 'npm run build'
            }
        }

        stage('unit tests') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.54.2-jammy'
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
                    image 'mcr.microsoft.com/playwright:v1.54.2-jammy'
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
}