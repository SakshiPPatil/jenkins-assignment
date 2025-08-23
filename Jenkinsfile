pipeline {
    agent any

    environment {
        DEPLOY_USER = 'ubuntu'
        DEPLOY_HOST = '34.203.233.215'
        APP_PORT    = '3000'
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/SakshiPPatil/jenkins-assignment.git', branch: 'main'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'npm test || true'
            }
        }

        stage('Deploy') {
            steps {
                sshagent(['6645fd39-248b-4d3c-af95-c7c5fe3d2aa9']) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ${DEPLOY_USER}@${DEPLOY_HOST} <<EOF
                            rm -rf /home/ubuntu/app
                            git clone https://github.com/SakshiPPatil/jenkins-assignment.git /home/ubuntu/app
                            cd /home/ubuntu/app
                            npm install
                           pm2 kill || true
                           pm2 start server.js --name myapp
                        EOF
                    """
                }
            }
        }
    }
}
