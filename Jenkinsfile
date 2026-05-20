pipeline {
    agent none // ⚡ FIX 1: Remove top-level docker to prevent nested container tracking errors

    options {
        ansiColor('xterm')
    }

    stages {
        stage('build and test') {
            agent {
                docker {
                    image 'node:22-slim' // ⚡ FIX 2: Swap alpine for slim. Includes full system tools natively.
                }
            }
            stages { // Nested stages keep build and test organized in one container instance
                stage('Build') {
                    steps {
                        sh 'npm ci'
                        sh 'npm run build'
                    }
                }
                stage('Unit Tests') {
                    steps {
                        sh 'npm run test:unit'
                    }
                }
                stage('integration tests') {
                    agent {
                        docker {
                            image 'mcr.microsoft.com/playwright:v1.54.2-jammy'
                            reuseNode true
                        }
                    }
                    steps {
                        sh 'npx playwright test'
                    }
                }
            }
        }

        stage('deploy') {
            agent any // ⚡ FIX 3: Runs instantly on the host machine. No slow image downloads or tracking bugs.
            steps {
                echo 'Mock deployment was successful!'
            }
        }
    }
}