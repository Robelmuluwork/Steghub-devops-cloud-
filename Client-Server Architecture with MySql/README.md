                                                    ![What_is_Client-Server_Architecture1](https://github.com/user-attachments/assets/54b861df-a84d-478f-b11a-854d0172c6f4)

# Client-Server Architecture with MySql
## Introduction

In this repository, we explore the Client-Server Architecture using MySQL as the database management system. The client-server model is one of the most widely used architectures for web applications, where clients and servers work together to exchange data and manage resources. MySQL, a powerful relational database, plays a key role in storing, retrieving, and managing data efficiently in this architecture.

This project demonstrates how to set up a simple client-server architecture with MySQL, including the processes of connecting the client-side application to the server-side database, performing CRUD (Create, Read, Update, Delete) operations, and ensuring smooth data flow between the client and server.

## What is Client-Server Architecture with MySQL?
Client-server architecture is a distributed application structure that divides tasks between two entities: the client and the server. The client requests services, such as data retrieval or updates, while the server processes these requests and responds accordingly. In this architecture:

The client refers to the front-end application (such as a web app or mobile app) that interacts with the user.
The server hosts the database and manages requests made by the client, such as querying or modifying the data.
With MySQL in the client-server architecture, the server is typically responsible for:

Storing and managing data: MySQL is used to manage and structure the data in relational tables.
Processing client queries: MySQL processes queries from the client application and returns the requested data or performs the required operations (such as inserts, updates, or deletions).
Ensuring data integrity and security: MySQL manages concurrent access and enforces data integrity rules, ensuring consistent data while preventing unauthorized access.
In this project, we demonstrate how to set up the client-server communication with MySQL, establish connections, and execute database operations from the client side.



To demonstrate a basic client-server using MySQL RDBMS, follow the instructions below:

## Step 1: Create and Configure Two Linux-Based Virtual Servers (EC2 Instances in AWS)

- **Server A name**: `mysql server`
- **Server B name**: `mysql client`

## Step 2: Install MySQL Server on `mysql server`

### Connect to `mysql server`:

```sh
ssh -i <your-key.pem> ubuntu@<mysql-server-public-ip>
```

### Install MySQL Server:

```sh
sudo apt update
sudo apt install mysql-server
```

### Start MySQL Service:

```sh
sudo systemctl start mysql
sudo systemctl enable mysql
sudo systemctl status mysql
```
![image](https://github.com/user-attachments/assets/49b86800-dae2-4ad3-b46b-a9caa1445c42)

### Configure MySQL to Allow Remote Connections:

```sh
sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf
```

Replace `127.0.0.1` with `0.0.0.0` in the `bind-address` line:

```ini
bind-address = 0.0.0.0
```
![image](https://github.com/user-attachments/assets/94bfdd84-fb7a-4638-8edb-61de357dc44e)


### Restart MySQL Service:

```sh
sudo systemctl restart mysql
```

### Create a MySQL User for Remote Access:

```sh
sudo mysql -u root
```
```sql
CREATE USER 'remote_user'@'%' IDENTIFIED BY 'password';
```
```sql
GRANT ALL PRIVILEGES ON *.* TO 'remote_user'@'%';
```
```sql
FLUSH PRIVILEGES;
```
```sql
EXIT;
```
![image](https://github.com/user-attachments/assets/295aea04-9d5e-46c6-a502-183737d317e1)

## Step 3: Install MySQL Client on `mysql client`

### Connect to `mysql client`:

```sh
ssh -i <your-key.pem> ubuntu@<mysql-client-public-ip>
```

### Install MySQL Client:

```sh
sudo apt update
sudo apt install mysql-client
```

## Step 4: Configure Security Groups

### Open TCP Port 3306 on `mysql server`:

1. Go to the AWS Management Console.
2. Navigate to EC2 Dashboard > Security Groups.
3. Select the Security Group associated with `mysql server`.
4. Edit Inbound rules to add a new rule:
   - Type: Custom TCP Rule
   - Port Range: 3306
   - Source: `mysql-client-private-ip` (e.g., `192.168.1.10`)

![image](https://github.com/user-attachments/assets/346d421d-90f3-4adc-9b64-d61d4d938de2)


### Ensure `mysql client` Can Communicate with `mysql server`:

By default, both EC2 instances are in the same local virtual network.

## Step 5: Connect Remotely to MySQL Server from `mysql client`

### Use MySQL Utility to Connect:

```sh
mysql -h <mysql-server-private-ip> -u remote_user -p
```

### Verify Connection:

Run the following SQL query:

```sql
SHOW DATABASES;
```

If you see an output similar to the image below, you have successfully completed this project.

![image](https://github.com/user-attachments/assets/d9f65899-17ad-4dc0-b2fd-8b78664163a3)



## Congratulations!✨🎉🎉🎊🎉🎉

You have deployed a fully functional MySQL Client-Server setup. Well done! 

