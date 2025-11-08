pipeline {
    agent none

    environment {
        FILE_NAME = 'container'
        NPM_CONFIG_CACHE = "${WORKSPACE}/.npm" 
        BUILD_FOLDER = "${WORKSPACE}/build"
    }

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    label 'linux docker java21'
                    reuseNode true
                }
            }

            steps {
                sh '''
                    ls -lha
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -lha
                '''
            }
        }

        stage('E2E') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                    label 'linux docker java21'
                    reuseNode true
                }
            }
            steps {
                echo "E2E"
                sh '''
                    npm install serve
                    serve -s build
                    npx playwright test
                '''
            }
        }

        stage('Test') {
            steps {
                echo "[+] Test stage"
                script {
                    if (fileExists("$BUILD_FOLDER/index.html")) {
                        echo "[+] Build artifact found: $BUILD_FOLDER/index.html"
                    } else {
                        // error() is a Jenkins pipeline step - it throws an exception and marks the stage (and pipeline) as FAILED.
                        error("[!] Build artifact not found at $BUILD_FOLDER/index.html — failing pipeline.")
                    }

                    sh 'npm test'
                }
            }
        }
    }

    post {
        always {
            junit 'test-results/junit.xml'
        }
    }
}
