# aws-ec2-complete-project-launch-setup

<b>EC2 Instance Setup Commands to launch a website in AWS</b>

<b>Connect to EC2</b>
- chmod 400 ironclad-farhan.pem
- ssh -i sampleKey.pem ubuntu@ec2-32-54-134-1.us-west-2.compute.amazonaws.com

<b>Instance</b>
- sudo apt update (Update Instance)

<b>Install NodeJS (v14)</b>
- curl -sL https://deb.nodesource.com/setup_14.x | sudo bash -
- cat /etc/apt/sources.list.d/nodesource.list
- sudo apt -y install nodejs
- node —version
<b>Install MySQL (Latest)</b>
- sudo apt install mysql-server
- sudo service mysql status
- sudo mysql
- ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password by 'admin_Ironclad_2022'; (If doesn’t work, type it instead copying and pasting)
- exit;
- sudo mysql_secure_installation
- sudo mysql -u root -p & put password admin_Ironclad_2022
- create database ironclad_db_portal

<b>Install PM2</b>
- sudo npm install -g pm2
- sudo pm2 startup systemd

<b>Install Nginx</b>
- sudo apt-get install -y nginx
- sudo ufw allow OpenSSH
- sudo ufw allow 'Nginx Full'
- sudo ufw --force enable

<b>Install Yarn</b>
- curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
- echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
- sudo apt update && sudo apt install --no-install-recommends yarn

<b>Setup project from git</b>
- Commands might change depending on your project setup
- cd ../../
- sudo mkdir app, mkdir portal
- sudo git clone https://gitlab.com/ironclad-investments/ironclad-investments.git
- git config --global --add safe.directory /app/ironclad-investments
- cd ironclad-investments
- sudo git checkout prod-portal
- cd server, sudo npm i (or sudo yarn)
- sudo npm run prisma-wohoo
- sudo npm start, Control-C
- sudo pm2 start dist/index.js
- cd ../client
- sudo npm i (or sudo yarn)

<b>NPM I takes too long (if)</b>
- https://github.com/microsoft/WSL/issues/7254

  <b>Build files location & copy build</b>
- go to root folder & cd home/
- sudo mkdir app-deploy
- sudo mkdir portal
- sudo cp -r app/portal/ironclad-investments/client/build/ home/app-deploy/portal/

<b>NGINX Setup</b>
- cd etc/nginx/sites-available/
- sudo rm -r default ,  cd ../sites-enabled/ , rm -r default (remove default nginx conf)
- cd ../sites-available/
- sudo nano portal & put config :

server {
  listen 80 default_server;
  server_name ip portal.ironcladinvestments.co.uk ww.portal.ironcladinvestments.co.uk;

  root /home/app-deploy/portal/build;
  index index.html indexmhtm;

 location / {
  try_files $uri /index.html;
  }
  location /api {
        proxy_pass https://api.ironcladinvestments.co.uk; (Or http://localhost:4000)
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
  }
}

- save it (ctl x, y, enter)
- sudo nano api-portal & put config :

server {
    server_name ip api.ironcladinvestments.co.uk ww.api.ironcladinvestments.co.uk;
    location / {
        proxy_pass http://localhost:4000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}

- save it (ctl x, y, enter)
- cd ../sites-enabled/
- sudo ln -fs ../sites-available/portal
- sudo ln -fs ../sites-available/api-portal
- sudo nginx -t
- sudo systemctl restart nginx


<b>Add SSL Certification Video URL :</b>  
https://www.youtube.com/watch?v=R5d-hN9UtpU
Renew : all domains : sudo certbot renew | specific : sudo certbot --force-renewal -d ww.api.ironcladinvestments.co.uk

<b>Connect To VPS Database (MYSQL, TablePlus Setup) :</b>
Change ubuntu to root If you are using DigitalOcean
![alt text](https://github.com/Farhan-meb/aws-ec2-complete-project-launch-setup/blob/main/TablePlus%20Setup.png)

