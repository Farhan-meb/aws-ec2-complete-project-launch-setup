**Step-by-Step Guide: Launching a Complete Website on AWS EC2**

In the digital era, a strong online presence is paramount for success. AWS EC2 provides an exceptional platform for hosting websites, offering the scalability and flexibility required to meet diverse demands. In this guide, we'll take you through the comprehensive process of setting up an entire project on AWS EC2, from connecting to the instance to configuring the web server and more.

**Connecting to Your EC2 Instance**

To begin your journey towards launching a website on AWS EC2, follow these steps:

1. Open your terminal and navigate to the directory containing your private key (PEM file).
2. Use the following command to adjust the permissions of your private key:
   ```
   chmod 400 ironclad-farhan.pem
   ```
3. Initiate an SSH connection to your EC2 instance using your private key:
   ```
   ssh -i ironclad-farhan.pem ubuntu@ec2-32-54-134-1.us-west-2.compute.amazonaws.com
   ```

**Setting Up Your EC2 Instance**

After connecting to your EC2 instance, proceed with the setup:

1. Update the instance for the latest packages:
   ```
   sudo apt update
   ```

2. Install Node.js version 14:
   ```
   curl -sL https://deb.nodesource.com/setup_14.x | sudo bash -
   cat /etc/apt/sources.list.d/nodesource.list
   sudo apt -y install nodejs
   ```

3. Install the latest MySQL:
   ```
   sudo apt install mysql-server
   sudo service mysql status
   sudo mysql
   ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password by 'admin_Ironclad_2022';
   exit;
   sudo mysql_secure_installation
   sudo mysql -u root -p
   create database ironclad_db_portal;
   ```

4. Set up PM2 (Process Manager for Node.js):
   ```
   sudo npm install -g pm2
   sudo pm2 startup systemd
   ```

5. Install Nginx (Web Server):
   ```
   sudo apt-get install -y nginx
   sudo ufw allow OpenSSH
   sudo ufw allow 'Nginx Full'
   sudo ufw --force enable
   ```

6. Install Yarn (Dependency Manager for Node.js):
   ```
   curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
   echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
   sudo apt update && sudo apt install --no-install-recommends yarn
   ```

**Setting Up Your Project**

Next, set up your project on the EC2 instance:

1. Navigate to your desired directory and create subdirectories for your project:
   ```
   cd ../../
   sudo mkdir app portal
   ```

2. Clone your project from GitLab:
   ```
   sudo git clone https://gitlab.com/ironclad-investments/ironclad-investments.git
   git config --global --add safe.directory /app/ironclad-investments
   cd ironclad-investments
   sudo git checkout prod-portal
   ```

3. Install dependencies and run your server and client applications:
   ```
   cd server
   sudo npm install
   sudo npm run prisma-wohoo
   sudo npm start
   sudo pm2 start dist/index.js
   cd ../client
   sudo npm install
   ```

**Deploying and Configuring Nginx**

Now, let's deploy your app and configure Nginx:

1. Create deployment directories and copy build files:
   ```
   cd ~
   sudo mkdir app-deploy
   sudo mkdir portal
   sudo cp -r app/portal/ironclad-investments/client/build/ ~/app-deploy/portal/
   ```

2. Configure Nginx for your project:

   - Navigate to Nginx's sites configuration directory:
     ```
     cd /etc/nginx/sites-available/
     ```

   - Remove the default configuration files:
     ```
     sudo rm -r default
     ```

   - Navigate to the `sites-enabled` directory and remove the default configuration symlink:
     ```
     cd ../sites-enabled/
     sudo rm -r default
     ```

   - Create and configure the Nginx server block for your portal:
     ```
     cd ../sites-available/
     sudo nano portal
     ```
     Add your configuration, save, and exit.

   - Create and configure the Nginx server block for your API:
     ```
     sudo nano api-portal
     ```
     Add your configuration, save, and exit.

3. Create symbolic links to enable the configurations:
   ```
   sudo ln -fs ../sites-available/portal
   sudo ln -fs ../sites-available/api-portal
   ```

4. Test the Nginx configurations:
   ```
   sudo nginx -t
   ```

5. Restart Nginx to apply the changes:
   ```
   sudo systemctl restart nginx
   ```

**Adding SSL Certification**

For added security, you can integrate SSL certification into your setup. Here's a video tutorial for the process: [SSL Certification Video Tutorial](https://www.youtube.com/watch?v=R5d-hN9UtpU). Additionally, you can renew certificates with the following commands:
   - For all domains:
     ```
     sudo certbot renew
     ```
   - For specific domains:
     ```
     sudo certbot --force-renewal -d ww.api.ironcladinvestments.co.uk
     ```

**Connecting to VPS Database (MYSQL, TablePlus Setup)**

If you are utilizing DigitalOcean, you might need to change the user from "ubuntu" to "root" to connect to your VPS database.

Launching a complete website on AWS EC2 involves several steps, from initial connection to instance setup, project deployment, Nginx configuration, and even SSL integration. By following this guide, you've embarked on a journey to successfully launch your website and make the most of the powerful capabilities offered by AWS.
![alt text](https://github.com/Farhan-meb/aws-ec2-complete-project-launch-setup/blob/main/TablePlus%20Setup.png)

