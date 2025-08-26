
# Set up a complete CI/CD workflow and deployment pipeline for a Node.js application with secureaccess and monitoring on jenkin.  <br/>

**Steps**  <br/>

1) Clone and Validate Application:  <br/>
        cloned repository into the aws server  <br/>
    ```
    git clone  https://github.com/SakshiPPatil/jenkins-assignment.git    # not mandatory
    sudo apt install nodejs -y
    sudo apt install npm
    install pm2
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
# Jenkinfile

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

sudo nano /etc/nginx/sites-available/devlogin11     # provide the same domain where it created in duckDNS website

add below content in this file
```
server {
    listen 80;
    server_name devlogin11.duckdns.org;

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
sudo ln -s /etc/nginx/sites-available/devlogin11 /etc/nginx/sites-enabled/
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



iv)

```
# Install Certbot and the NGINX plugin
sudo apt-get install certbot python3-certbot-nginx -y

# Run Certbot to get and install the SSL certificate
sudo certbot --nginx -d devlogin11.duckdns.org

```


v) after successfully requesting a certificate: hit **https://devlogin11.duckdns.org/**  this domain on client
<img width="1031" height="651" alt="Screenshot 2025-08-25 153653" src="https://github.com/user-attachments/assets/a4c60497-182d-49db-b0da-7d0dc0437769" />   <br/>

<img width="1054" height="607" alt="image" src="https://github.com/user-attachments/assets/fb1ecc7f-7b63-4a85-ab77-39cfd99938c1" />   <br/>

notes:
add 443 -https port number on security group.<br/>
becsause when ssl works it creates the domain with https because it provides the security between client and server (encrypted form) <br/>

<img width="1510" height="549" alt="image" src="https://github.com/user-attachments/assets/2adcc83e-9da0-45de-b788-7e56eff1342d" />


add plugins:
```
git ,github
ssh agent
pipeline,pipeline groovy
```




 
 



    
