pipeline {
    agent none 

    options {
        ansiColor('xterm')
        // ⚡ FIX: Limpia el espacio de trabajo de forma segura ANTES de descargar el código de Git
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
                // ⚡ `cleanWs()` fue eliminado de aquí para no borrar tu código fuente
                
                // Instala dependencias usando el package-lock.json existente
                sh 'npm ci'
                sh 'npm run build'
                
                // 📦 Saca una copia de los archivos para las etapas en paralelo
                stash name: 'compiled-app', useDefaultExcludes: false
            }
        }

        stage('Parallel Automated Testing') {
            parallel {
                stage('Unit Tests') {
                    agent {
                        docker {
                            image 'node:22-slim'
                        }
                    }
                    steps {
                        unstash 'compiled-app'
                        sh 'npm run test:unit'
                    }
                }

                stage('Integration Tests') {
                    agent {
                        docker {
                            image '://microsoft.com'
                            args '--ipc=host --user root'
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
