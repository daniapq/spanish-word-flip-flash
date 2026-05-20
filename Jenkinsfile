pipeline {
    agent any

    options {
        ansiColor('xterm')
    }

    stages {

        stage('build') {
            agent {
                docker {
                    image 'node:22-bookworm'
                    args '--user root'
                    reuseNode true
                }
            }
            steps {
                sh 'npm ci'
                sh 'npm run build'
            }
        }

        stage('test') {
            parallel {

                stage('unit tests') {
                    agent {
                        docker {
                            image 'node:22-bookworm'
                            args '--user root'
                            reuseNode true
                        }
                    }
                    steps {
                        sh 'npm ci'
                        sh 'npx vitest run --reporter=verbose'
                    }
                }

                stage('integration tests') {
                    agent {
                        docker {
                            image 'node:22-bookworm'
                            args '--user root'
                            reuseNode true
                        }
                    }
                    steps {
                        echo 'integration tests completed!'
                    }
                }
            }
        }

        stage('deploy') {
            agent {
                docker {
                    image 'alpine'
                    reuseNode true
                }
            }
            steps {
                echo 'Mock deployment was successful!'
            }
        }

        stage('e2e') {
            agent {
                docker {
                    image 'node:22-bookworm'
                    args '--user root'
                    reuseNode true
                }
            }
            environment {
                CI = 'true'
                E2E_BASE_URL = 'https://spanish-cards.netlify.app/'
            }
            steps {
                sh 'npm ci'
                sh 'npx playwright install --with-deps chromium'
                sh 'npx playwright test'
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished.'
        }

        success {
            echo 'Pipeline completed successfully!'
        }

        failure {
            echo 'Pipeline failed.'
        }
    }
}