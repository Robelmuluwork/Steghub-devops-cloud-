# MERN Stack To-Do Application
This repository showcases a full-stack web application developed using the MERN stack, which consists of MongoDB, Express.js, React.js, and Node.js. The application is deployed on an AWS EC2 instance and utilizes MongoDB Atlas for database management. This project exemplifies a complete workflow for building, deploying, and managing a modern web application, including the creation of backend APIs and a frontend client for user interaction.
## Introduction

In this project, we create a simple To-Do list application leveraging the MERN stack. It features a backend built with Node.js and Express.js, a MongoDB database for data storage, and a React.js frontend for user engagement. The application is hosted on an AWS EC2 instance, utilizing MongoDB Atlas as the database solution.

The primary goal of this project is to build a scalable and modern web application with RESTful APIs using a unified JavaScript stack. Additionally, it demonstrates how to effectively host the application using AWS services, highlighting the integration of EC2 and MongoDB Atlas.

## Prerequisites

Before starting, ensure that you have an AWS Account and the following packages

- **Node.js** (version 18.x or higher)
- **npm** (Node Package Manager)
- **MongoDB Atlas Account** for creating a cloud MongoDB database
- **SSH Client** for connecting to the EC2 instance

##Instance Setup
1. In the AWS console, create a new t2.micro (or t3.micro) instance with the Ubuntu Server 24.04 LTS image.
![image1](https://github.com/user-attachments/assets/c7300248-6d32-4daa-9105-b493bbc7483f)


2. Create a new key pair and download it to your device.

![image2](https://github.com/user-attachments/assets/a221eb60-649d-479c-a995-b161e7ee8b09)
![image3](https://github.com/user-attachments/assets/4885b8d9-4137-483c-9129-1a63c1148099)

3. Set up a security group with the appropriate rules (HTTP, HTTPS, and SSH).
Set up the rules as shown in the screenshot above.

![image4](https://github.com/user-attachments/assets/7474a8e1-9511-41f5-9a81-d080572d397b)

4. Launch the instance.

### Accessing the Instance
To access the instance from your terminal:
```bash
ssh -i "Private-key-name.pem" ubuntu@<Public IP address>
```

![image5](https://github.com/user-attachments/assets/5cf6ac1a-3cdc-4f35-885a-0b972ac64e04)




### Backend Setup

1. **Update the server**  
   SSH into your EC2 instance and run the following commands to update the server:

   ```bash
   sudo apt update
   ```
![image](https://github.com/user-attachments/assets/51e82e2d-600a-40f5-8422-b7ac9734366e)

2. **Install Node.js**  
   Install Node.js and npm from the NodeSource repository:

   ```bash
   curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
   sudo apt-get install -y nodejs
   ```
![image](https://github.com/user-attachments/assets/9113aad9-8be3-49f0-94e0-bec9f688a399)
![image](https://github.com/user-attachments/assets/e334425b-ac5d-4271-b957-b51825b15f1d)


3. **Initialize the project**  
   Create a new project directory and initialize it with npm:

   ```bash
   mkdir Todo
   cd Todo
   npm init -y
   ```
   ![image](https://github.com/user-attachments/assets/f1746da3-a1df-4091-a89a-5884b65d07de)


4. **Install Express.js**  
   Express.js will be used to create the server and handle API requests:

   ```bash
   npm install express
   ```
![image](https://github.com/user-attachments/assets/be70da51-b8ad-4f23-a727-cdb0303f82eb)

5. **Create the `index.js` file**  
   This file contains the server code. Create it and add the following:

   ```js
   const express = require("express");
   require("dotenv").config();
   const app = express();
   const port = process.env.PORT || 5000;

   app.use((req, res, next) => {
     res.header("Access-Control-Allow-Origin", "*");
     res.header(
       "Access-Control-Allow-Headers",
       "Origin, X-Requested-With, Content-Type, Accept"
     );
     next();
   });

   app.get("/", (req, res) => {
     res.send("Welcome to Express");
   });

   app.listen(port, () => {
     console.log(`Server running on port ${port}`);
   });
   ```
run 
````js
node index.js
````


You can find it in your AWS web console in EC2 detail 
![image](https://github.com/user-attachments/assets/3376a6a7-195d-4be3-b06c-12a6f9db2ea3)

![image](https://github.com/user-attachments/assets/e7feee7d-4d31-4845-b4ff-2ac8165f9e27)

check if everything is working 

![image](https://github.com/user-attachments/assets/d34efc18-a40f-4755-a8ee-7b18562f12cf)

6. **Install dotenv**  
   To manage environment variables, install the `dotenv` module:
   ```bash
   npm install dotenv
   ```


