# DevOps_Tooling_Website_Solution

#### In this project you will implement a solution that consists of following components:
- AWS Webserver: Red Hat Enterprise Linux 8.
- Database Server: Ubuntu 24.04 + MySQL.
- Storage Server: Red Hat Enterprise Linux 8 + NFS Server.
- Programming Language: PHP.
- Code Repository: GitHub.

On the diagram below we can see a common pattern where several stateless Web Servers share a common database and also access the same files using Network File Sytem (NFS) as a shared file storage. Even though the NFS server might be located on a completely separate hardware - for Web Servers it look like a local file system from where they can serve the same files.

![image](https://github.com/user-attachments/assets/ccb21a71-6a39-4b37-bcd4-9f6305e16811)

It is important to know what storage solution is suitable for what use cases, for this - we need to answer following questions: what data will be stored, in what format, how this data will be accessed, by whom, from where, how frequently, etc. Base on this you will be able to choose the right storage system for your solution.


## Step 1 - Prepare NFS Server
1. Spin up a new EC2 instance with RHEL Linux 9 Operating System.
![image](https://github.com/user-attachments/assets/bfa7d172-ddbb-4705-ae41-9b5377a5be59)


![image](https://github.com/user-attachments/assets/942d2ad9-55ad-4984-8cc4-ec2e3e1f868a)



3. Based on LVM experience from Project 6, Configure LVM on the Server.

- Based on your LVM experience from Project 6, Configure LVM on the Server. Create 3 volumes in the same AZ as your Web Server EC2, each of 20 GiB.
![image](https://github.com/user-attachments/assets/4912ef5e-25c9-4bfe-b14d-a7e1f6dd8fc7)


- Updating the Machine.

````
sudo yum update -y
````

List the disk
```
lsblk
```
![image](https://github.com/user-attachments/assets/b7f6c870-4775-4fbf-9d9c-09e9d8fbe28f)


Use df -h command to see all mounts and free space

```
df -h
```
![image](https://github.com/user-attachments/assets/83e719bf-a0cc-447b-bb04-79fd8e1d0a79)


Use gdisk utility to create a single partition on each of the 3 disks:
```
sudo gdisk /dev/xvdbf
```
![image](https://github.com/user-attachments/assets/bbc564cb-68e5-4eec-9efb-3804e45dc161)

we follow the same steps for remaining two and create partision:

Use lsblk utility to view the newly configured partition on each of the 3 disks.
![image](https://github.com/user-attachments/assets/f8a0fe31-3c5e-4add-bd74-05e78bb9801a)



Install lvm2 package: creating logical volumes, which can be resized or moved without needing to unmount file systems.
```
sudo yum install lvm2 -y
```

Check for available partitions.
```
sudo lvmdiskscan 
```
![image](https://github.com/user-attachments/assets/d22cd8ba-aa60-4b8a-b8d5-3e9059bd8ad4)


Create Physical Volumes Use pvcreate utility to mark each of 3 disks as physical volumes (PVs) to be used by LVM
```
sudo pvcreate /dev/xvdbb1 /dev/xvdbc1 /dev/xvdbd1
```
![image](https://github.com/user-attachments/assets/79263819-7710-447a-a578-7995e9189d21)


Verify that your Physical volume has been created successfully
```
sudo pvs
```
![image](https://github.com/user-attachments/assets/c4783c35-a370-4360-b6fa-99cad81d4419)


Use vgcreate utility to add all 3 PVs to a volume group (VG) Name the VG webdata-vg
```
sudo vgcreate webdata-vg /dev/xvdbb1 /dev/xvdbc1 /dev/xvdbd1
```

Verify that your VG has been created successfully
```
sudo vgs
```
![image](https://github.com/user-attachments/assets/750f9dcf-6771-4326-892e-d82741149eb8)


**Create Logical Volumes Use lvcreate utility to create logical volumes
```
sudo lvcreate -L 14G -n lv-apps webdata-vg
sudo lvcreate -L 14G -n lv-logs webdata-vg
sudo lvcreate -L 14G -n lv-opt  webdata-vg
```
![image](https://github.com/user-attachments/assets/c81eef88-95be-43ad-b283-b8c85a439168)

Verify that our Logical Volume has been created successfully
```
sudo lvs
```

Verify the entire setup #view complete setup - VG , PV, and LV
```
sudo vgdisplay -v
```

![image](https://github.com/user-attachments/assets/16295300-6fd2-40ab-9481-76bfd8a5e808)



2. Instead of formatting the disks as ext4 you will have to format them as xfs.
- Ensure there are 3 Logical Volumes lv-opt lv-apps, and lv-logs
```
sudo lsblk
```
![image](https://github.com/user-attachments/assets/b435eec9-f269-41cb-8b42-72f14847beaa)

Format the Logical Volumes as XFS:
```
sudo mkfs.xfs /dev/webdata-vg/lv-apps
sudo mkfs.xfs /dev/webdata-vg/lv-logs
sudo mkfs.xfs /dev/webdata-vg/lv-opt
```
![image](https://github.com/user-attachments/assets/48857069-1f3b-4cda-bb4a-d4c764674007)



3. Create mount points on /mnt directory for the logical volumes as follows: Mount lv-apps on /mnt/apps - To be used by webservers Mount lv-logs on /mnt/logs - To be used by webserver logs Mount lv-opt on /mnt/opt - To be used by Jenkins server in Project 8.

![image](https://github.com/user-attachments/assets/4da42fa4-0d6f-47a9-b6f5-bbc4975f878d)


Mount Logical Volumes
```
sudo mount /dev/webdata-vg/lv-apps /mnt/apps
sudo mount /dev/webdata-vg/lv-logs /mnt/logs
sudo mount /dev/webdata-vg/lv-opt /mnt/opt
```
Add Mount Points to /etc/fstab
```
sudo vi /etc/fstab
```
Add the following lines:

```
/dev/webdata-vg/lv-apps /mnt/apps xfs defaults 0 0
/dev/webdata-vg/lv-logs /mnt/logs xfs defaults 0 0
/dev/webdata-vg/lv-opt /mnt/opt xfs defaults 0 0
```
![image](https://github.com/user-attachments/assets/544d06ba-9cb0-434d-a07d-bd29e2738565)

Verify Mounts:
```
sudo mount -a
```

3. Install NFS server, configure it to start on reboot and make sure it is u and running.
Update the System and Install NFS Utilities:

```
sudo yum -y update
sudo yum install nfs-utils -y
sudo systemctl start nfs-server.service
sudo systemctl enable nfs-server.service
sudo systemctl status nfs-server.service
```

4.Export the NFS Mounts
Use subnet cidr to connect as clients. For simplicity, we will install our all three Web Servers inside the same subnet, but in production set up would probably want to separate each tier inside its own subnet for higher level of security. To check our subnet cidr - open our EC2 details in AWS web console and locate Networking tab and open a Subnet link:

Set Permissions on Mount Points.

```
sudo chown -R nobody:nobody /mnt/apps
sudo chown -R nobody:nobody /mnt/logs
sudo chown -R nobody:nobody /mnt/opt
sudo chmod -R 777 /mnt/apps
sudo chmod -R 777 /mnt/logs
sudo chmod -R 777 /mnt/opt
```

Restart NFS Server

```
sudo systemctl restart nfs-server.service
```

Configure access to NFS for clients within the same subnet (example of Subnet CIDR - 172.31.32.0/20 ):

```
sudo nano /etc/exports
```

Add the following lines:
```
/mnt/apps 172.31.32.0/20(rw,sync,no_all_squash,no_root_squash)
/mnt/logs 172.31.32.0/20(rw,sync,no_all_squash,no_root_squash)
/mnt/opt 172.31.32.0/20(rw,sync,no_all_squash,no_root_squash)
```

![image](https://github.com/user-attachments/assets/220042b2-80dc-470f-9169-ac488014a912)

save and exit from the editor by ctrl+s ctrl+x

Export the NFS Shares:

```
sudo exportfs -arv
```

5. Check which port is used by NFS and open necessary ports in Security Groups (add new Inbound Rule)

Check NFS Ports:

```
rpcinfo -p | grep nfs
```
##### Important note: In order for NFS server to be accessible from our client,we open following ports:
- TCP 111
- UDP 111
- UDP 2049

![image](https://github.com/user-attachments/assets/18f6b8c1-fef2-435c-80c9-b3eb2a599ba0)



## Step 2 - Configure the database server

Log to aws account console and create EC2 instance of t2.micro type with Ubuntu Server launch in the default region ap-south-1. name instance MySQL server.

![image](https://github.com/user-attachments/assets/973d3bd9-6456-46d5-8f5b-4f57cf31aecb)


```
sudo apt update -y
```
1. Install MySQL server:

```
sudo apt install mysql-server
```

2. Check status:

```
sudo systemctl status mysql
```
3. Enable MySQL to start on boot:

```
sudo systemctl enable mysql
```
![image](https://github.com/user-attachments/assets/d01fbdfd-e195-426f-b228-89ba74c8f9ca)


4. Create a database Tooling and User name it webaccess.

Login to Database:

```
sudo mysql
CREATE DATABASE tooling;
CREATE USER 'webaccess'@'%' IDENTIFIED BY 'mypass';
GRANT ALL PRIVILEGES ON tooling. * TO 'webaccess'@'%' WITH GRANT OPTION;
exit
```
![image](https://github.com/user-attachments/assets/6577ce42-48dc-45a0-96d2-f4a9901bd9ab)

## Step 3 - Prepare the Web Servers
In this step we will do the following:
- Configure NFS client (this step must be done on all three servers).
- Deploy a Tooling application to our Web Servers into a shared NFS folder.
- Configure the Web Servers to work with a single MySQL database For server One.

1. Launch a new EC2 instance with REDHAT Operating System.

![image](https://github.com/user-attachments/assets/06fa9e34-4f0c-4c20-886d-ad131d561752)



Updating machine...

```
sudo yum update -y
```

Installing NFS CLIENT:

```
sudo yum install nfs-utils nfs4-acl-tools -y
```

2. Mount /var/www/ and target the NFS server's export for apps

```
sudo mkdir /var/www
sudo mount -t nfs -o rw,nosuid <NFS-Server-Private-IP-Address>:/mnt/apps /var/www
sudo mount -t nfs -o rw,nosuid 172.31.33.253:/mnt/apps /var/www/

```
3. Verify that NFS was mounted successfully
```
 df -h
```
Make sure that the changes will persist on Web Server after reboot:

```
sudo vi /etc/fstab
```
add following line
![image](https://github.com/user-attachments/assets/2418eed6-8d74-4a5c-837c-e5ef773e757f)

4. Install Remi's repository, Apache and PHP

```
sudo yum install httpd -y
sudo systemctl enable httpd.service
sudo systemctl start httpd.service
sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
sudo dnf install dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm
sudo dnf module reset php
sudo dnf module enable php:remi-7.4
sudo dnf install php php-opcache php-gd php-curl php-mysqlnd
sudo systemctl start php-fpm
sudo systemctl enable php-fpm
setsebool -P httpd_execmem 1
```
Repeat steps 1-5 for another 2 Web Servers.

5. Verify NFS Mount and Apache Setup:
     Verify that Apache files and directories are available on the Web Server in /var/www and also on the NFS server in /mnt/apps. If you see the same files - it means NFS is mounted correctly.

```
cd /var/www
```
You can try to create a new file.
```
sudo touch test.txt
```
![image](https://github.com/user-attachments/assets/eceb9a9b-94fc-4e94-bb3f-e5836cde221f)



We can see the text.txt file created inside our nfs server /mnt/apps directory. So they are communicating perfectly.
![image](https://github.com/user-attachments/assets/52ee92d3-e93d-42ca-93d0-66e617bf0f82)


6. Fork the tooling source code from Darey.io Github Account to your Github account. Download git.

```
sudo yum install git -y
git clone https://github.com/ksal1235/tooling.git
```
Clone the repository you forked the project into

```
git clone https://github.com/ksal1235/tooling.git
```
![image](https://github.com/user-attachments/assets/8fcb2a89-047b-4be4-9e47-f0ec0bcb2f8d)


8. Deploy the tooling website's code to the Webserver. Ensure that the html folder from the repository is deployed to /var/www/html

          Note 1: Do not forget to open TCP port 80 on the Web Server.
          Note 2: If you encounter 403 Error - check permissions to your /var/www/html folder and also disable SELinux sudo setenforce 0 To make this change permanent - open following config file

Disable SELinux
```
sudo setenforce 0
sudo vi /etc/sysconfig/selinux
```

and set SELINUX=disabled
![image](https://github.com/user-attachments/assets/5c78675f-2278-4471-bf18-1979298642c1)


Then restrt httpd.

```
sudo systemctl restart httpd
sudo systemctl status httpd
```
9. Update the website's configuration to connect to the database (in /var/www/html/functions.php file). Apply tooling-db.sql script to your database using this command.


10. Create in MySQL a new admin user with username: myuser and password: password:

Creating user and assignning the Required All Permission 
```
CREATE USER 'myuser'@'172.31.32.52' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON *.* TO 'myuser'@'172.31.32.52' WITH GRANT OPTION;
FLUSH PRIVILEGES;

```
![image](https://github.com/user-attachments/assets/9af39059-eff8-4d1e-bcaa-92bd529afdc0)

Use databases;
```
USE tooling
```

Creating the table
````
CREATE TABLE users (
id INT PRIMARY KEY,
username VARCHAR(50),
password VARCHAR(100),
email VARCHAR(100),
user_type VARCHAR(20),
status TINYINT(1)
     );

````

```
INSERT INTO 'users' ('id', 'username', 'password', 'email', 'user_type', 'status') VALUES -> (1, 'myuser', '5f4dcc3b5aa765d61d8327deb882cf99', 'user@mail.com', 'admin', '1');
```
Open the website in your browser http://Web-Server-Public-IP-Addressor Public-DNS-Name/index.php and make sure you can login into the website with your user and password.
```
http://34.203.210.189/login.php
```
![image](https://github.com/user-attachments/assets/c5329c03-f8e4-42c4-aab7-6c3d85f152e0)

Login with Username and Password:
![image](https://github.com/user-attachments/assets/1a029b35-356e-47c6-aed2-229f41f4de06)


## Conclusion:

The DevOps Tooling Website Solution project demonstrates the successful implementation of a robust and scalable web solution using DevOps principles and tools. By leveraging technologies such as Apache, PHP, MySQL, and NFS, the project showcases the ability to deploy a reliable and efficient web application.

#### Throughout the project, several key milestones were achieved:

The successful configuration of an NFS server to provide shared storage for web servers, ensuring data consistency and facilitating seamless collaboration.
The implementation of a load balancer using Apache, which efficiently distributes incoming traffic across multiple web servers, enhancing performance and availability.
The deployment of a MySQL database server to store and manage the application's data, ensuring data integrity and reliability.
The setup of multiple web servers running Apache and PHP, allowing for the hosting and serving of the DevOps Tooling Website.
The integration of version control using Git, enabling effective collaboration, code management, and deployment processes.
The project's architecture, which separates the web servers, database server, and shared storage, demonstrates a modular and scalable approach. This design allows for easy maintenance, troubleshooting, and future expansion as the application grows.

By following DevOps best practices and utilizing industry-standard tools, the project showcases the benefits of automation, collaboration, and continuous integration/continuous deployment (CI/CD) pipelines. The implemented solution not only improves the efficiency of the development and deployment processes but also enhances the overall reliability and performance of the web application.

In conclusion, the DevOps Tooling Website Solution project serves as a testament to the power of DevOps methodologies in delivering high-quality, scalable, and maintainable web solutions. The project's success highlights the importance of leveraging the right tools, adopting best practices, and fostering a culture of collaboration and automation in modern software development and operations.
