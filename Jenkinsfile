pipeline {
    agent none // Evita conflictos de contenedores a nivel global

    options {
        ansiColor('xterm')
    }

    stages {
        stage('Build Project') {
            agent {
                docker {
                    image 'node:22-slim'
                    args '--user root' // Permite permisos de escritura correctos en el workspace
                }
            }
            steps {
                // Limpia cualquier residuo de compilaciones congeladas anteriores
                cleanWs()
                
                // Instala dependencias y compila el proyecto
                sh 'npm ci'
                sh 'npm run build'
                
                // 📦 Saca una copia de los archivos compilados y node_modules para las etapas en paralelo
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
                        // Trae los archivos guardados en la etapa de Build
                        unstash 'compiled-app'
                        
                        // Ejecuta tus pruebas unitarias con Vitest
                        sh 'npm run test:unit'
                    }
                }

                stage('Integration Tests') {
                    agent {
                        docker {
                            // ⚡ SINTAXIS CORREGIDA: Imagen oficial desde el registro de Microsoft sin "://"
                            image '://microsoft.com'
                            
                            // ⚙️ Argumentos críticos para evitar que los navegadores de Playwright se congelen en Docker
                            args '--ipc=host --user root'
                        }
                    }
                    steps {
                        // Trae los archivos guardados en la etapa de Build
                        unstash 'compiled-app'
                        
                        // Ejecuta tus pruebas de integración con Playwright
                        sh 'npx playwright test'
                    }
                }
            }
        }

        stage('Deploy') {
            agent any // Se ejecuta instantáneamente en la máquina host sin descargar contenedores lentos
            steps {
                echo 'Mock deployment was successful!'
            }
        }
    }
}
