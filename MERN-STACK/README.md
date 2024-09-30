# MERN Stack To-Do Application

This repository contains a full-stack web application built using the MERN stack (MongoDB, Express.js, React.js, Node.js). The application is hosted on an EC2 instance in AWS and uses MongoDB Atlas as the database. The project demonstrates a complete workflow for developing, deploying, and managing a modern web application, including the creation of backend APIs, and the frontend client to interact with them.

## Table of Contents

- [MERN Stack To-Do Application](#mern-stack-to-do-application)
	- [Table of Contents](#table-of-contents)
	- [Introduction](#introduction)
	- [Prerequisites](#prerequisites)
	- [Setup and Installation](#setup-and-installation)
		- [Backend Setup](#backend-setup)
		- [Frontend Setup](#frontend-setup)
	- [Testing the Backend](#testing-the-backend)
		- [Sample Endpoints](#sample-endpoints)
	- [Running the Application](#running-the-application)
	- [Application Features](#application-features)
	- [AWS Configuration](#aws-configuration)
		- [Launching an EC2 Instance](#launching-an-ec2-instance)
		- [Configuring Security Groups](#configuring-security-groups)
		- [SSH Access](#ssh-access)
	- [Deployment](#deployment)
			- [**1. Setting Up Node.js and MongoDB**](#1-setting-up-nodejs-and-mongodb)
			- [**2. Backend Setup: Express Server and MongoDB**](#2-backend-setup-express-server-and-mongodb)
			- [**3. Frontend Setup: React App**](#3-frontend-setup-react-app)
			- [**4. API Requests and Integration**](#4-api-requests-and-integration)
			- [**5. Deployment on AWS EC2**](#5-deployment-on-aws-ec2)

## Introduction

This project involves the creation of a simple To-Do list application using the MERN stack. It includes a backend built with Node.js and Express.js, a MongoDB database for data storage, and a React.js frontend for user interaction. The application is deployed on an AWS EC2 instance and uses MongoDB Atlas for the database.

The main objective is to create a scalable, modern web application with RESTful APIs using a unified JavaScript stack. This project also demonstrates how to host the application using AWS services like EC2 and MongoDB Atlas.

## Prerequisites

Before starting, ensure that you have the following software installed:

- **Node.js** (version 18.x or higher)
- **npm** (Node Package Manager)
- **AWS Account** for creating and managing EC2 instances
- **MongoDB Atlas Account** for creating a cloud MongoDB database
- **SSH Client** for connecting to the EC2 instance

## Setup and Installation

### Backend Setup

1. **Update the server**  
   SSH into your EC2 instance and run the following commands to update the server:

   ```bash
   sudo apt update
   ```

2. **Install Node.js**  
   Install Node.js and npm from the NodeSource repository:

   ```bash
   curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
   sudo apt-get install -y nodejs
   ```

3. **Initialize the project**  
   Create a new project directory and initialize it with npm:

   ```bash
   mkdir Todo
   cd Todo
   npm init -y
   ```

4. **Install Express.js**  
   Express.js will be used to create the server and handle API requests:

   ```bash
   npm install express
   ```

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

6. **Install dotenv**  
   To manage environment variables, install the `dotenv` module:
   ```bash
   npm install dotenv
   ```

### Frontend Setup

1. **Create the React app**  
   Inside the `Todo` directory, run:

   ```bash
   npx create-react-app client
   ```

2. **Install dependencies**  
   Install `concurrently` and `nodemon` to run both the frontend and backend simultaneously:

   ```bash
   npm install concurrently --save-dev
   npm install nodemon --save-dev
   ```

3. **Modify `package.json` scripts**  
   Add the following scripts to your `package.json` to enable concurrent development:

   ```json
   "scripts": {
     "start": "node index.js",
     "start-watch": "nodemon index.js",
     "dev": "concurrently \"npm run start-watch\" \"cd client && npm start\""
   }
   ```

4. **Create React Components**  
   Inside the `client/src/components` directory, create `Input.js`, `ListTodo.js`, and `Todo.js` files.

   - **Input.js** handles adding new To-Do items.
   - **ListTodo.js** renders the list of tasks.
   - **Todo.js** combines the other two components and handles state management for the To-Do list.

## Testing the Backend

Before connecting the frontend, you can test the backend APIs using Postman or a similar tool. Create a POST request to add a new To-Do item, a GET request to fetch all items, and a DELETE request to remove an item.

### Sample Endpoints

- **GET** `/api/todos`
- **POST** `/api/todos`
- **DELETE** `/api/todos/:id`

## Running the Application

To run the full MERN stack application (backend + frontend), use the following command from the `Todo` directory:

```bash
npm run dev
```

This will launch both the backend server and the React frontend, allowing you to interact with the application from a browser.

## Application Features

1. **Create a Task**  
   Users can create a new To-Do task by entering the task description and clicking the "Add Todo" button.

2. **View Tasks**  
   All active tasks are displayed in a list on the homepage. Tasks can be clicked to mark them as complete.

3. **Delete a Task**  
   Users can delete a completed task from the list by clicking on it.

## AWS Configuration

### Launching an EC2 Instance

1. Go to the AWS Management Console and launch a new EC2 instance in the `us-east-1` region.
2. Select **Ubuntu** as the operating system and choose an appropriate instance type (e.g., t2.micro for free tier).
3. Configure the security group to allow inbound traffic on ports **22** (SSH), **80** (HTTP), and **443** (HTTPS).

### Configuring Security Groups

Set up inbound rules to control access:

- **SSH (port 22):** Restrict access to your trusted IP addresses for security.
- **HTTP (port 80):** Allow web traffic from anywhere.
- **HTTPS (port 443):** Allow secure web traffic.

### SSH Access

Once your instance is running, you can connect to it via SSH:

```bash
ssh -i "your-key.pem" ubuntu@<EC2-instance-public-IP>
```

## Deployment

1. **MongoDB Atlas Setup**  
   Create a free MongoDB cluster on MongoDB Atlas and obtain the connection URI. Add this URI to your `.env` file.

2. **Environment Variables**  
   Create a `.env` file in your root directory and add your MongoDB URI:

   ```env
   DB='mongodb+srv://<username>:<password>@cluster0.mongodb.net/test?retryWrites=true&w=majority'
   ```

3. **Run the Application**  
   Start the server and frontend:

   ```bash
   npm run dev
   ```

4. **Access the Application**  
   Open your browser and navigate to the public IP of your EC2 instance, followed by port 5000:
   ```url
   http://<EC2-public-IP>:5000
   ```

---

#### **1. Setting Up Node.js and MongoDB**

- **Updating and Upgrading Packages**  
  ![update and upgrade](https://github.com/user-attachments/assets/fb9d1fd2-a84f-4cef-9273-387fd2461de0)  
  The first step is to ensure all packages are up-to-date. Run `sudo apt-get update && sudo apt-get upgrade`.

- **Verifying Node.js and NPM Versions**  
  ![node_and_npm_version](https://github.com/user-attachments/assets/ecc72d98-0696-4627-9ef4-d3e86c3989ad)  
  Confirm that Node.js and npm are correctly installed by checking their versions. The versions used here are `v14.17.0` for Node and `6.14.13` for npm.

- **Installing Nodemon**  
  ![install_nodemon](https://github.com/user-attachments/assets/05b1ca39-5b97-45a2-90a3-29cd3050274a)  
  Nodemon is installed to automatically restart the server when file changes are detected.

---

#### **2. Backend Setup: Express Server and MongoDB**

- **Creating the Backend (Express App)**  
  ![node_indexJs_to_run_server_on_terminal](https://github.com/user-attachments/assets/7d89dabe-f56b-4251-b215-152f12e961f3)  
  The backend server is created using Express.js, which listens on a specified port to handle API requests.

- **Connecting to MongoDB Atlas**  
  ![connect_cluster](https://github.com/user-attachments/assets/0a9671b1-c2df-4588-bf0b-0569b14b0188)  
  MongoDB Atlas is used to store the application’s data. After setting up the cluster, the connection string is added to the environment variables.

- **Database and Collection Setup**  
  ![create_mongoDB_database_and_collection](https://github.com/user-attachments/assets/29ce40b0-7dfd-42ed-be23-46ad718475f8)  
  A new database (`todo`) and a collection (`tasks`) are created in MongoDB Atlas for managing Todo tasks.

---

#### **3. Frontend Setup: React App**

- **Creating the React Application**  
  ![create_reactApp_scaffolding](https://github.com/user-attachments/assets/b34238dc-5f42-4ca9-b88e-1949a7222f12)  
  The frontend of the Todo app is scaffolded using `create-react-app`.

- **Frontend Components**

  - **Input Component**  
    ![edit_inputJS](https://github.com/user-attachments/assets/1a21d7c4-23f3-4eab-8703-a482608ed1e0)  
    This component handles user input for creating new Todo items.
  - **List Component**  
    ![edit_listTodo](https://github.com/user-attachments/assets/cdd7968e-8eb9-4ea5-bad2-7fd2c7ce7742)  
    The list component fetches and displays tasks stored in MongoDB.

  - **Styling**  
    ![edit_indexCSS](https://github.com/user-attachments/assets/f144702e-c89c-4431-a89c-2b110aee6911)  
    Basic CSS styling is applied to the components to enhance the user interface.

---

#### **4. API Requests and Integration**

- **Testing with Postman**  
  ![postman](https://github.com/user-attachments/assets/b61210b6-2556-4f33-ba7f-1c7155221a8f)  
  Before connecting the React frontend, API endpoints for adding, retrieving, and deleting tasks are tested using Postman.

- **Handling API Requests in React**  
  ![get_request_method](https://github.com/user-attachments/assets/afa20841-52b1-4763-b0ad-b0de59965a89)  
  Axios is used in React to make GET, POST, and DELETE requests to the Express backend.

---

#### **5. Deployment on AWS EC2**

- **Setting Up the EC2 Instance**  
  ![node_from_ubuntu](https://github.com/user-attachments/assets/9013c1c0-0891-4a79-913f-64cd8b6e530b)  
  An Ubuntu EC2 instance is provisioned to host the MERN application.

- **Security Group Configuration**  
  ![EC2 security group for port 5000 for MERN todo](https://github.com/user-attachments/assets/4d37deba-1861-467d-90b5-0809db19cd24)  
  Inbound rules are set up in the security group to allow traffic on ports 3000 (React frontend) and 5000 (Express backend).

- **Final Deployment**  
  ![deploy_Todo_MERN_Final](https://github.com/user-attachments/assets/dd74bb03-a98d-4cc6-9efd-f0f9c6bba890)  
  Once everything is set up, the MERN application is deployed, and users can interact with the Todo app through the web.
