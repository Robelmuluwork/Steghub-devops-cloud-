
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



![image2](https://github.com/user-attachments/assets/90720a76-0f38-49d9-9ed4-0ac62a9a8239)
![image3](https://github.com/user-attachments/assets/e1c6311b-cb7b-4413-bd40-686fb9b488ae)
![image4](https://github.com/user-attachments/assets/521eb4d7-8791-4e82-8dc9-c8edd5812e97)


3. Select a region close to you and launch an instance of t2.micro family with Linux ubuntu server.
4. Create a new .pem priavte key(If you don't have one already) and save it securely.
   ![image6](https://github.com/user-attachments/assets/6fa113c8-faef-4ee4-90df-3221c43b6bdd)
![image7](https://github.com/user-attachments/assets/ec3564cb-f791-4774-a583-fb0e793ebaf5)

  5. After downloading there are a few things that must be edited and those are the inbound and outbound setting
![image8](https://github.com/user-attachments/assets/b351f682-3fbe-4f31-bc0c-cfa8cd3ab04f)
![image9](https://github.com/user-attachments/assets/f7a67e5d-bd6b-4041-a0b0-695f93c8afad)
![image10](https://github.com/user-attachments/assets/0552b171-7ff1-4237-a9ee-a38fb7ef4fdf)
![image11](https://github.com/user-attachments/assets/266bac1a-9d61-4e93-af79-228167663e0d)
7. after that connect the instance with SSH
![image12](https://github.com/user-attachments/assets/0553e349-bb09-4ff7-8d96-e94d2061bf9e)
![image13](https://github.com/user-attachments/assets/a5002483-a227-4063-b17a-5ac61aa5675d)



## Step 2 - Connect to the ec2 instance via ssh
      
1. Open your  terminal  and navigate to your downloads directory

   `cd downloads`
   ![image14](https://github.com/user-attachments/assets/66591a14-1def-4bd7-a4f3-cd7db1e6aa73)
3. Connect to the instance by running
   `chmod 400 <your .pem file>`
`ssh -i your-key.pem ubuntu@(your-ec2-public-ip)`

 ![image15](https://github.com/user-attachments/assets/066ab5b7-d0c6-4f39-a628-f5815905a439)



 Now, you're connected to your ec2 instance via ssh    



## Step 3 - Install Apache and update the firewall

1. Install Apache using Ubuntu's package manager 'apt'. 
Run the following commands

`sudo apt update`
    I've already updated 

![image16](https://github.com/user-attachments/assets/e7dbec0b-4b68-4448-942b-bda1632e82c1)


`sudo apt install apache2`
  ![image18](https://github.com/user-attachments/assets/2d75e4dc-921f-4633-a84b-d498d9d0a228)
    


2. To verify apache is running, run this command

`sudo systemctl status apache2`
         
![image17](https://github.com/user-attachments/assets/4bc82376-0f26-4d05-b687-099932292c2a)




3. Now that your server is running, check it via http://(your-ec2-public-ip)

![image16](https://github.com/user-attachments/assets/3ad9c3ea-5aab-40ff-a27d-ecb8c328b0f9)

   
Congrats! You've just launched your first web browser in the clouds.
    
## Step 4 - Install MySQL

1. To Install MySQL:
`sudo apt install mysql-server -y`
![image17](https://github.com/user-attachments/assets/8f65ee20-ca76-451d-9418-d0176e9d1887)




2. Secure MySQL installation:
`sudo mysql` 
![image19](https://github.com/user-attachments/assets/d438130b-ec94-4fc7-8feb-05209c7c4395)
3. It is recommended that you run a script that comes pre-installed with MySQL. This script will remove some insecure default and lockdown access to your database system.
`ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';`


![image20](https://github.com/user-attachments/assets/0c1a144a-b03d-434a-82fa-efb1fb600f59)



4. Exit the MySQL shell:
`mysql> exit`

![image21](https://github.com/user-attachments/assets/7c9b1a68-e91b-49ce-ae0c-5295d345fc35)



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

 ![image21](https://github.com/user-attachments/assets/c8161441-2e07-4199-b370-cd39687cbb25)



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
![image22](https://github.com/user-attachments/assets/ad6f069a-2e9b-4395-8188-a312d0cae7b8)

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

![image23](https://github.com/user-attachments/assets/f4ae5b38-27e5-4ff7-b139-04d1a5e8c8b7)





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
![php result](https://github.com/user-attachments/assets/0cb84c4f-fff2-43a0-8d9b-b56dfd074af1)

7. For security reasons, remove index.php
`rm index.php`


CONGRATULATIONS, you just finished your first REAL LIFE PROJECT by deploying a LAMP stack in the cloud.
