pipeline {
    agent { label 'agent1' }
    environment {
        IMAGE_NAME = 'belalelnady/simple-app:latest'
        REMOTE_HOST = '192.168.1.111'
        REMOTE_USER = 'belal'
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${IMAGE_NAME}", './web-app')
                }
            }
        }
        
        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerHub', 
                                                  usernameVariable: 'DOCKER_USER', 
                                                  passwordVariable: 'DOCKER_PASS')]) {
                    script {
                        sh """
                            docker login -u ${DOCKER_USER} -p ${DOCKER_PASS}
                            docker push ${IMAGE_NAME}
                        """
                    }
                }
            }
        }

        stage('Deploy to Remote Server') {
            steps {
                sshagent (credentials: ['u-server']) {
                    sh """
                        ssh -o StrictHostKeyChecking=no belal@192.168.1.111 '
                        docker run -d -p 8770:80 --name simple-app ${IMAGE_NAME}'
                    """
                }
            }
        }
    }
    post {
        success{
            sh 'echo sucess'
        }
        always {
            sh 'echo "build is finished"'
            // cleanWs()
        }
    }
}
