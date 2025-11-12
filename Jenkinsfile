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
                    # Install required package
                    npm install react-scripts@5.0.1 --save
                    # Verify react-scripts is installed
                    npx react-scripts --version
                    npm run build
                    ls -lha
                '''
                stash includes: 'build/**', name: 'react-build'
            }
        }

        stage('E2E') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.56.1-jammy'
                    label 'linux docker java21'
                    reuseNode true
                }
            }
            steps {
                unstash 'react-build'
                echo "Starting E2E tests..."
                sh '''
                    npm install serve
                    export PATH=$PATH:./node_modules/.bin
                    serve -s build -l 3000 &
                    npx playwright test --reporter=html
                '''
            }
        }

        stage('Test') {
            agent {
                docker {
                    image 'node:18-alpine'
                    label 'linux docker java21'
                    reuseNode true
                }
            }

            steps {
                echo "[+] Running unit tests..."
                unstash 'react-build'
                sh '''
                    if [ ! -f "build/index.html" ]; then
                        echo "[!] Build artifact not found — failing."
                        exit 1
                    fi
                    npm test
                '''
            }
        }
    }

    post {
        always {
            junit 'test-results/junit.xml'
            publishHTML([
                allowMissing: false,
                alwaysLinkToLastBuild: false,
                icon: '',
                keepAll: false,
                reportDir: 'playwright-report',
                reportFiles: 'index.html',
                reportName: 'Playwright HTML Report',
                reportTitles: '',
                useWrapperFileDirectly: true
            ])
            cleanWs()
        }
    }
}
