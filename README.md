# LinuxAssignment3
# Armandeep Pannu A01339822
# Setting Up a Debian 12 Server on DigitalOcean

This tutorial will guide you through the process of setting up a fresh Debian 12 server on DigitalOcean. We will create a new regular user, configure it for administrative tasks, and secure SSH access. Additionally, we'll install and configure Nginx to serve a sample website.

`sudo`: Executes the command with superuser privileges

## Step 1: Creating a Debian 12 Server on DigitalOcean

  1.1. Log in to your DigitalOcean account.

  1.2. Click on "Create" to create a new Droplet.

  1.3. Choose "Debian 12" as the distribution.

  1.4. Select a plan based on your requirements.

  1.5. Choose a data center region.

  1.6. Add your SSH key or choose a password for authentication.

  1.7. Click on "Create Droplet."

## Step 2: Connecting to the Server via SSH

  2.1. Open your terminal.

  2.2. Use the following command to connect to your server:
   ```bash
   ssh -i [location] root@your_server_ip
   ```
  2.3. You are now logged in as the root user.

## Step 3: Creating a New Regular User
  3.1q Add a new user with the following command 
  ```bash
  useradd -ms /bin/bash [username]
  ```
  3.2 Add the user to the sudo group for administrative tasks:
  ```bash
  usermod -aG sudo your_username
  ```

4.  **Set a password for the new user:**
    
    **Objective**: To securely set a password for the new user.
   - You will be prompted to set a password during the user creation process.
     ```bash
     passwd [username]
     ```
    
      
## Step 4: SSH Setup for New User

1. **Copy `.ssh` directory from root to the new user's home directory:**
    
    **Objective**: To copy SSH configuration from root to the new user.
   - Copy command:
     ```bash
     sudo cp -R /root/.ssh /home/[username]/
     ```
      

2. **Change ownership of the copied `.ssh` directory:**
    
    **Objective**: To ensure the new user owns the copied `.ssh` directory.
   - Change ownership with:
     ```bash
     sudo chown -R [username]:[username] /home/[username]/.ssh
     ```
       
## Step 5: Test SSH Connection
1. **Log Out of the Current Session:**
    
    **Objective**: To exit from the current user session.
   - To log out of your current SSH or user session, simply type:
     ```bash
     exit
     ```


2. **SSH into the server with the new user:**
    
    **Objective**: To test the SSH connection with the new user.
   - Use:
     ```bash
     ssh -i [location] [username]@your_server_ip
     ```  

## Step 6: Edit SSH Configuration to Disable Root Login

1. **Edit the SSH configuration file:**
    
    **Objective**: To modify SSH server settings.
   - Open for editing:
     ```bash
     sudo vim /etc/ssh/sshd_config
     ```


2. **Restart the SSH service:**
    
    **Objective**: To apply the new SSH configuration settings.
   - Restart with:
     ```bash
     sudo systemctl restart sshd
     ```
      
## Step 7: Verifying Root Login Restriction

1. **Log Out of the Current Session:**
    
    **Objective**: To exit from the current user session, preparing to log in as the new user.
   - To log out of your current SSH or user session, simply type:
     ```bash
     exit
     ```
    
2. **Verify Root Login is Disabled:**
    
    **Objective**: To ensure that root login has been successfully restricted for increased security.
   - Attempt to log in as the root user via SSH:
     ```bash
     ssh -i [location] root@your_server_ip
     ```
     - If root login is correctly disabled, this attempt should fail.
      


# Installing and Configuring Nginx Server



**This guide outlines the process of installing and configuring the Nginx web server on a Linux-based system in four main steps.**

## Step 1: System Preparation

1. **Update the System**
     
     **Objective**: To ensure the latest software and dependencies are available before installing Nginx.
   - Command:
     ```bash
     sudo apt update
     ```
      - `apt update`: To check for the update in the system
     

2. **Install Nginx**
    
    **Objective**: To install Nginx as the web server on the system.
   - Command:
     ```bash
     sudo apt install nginx
     ``` 
      
## Step 2: Configure Nginx

1. **Enable and Start Nginx**
    
    **Objective**: To ensure Nginx runs automatically upon system boot and starts running immediately.
   - Commands:
     ```bash
     sudo systemctl enable nginx
     sudo systemctl start nginx
     ```
      
2. **Navigate to Web Root**
    
    **Objective**: To access the default directory where web files are stored.
   - Command:
     ```bash
     cd /var/www
     ```
      - `cd /var/www`: Changes the current directory to Nginx's web root.
      

3. **Create a New Directory**
    
    **Objective**: To create a specific directory for your website's files.
   - Command:
     ```bash
     sudo mkdir [dir]
     ```


## Step 3: Add Web Content

1. **Create an HTML File**
    
    **Objective**: To include unique web content, like a homepage.
   Use the following command to create an HTML file inside the directory that you created in the previous step. This HTML file will show a straightforward "Hello, World" greeting..

   - Command:
     ```bash
     sudo vim index.html
     ```
      - `vim index.html`: Opens the Vim editor to create or edit an `index.html` file.
      

   **HTML Code to Copy:**
   ```html
   <!DOCTYPE html>
   <html lang="en">
   <head>
       <meta charset="UTF-8">
       <meta name="viewport" content="width=device-width, initial-scale=1.0">
       <title>2420</title>
       <style>
           body {
               display: flex;
               align-items: center;
               justify-content: center;
               height: 100vh;
               margin: 0;
           }
           h1 {
               text-align: center;
           }
       </style>
   </head>
   <body>
       <h1>Hello, World</h1>
   </body>
   </html>
     
## Step 4: Server Block Configuration

1. **Creating a Custom Server Block**
    
    **Objective**: To configure a server block for hosting websites.
   - Use this command to create and edit a new server block configuration file. This configuration tells Nginx how to handle incoming requests and serve your website.

   - Command:
     ```bash
     sudo vim /etc/nginx/sites-available/[my-server]
     ```
      - Replace `[my-server]` with your file name.
      - `vim /etc/nginx/sites-available/[my-server]`: Opens the Vim editor to create or edit the server block configuration file.
      
   **Server Block Sample Code:**
   Replace `[your dir]` with the directory name you created in `/var/www`.
   ```nginx
   server {
       listen 80 default_server;
       listen [::]:80 default_server;
       
       root /var/www/[your dir];
       
       index index.html index.htm index.nginx-debian.html;
       
       server_name _;
       
       location / {
           # First attempt to serve request as file, then
           # as directory, then fall back to displaying a 404.
           try_files $uri $uri/ =404;
       }
   }


2. **Enable the Server Block**
    
    **Objective**: To enable the new server block configuration by creating a symbolic link and disable the default.
   - Commands:
     ```bash
     sudo ln -s /etc/nginx/sites-available/[my-server] /etc/nginx/sites-enabled/
     sudo unlink /etc/nginx/sites-enabled/default
     ```
      
3. **Reload or Restart Nginx**
    
    **Objective**: To apply the changes made to the Nginx configuration.
   - Commands:
     ```bash
     sudo systemctl reload nginx
     sudo systemctl restart nginx
     ```
      

4. **Verify Nginx Configuration**
     
     **Objective**: To ensure that there are no errors in your Nginx configuration files.
   - Before restarting the Nginx service, it's a good practice to check if the configuration file syntax is correct.
   - Command:
     ```bash
     sudo nginx -t
     ```

   After you are done with all the steps, you are all set!!!
   Congratulation!! Your Debian 12 server is now configured with a new user, secure SSH access, and an Nginx server serving a sample website.
  
     
