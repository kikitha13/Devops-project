pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('docker1234')  // Your Docker Hub username/password credential ID in Jenkins
        VM_SSH_KEY = 'myvmid'                             // SSH private key credential ID in Jenkins
        VM_HOST = '13.126.72.51'                          // Your VM's public IP or hostname
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
                    withCredentials([usernamePassword(credentialsId: 'docker123', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    }
                }
            }
        }

        stage('Push Docker Images') {
            steps {
                script {
                    sh 'docker push kikitha/mean-backend:latest'
                    sh 'docker push kikitha/mean-frontend:latest'
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

               
