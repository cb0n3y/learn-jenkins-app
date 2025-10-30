pipeline {
    agent any
    
    environment {
        FILE_NAME = 'container'
    }

    stages {
        stage('w/o docker') {
            steps {
                sh '''
                    echo "Without docker"
                    ls -lha
                    touch $FILE_NAME-no.txt
                '''
            }
        }
        
        stage('with docker') {
            agent {
                docker {
                    image 'node:18-alpine'
                    label 'linux docker java21'
                    //reuseNode true
                }
            }
            steps {
                sh '''
                    echo "With docker"
                    ls -lha
                    touch $FILE_NAME-yes.txt
                '''
            }
        }
    }
}
