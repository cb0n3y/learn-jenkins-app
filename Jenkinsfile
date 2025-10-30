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
    }
}
