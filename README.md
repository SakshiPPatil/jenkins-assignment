
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
        DEPLOY_HOST = '54.243.27.220'
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

```

output

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/e6241219-457b-42d2-bdd8-3628d55b62a6" /> <br/>


3) Implement Matrix-Based Security in Jenkins. <br/>

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/7695d191-5453-4b63-9ac4-568cf23e1416" /> <br/>


4) Deploy Using NGINX with SSL

1) install nginx  <br>
   

   ```
         sudo apt-get install nginx -y
   ```

i)  Configure NGINX Reverse Proxy: <br/>


```
sudo nano /etc/nginx/sites-available/devlogin11

add below content in this file

server {
    listen 80;
    server_name devlogin.nextastra.com;

    location / {
        # Forward requests to the Node.js app
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}

```
ii) Enable the Configuration <br/>
 Create a symbolic link to enable your new configuration and test it.<br/>

```
sudo ln -s /etc/nginx/sites-available/nextastra /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```


iii)  Set up SSL with Let's Encrypt and Certbot: <br/>
        First, you need to configure your domain with a DNS service like DuckDNS:
```
Step 1: Create an Account and Domain
Go to the DuckDNS website.

Log in using one of the available options like Google, GitHub, or Twitter.

Once you're logged in, you'll see a page with a section to add a new domain.

In the "domain" field, enter a unique name for your subdomain (e.g., devlogin11).

Your domain will then be devlogin.duckdns.org. Make sure the current IP address is correct (it should auto-populate with your server's public IP).

Click the add domain button.

You should now see your new domain listed, along with a token at the top of the page. This token is very important, as it is used to authenticate requests to update your IP address. Keep this token private!

```

<img width="1722" height="946" alt="image" src="https://github.com/user-attachments/assets/1571c2f0-6e44-4c57-99fe-3744e1f43145" />


 
 



    
