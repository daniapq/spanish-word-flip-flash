pipeline {
    agent none // ⚡ FIX 1: Remove top-level docker to prevent nested container tracking errors

    options {
        ansiColor('xterm')
    }

    stages {
        stage('build and test') {
            agent {
                docker {
                    image 'node:22-slim'
                    // ⚡ FIX: Forces the container to run as root so apt-get dependencies can install
                    args '--user root' 
                }
            }
            stages { // Nested stages keep build and test organized in one container instance
                stage('Build') {
                    steps {
                        sh 'npm ci'
                        sh 'npm run build'
                    }
                }
                stage ('test') {
                    parallel {
                        stage('Unit Tests') {
                            steps {
                                sh 'npm run test:unit'
                            }
                        }
                        stage('Integration Tests') {
                            agent {
                                docker {
                                    // ⚡ FIX 1: Correct MCR image registry path and tag
                                    image '://microsoft.com'
                                    args '--ipc=host --user root'
                                }
                            }
                            steps {
                                // 📥 Bring the files in if you are using stash/unstash
                                unstash 'project-files' 
                                
                                // ⚡ FIX 2: Pre-installed browsers exist, go straight to testing!
                                sh 'npx playwright test'
                            }
                        }
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