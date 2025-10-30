pipeline {
    agent any
    
    environment {
        FILE_NAME = 'container'
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
                '''
            }
        }
    }
}
