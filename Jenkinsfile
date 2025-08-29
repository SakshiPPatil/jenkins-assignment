pipeline {
    agent any

    environment {
        DEPLOY_USER = 'ubuntu'
        DEPLOY_HOST = '34.201.170.247'
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
                sshagent(['46a962c8-d986-4203-9318-cdd7adbf4525']) {
                    sh '''#!/bin/bash
ssh -o StrictHostKeyChecking=no ${DEPLOY_USER}@${DEPLOY_HOST} << 'EOF'
rm -rf /home/ubuntu/app
git clone https://github.com/SakshiPPatil/jenkins-assignment.git /home/ubuntu/app
cd /home/ubuntu/app
npm install
if ! command -v pm2 &> /dev/null; then
    sudo npm install -g pm2
fi
pm2 delete all || true
pm2 start server.js --name myapp
EOF
                    '''
                }
            }
        }

    }
}


//delete this linies
stage('Deploy') {
            steps {
                sshagent(['46a962c8-d986-4203-9318-cdd7adbf4525']) {
                    sh '''#!/bin/bash
ssh -o StrictHostKeyChecking=no ${DEPLOY_USER}@${DEPLOY_HOST} << 'EOF'
rm -rf /home/ubuntu/app
git clone https://github.com/SakshiPPatil/jenkins-assignment.git /home/ubuntu/app
cd /home/ubuntu/app
npm install
if ! command -v pm2 &> /dev/null; then
    sudo npm install -g pm2
fi
pm2 delete all || true
pm2 start server.js --name myapp
EOF
                    '''
                }
            }
        }

    }
}
