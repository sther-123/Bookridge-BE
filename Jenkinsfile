pipeline {
    agent none

    environment {
        IMAGE = 'kit3at/bookridge-be'
    }

    stages {
        stage('Install') {
            agent {
                docker {
                    image 'node:20'
                    args '-e npm_config_cache=/tmp/.npm'
                }
            }
            steps {
                sh 'npm ci'
            }
        }

        stage('Docker Build') {
            agent any
            steps {
                sh 'docker build -t ${IMAGE}:${BUILD_NUMBER} -t ${IMAGE}:latest .'
            }
        }

        stage('Docker Push') {
            agent any
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-cred',
                    usernameVariable: 'DH_USER',
                    passwordVariable: 'DH_PASS'
                )]) {
                    sh '''
                        echo "$DH_PASS" | docker login -u "$DH_USER" --password-stdin
                        docker push ${IMAGE}:${BUILD_NUMBER}
                        docker push ${IMAGE}:latest
                        docker logout
                    '''
                }
            }
        }
    }
}
