pipeline {
    agent none 

    options {
        ansiColor('xterm')
        skipDefaultCheckout(false) 
    }

    stages {
        stage('Build Project') {
            agent {
                docker {
                    image 'node:22-slim'
                    args '--user root' 
                }
            }
            steps {
                sh 'npm ci'
                sh 'npm run build'
                stash name: 'compiled-app', useDefaultExcludes: false
            }
        }

        stage('Parallel Automated Testing') {
            parallel {
                stage('Unit Tests') {
                    agent {
                        docker {
                            image 'node:22-slim'
                            args '--user root' 
                        }
                    }
                    steps {
                        unstash 'compiled-app'
                        sh 'npm run test:unit'
                    }
                }

                // ⚡ TRUCO: Cambiamos el nombre a 'Playwright E2E Tests' para obligar a Jenkins a romper la caché vieja
                stage('Playwright E2E Tests') { 
                    agent {
                        docker {
                            // Ruta limpia sin protocolos web de por medio
                            image 'mcr.microsoft.com/playwright:v1.54.2-jammy'
                            args '--ipc=host --user root' // Evita cierres inesperados de Chromium
                        }
                    }
                    steps {
                        unstash 'compiled-app'
                        sh 'npx playwright test'
                    }
                }
            }
        }

        stage('Deploy') {
            agent any 
            steps {
                echo 'Mock deployment was successful!'
            }
        }
    }
}
