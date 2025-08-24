pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('docker1234')  // Docker Hub credentials ID
        VM_SSH_KEY = 'myvmid'                             // SSH private key credential ID
        //VM_HOST = '13.126.72.51'                // Replace with your VM address
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Backend Image') {
            steps {
                script {
                    docker.build('kikitha/mean-backend:latest', './backend')
                }
            }
        }

        stage('Build Frontend Image') {
            steps {
                script {
                    docker.build('kikitha/mean-frontend:latest', './frontend')
                }
            }
        }

        stage('Login to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('', DOCKERHUB_CREDENTIALS) {
                        echo "Logged into Docker Hub"
                    }
                }
            }
        }

        stage('Push Docker Images') {
            steps {
                script {
                    docker.withRegistry('', DOCKERHUB_CREDENTIALS) {
                        docker.image('kikitha/mean-backend:latest').push()
                        docker.image('kikitha/mean-frontend:latest').push()
                    }
                }
            }
        }

        stage('Deploy to VM') {
            steps {
                sshagent([VM_SSH_KEY]) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ubuntu@${VM_HOST} 'cd ~/mean-app && docker-compose pull && docker-compose up -d'
                    """
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check logs for details.'
        }
    }
}
       
