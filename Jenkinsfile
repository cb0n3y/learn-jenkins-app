pipeline {
    agent {
        docker {
            image 'node:18-alpine'
            label 'linux docker java21'
            reuseNode true
        }
    }

    environment {
        FILE_NAME = 'container'
        NPM_CONFIG_CACHE = "${WORKSPACE}/.npm" 
        BUILD_FOLDER = "${WORKSPACE}/build"
    }

    stages {
        stage('Build') {
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

        stage('Test') {
            steps {
                echo "[+] Testing ..."
                script {
                    if (fileExists("$BUILD_FOLDER/index.html")) {
                        echo "[+] File found"
                    } else {
                        echo "[i] File not found"
                    }
                }
            }
        }
    }
}
