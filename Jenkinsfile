pipeline {
    agent none // Prevents global container conflicts

    options {
        ansiColor('xterm')
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
                // 📦 Save the built project files and node_modules for the parallel testing stages
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
                        // Runs integration tests instantly using MCR pre-installed browsers
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
