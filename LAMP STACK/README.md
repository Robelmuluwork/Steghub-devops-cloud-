
# WEB Stack Implementation (L.A.M.P Stack in AWS)
What is the LAMP Stack?
LAMP is an acronym for a set of open-source software used together to host and run dynamic web applications. The stack comprises four core components:

1. Linux: The operating system that serves as the foundation for the stack. It provides the environment in which the other components run.

2. Apache: The web server software that handles HTTP requests and serves web pages to users. It is known for its reliability, performance, and flexibility.

3. MySQL: The relational database management system (RDBMS) that stores and manages data. MySQL is used to handle database operations and queries for web applications.

4. PHP (or Perl/Python): The server-side scripting language that generates dynamic content. PHP is commonly used, but Perl and Python can also be used depending on the requirements of the application.
Here’s a step-by-step guide on how to set up a LAMP stack (Linux, Apache, MySQL, PHP) on an AWS EC2 t2.micro instance with Ubuntu 24.04 LTS for a DevOps project.




## Prerequisites

1. Install Terminal it can be windows or Linux am a Linux Enthusiast so I'm using Linux(kali)
2. Open an AWS account and provision an Ubuntu server
3. SSH key pair for EC2 instance
## Step 1 - Get your instance up and running

1. Sign up to AWS console following this [instruction](https://console.aws.amazon.com/).
2. After successfully signing up, launch a new instance.
![image2](https://github.com/user-attachments/assets/62bd2f55-69a0-4390-b0e0-8d1529a0d896)
![image3](https://github.com/user-attachments/assets/78269e79-c863-4488-90c7-937e517199f6)
![image4](https://github.com/user-attachments/assets/5015da55-b6a1-4d22-9e28-b246a48cdf2f)



3. Select a region close to you and launch an instance of t2.micro family with Linux ubuntu server.
4. Create a new .pem priavte key(If you don't have one already) and save it securely.

![image6](https://github.com/user-attachments/assets/ebada85f-5d8e-4f57-bf6f-4c7908ac1967)
![image7](https://github.com/user-attachments/assets/33b2b6e9-a01d-4a39-b154-b1bb636c243a)
 5. After downloading there are a few things that must be edited and those are the inbound and outbound setting
![image8](https://github.com/user-attachments/assets/c8bf4ae2-91e0-4811-9eb2-5f2ad3a1ba1e)
![image9](https://github.com/user-attachments/assets/0493ebdd-bc9c-49f5-8ed4-a1f0f13469d3)
![image10](https://github.com/user-attachments/assets/157a24a1-345d-4e48-b4b9-c1c4aeac20aa)
![image11](https://github.com/user-attachments/assets/abfe2b1c-6cac-4842-8117-bde13a3e437b)
7. after that connect the instance with SSH
![image12](https://github.com/user-attachments/assets/b99c6113-964c-4f6d-bf2a-5463b5ce2ca7)
![image13](https://github.com/user-attachments/assets/6ad226b0-b030-494e-a77f-493fe39b6b60)




## Step 2 - Connect to the ec2 instance via ssh
      
1. Open your  terminal  and navigate to your downloads directory

   `cd downloads`
  ![image14](https://github.com/user-attachments/assets/2cf61bf7-6e37-46d3-8e9f-c1713c74c1d2)

3. Connect to the instance by running
   `chmod 400 <your .pem file>`
`ssh -i your-key.pem ubuntu@(your-ec2-public-ip)`

![image15](https://github.com/user-attachments/assets/c93f233b-1f09-4465-b4ca-6594b5f79f13)




 Now, you're connected to your ec2 instance via ssh    



## Step 3 - Install Apache and update the firewall

1. Install Apache using Ubuntu's package manager 'apt'. 
Run the following commands

`sudo apt update`
    I've already updated 

![image16](https://github.com/user-attachments/assets/df2ef293-9702-4aeb-8943-6f227444129d)



`sudo apt install apache2`

    ![image18](https://github.com/user-attachments/assets/369f6b2b-e2da-48f6-bc0a-74601631e2aa)



2. To verify apache is running, run this command

`sudo systemctl status apache2`
         ![image17](https://github.com/user-attachments/assets/8a624b57-3305-43b9-8bb3-d08a1945d927)






3. Now that your server is running, check it via http://(your-ec2-public-ip)

![image16](https://github.com/user-attachments/assets/8dbf0495-8f94-4e54-9f3b-9807138e86d9)


   
Congrats! You've just launched your first web browser in the clouds.
    
## Step 4 - Install MySQL

1. To Install MySQL:
`sudo apt install mysql-server -y`

![image17](https://github.com/user-attachments/assets/71d58fc9-ca89-4b07-82cf-151d30db0e39)




2. Secure MySQL installation:
`sudo mysql` 
![image19](https://github.com/user-attachments/assets/5fd77ee4-d5d2-46f2-8ecb-dc692fc3a73a)

3. It is recommended that you run a script that comes pre-installed with MySQL. This script will remove some insecure default and lockdown access to your database system.
`ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';`

![image20](https://github.com/user-attachments/assets/f362b008-6e08-4f3d-9fb5-bc052ec3b6c5)





4. Exit the MySQL shell:
`mysql> exit`

![image21](https://github.com/user-attachments/assets/eb3d80c3-3baa-4195-a7a0-778d388febe8)




5. Start the interactive script:
`sudo mysql_secure_installation`  

6. You'll be prompted to change your default password to a password of your choice, remove anonymous users, disallow root logins etc. Answer y for yes or anything else to continue without enabling.

7. Login into MySQL to ensure it works:
`sudo mysql -p`
    
    Use your new password If you changed it to access MySQL server

8. Exit the MySQL console:
`mysql> exit`            
## Step 5 - Install PHP

1. Install PHP, PHP extensions for Apache and MySQL
`sudo apt install php libapache2-mod-php php-mysql php-cli `

![image21](https://github.com/user-attachments/assets/fb53f06f-c540-4e74-b906-52ba49ff3474)




2. Confirm your php version
`php -v`



At this point, your L.A.M.P stack is fully installed and operational.

## Step 6 - Create a virtual host for your website using Apache

1. Create a new directory for your website(my website would be named projectlamp, you can use any name for yourself.)
`sudo mkdir /var/www/projectlamp`

2. Assign ownership of the directory with the user environment variable
`sudo chown -R $USER:$USER /var/www/projectlamp`

3. Create a new config file
`sudo nano /etc/apache2/sites-available/projectlamp.conf`
![image22](https://github.com/user-attachments/assets/4e32cdcb-0259-4fd7-a38e-087286ad4c7a)


5. You can use the ls command to show the new file in the sites-available directory
`sudo ls /etc/apache2/sites-available`
6. Enable the new virtual host
`sudo a2ensite projectlamp`
7. Disable Apache default website
`sudo a2dissite 000-default`
8. Confirm your config file has zero syntax error
`sudo apache2ctl configtest`



9. Reload Apache so these changes can take effect
`sudo systemctl reload apache2`    


Congrats! Your new website is now active, but the web root /var/www/projectlamp is still empty. You need to create an html file in that location so we can confirm that it works perfectly.
## Step 7 - Test the website with html scripts
1. Create an index.html file Copy the following:
```
sudo echo 'Hello LAMP from hostname $(TOKEN="curl -X PUT
"http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"" 46
curl -H "X-aws-ec2-metadata-token: STOKEN" -s http://169.254.169.254/latest/meta-data/public-
hostname) 'with public IP' S (TOKEN='curl -X PUT "http://169.254.169.254/latest/api/token" -H "X- aws-ec2-metadata-token-ttl-seconds: 21600" && curl -H "X-aws-ec2-metadata-token: STOKEN" -
http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectlamp/index.html
```
5. To view the html code you just wrote:
`cat index.html`


7. Now you view your new web page via http://(your-ec2-public-ip)    

![image23](https://github.com/user-attachments/assets/5038dc3f-a8af-435a-aa8a-b4711f82c6ec)






## Step 8 - Enable PHP on the website

1. Create a PHP test file to confirm that Apache is able to handle and process requests for PHP files
`nano /var/www/projectlamp/index.php`

2. Add the following php code to enable php on the website
`sudo nano /etc/apache2/mods-enabled/dir.conf`
```
<IfModule mod_dir.c>
    DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm
</IfModule>
```
`sudo systemctl reload apache2`

`rm index.html`

3. Create a new index.php file inside your custom web root folder
`nano /var/www/lampproject/index.php`

4. Add the following text
```
<?php
phpinfo();
?>
```

5. After completing this, refresh your http://(your-public-ip)/info.php


If you're seeing this page in your browser, It means your PHP installation is working as expected. 
![php result](https://github.com/user-attachments/assets/acb7e37d-d861-4857-a1ca-f5aa893981e9)


7. For security reasons, remove index.php
`rm index.php`


CONGRATULATIONS, you just finished your first REAL LIFE PROJECT by deploying a LAMP stack in the cloud.
