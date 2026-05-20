pipeline {
    agent {
        docker {
            image 'node:22-alpine'
            // This shares the agent container across all stages naturally
        }
    }
    
    options {
        ansiColor('xterm')
    }

    stages {
        stage('build') {
            steps {
                sh 'npm ci'
                sh 'npm run build'
            }
        }

        stage('test') {
            parallel {
                stage('unit tests') {
                    steps {
                        // Changed to 'npm run' to strictly use the installed local vitest
                        sh 'npm run test:unit' 
                    }
                }
            }
        }

        stage('deploy') {
            // We temporarily override the agent just for deployment
            agent {
                docker {
                    image 'alpine'
                }
            }
            steps {
                echo 'Mock deployment was successful!'
            }
        }
    }
}