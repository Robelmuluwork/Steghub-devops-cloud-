# DevOpsTraining
**DevOps/Cloud Training Material**

# TASK - Implement a Client Server Architecture using MySQL Database Management System (DBMS)

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
```
![image](https://github.com/stiven-skyward/DevOpsTraining/assets/135337796/777b70dc-d170-4010-861f-5ea38cd77d38)

### Configure MySQL to Allow Remote Connections:

```sh
sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf
```

Replace `127.0.0.1` with `0.0.0.0` in the `bind-address` line:

```ini
bind-address = 0.0.0.0
```
![image](https://github.com/stiven-skyward/DevOpsTraining/assets/135337796/b3b6a131-60e9-4986-b1ac-787922ed9c20)

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

![image](https://github.com/stiven-skyward/DevOpsTraining/assets/135337796/6055822c-53f8-4cba-9c70-5343a2f2554b)

## Congratulations!

You have deployed a fully functional MySQL Client-Server setup. Well done! You can further explore this setup by practicing creating and dropping databases & tables, and inserting and selecting records.

## Further Exploration

- **Create a Database**:

    ```sql
    CREATE DATABASE mydatabase;
    ```

- **Create a Table**:

    ```sql
    USE mydatabase;
    CREATE TABLE mytable (
        id INT AUTO_INCREMENT PRIMARY KEY,
        name VARCHAR(255) NOT NULL,
        age INT
    );
    ```

- **Insert Data**:

    ```sql
    INSERT INTO mytable (name, age) VALUES ('John Doe', 30);
    ```

- **Select Data**:

    ```sql
    SELECT * FROM mytable;
    ```

    ![image](https://github.com/stiven-skyward/DevOpsTraining/assets/135337796/4e81bba7-1dab-4e3e-973f-cef0aaaea3f9)
  


