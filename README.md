
# Set up a complete CI/CD workflow and deployment pipeline for a Node.js application with secureaccess and monitoring.  <br/>

**Steps**  <br/>

1) Clone and Validate Application:  <br/>
        cloned repository into the aws server  <br/>
    ```
        git clone  https://github.com/SakshiPPatil/jenkins-assignment.git
    ```

2) CI/CD Pipeline with Jenkins:  <br/>

 Install Jenkins on a Linux server.  <br/>

```
sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins -y

```

Jenkins job or pipeline  <br/>

```
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
                sshagent(['4837f28e-fc73-47b5-b78f-dadbbdcd73e4']) {
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



```


3) Implement Matrix-Based Security in Jenkins. <br/>

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/7695d191-5453-4b63-9ac4-568cf23e1416" />


    
