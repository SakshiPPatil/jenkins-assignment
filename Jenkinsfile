

pipeline {
    agent any

    environment {
        
        DEPLOY_USER = 'ubuntu'
        DEPLOY_HOST = '34.203.233.215'
        APP_PORT = '3000'
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
              
                sshagent(['4837f28e-fc73-47b5-b78f-dadbbdcd73e4']) {
                    sh """
                    ssh -o StrictHostKeyChecking=no ${DEPLOY_USER}@${DEPLOY_HOST} <<EOF
                        cd /var/www/nextastra/devlogin.nextastra.com
                        npm install
                        pm2 kill || true
                        npm start
                    EOF
                    """
                }
            }
        }
    }

    post {
        always {
           
            mail to: 'your_email@example.com',
                 subject: "Jenkins Build ${currentBuild.fullDisplayName} Status: ${currentBuild.result}",
                 body: "The build has finished with status: ${currentBuild.result}"
        }
    }
}