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

## Instance Setup
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
   To manage environment variables, install the `dotenv` module(we are going to use it later):
   ```bash
   npm install dotenv
   ```
   
**Routing**
There are 3 actions that oir TO_DO application needs
    1.Create a new task
    2.Display list of all tasks
    3.Delete a completed task
Each task will be associated with HTTP Requests
for each task we need to create a route that will define endpoints so lets create a folder roudes
````bash
mkdir routes
````
change directory
````bash
cd routes
touch api.js
nano api.js
````
![image15](https://github.com/user-attachments/assets/2d7a36ed-c641-4581-a87e-a1420a0aa4b9)

in the nano editor write this code👇 

![image16](https://github.com/user-attachments/assets/7880f90e-6872-4f80-88a8-98bbdbab13eb)

moving forward let create Models directory
  Now comes the interesting part, since the app is going to make use of Mongodb which is a NoSQL database, we need to create a model.
A model is at the heart of JavaScript based applications, and it is what makes it interactive.
We will also use models to define the database schema. This is important so that we will be able to define the fields stored in each Mongodb document. (Seems like a lot of information, but not to worry, everything will become clear to you over time. I promise!!!)
In essence, the Schema is a blueprint of how the database will be constructed, including other data fields that may not be required to be stored in the database. These are known as virtual properties
To create a Schema and a model, install mongoose which is a Node.js package that makes working with mongodb easier.
Change directory back Todo folder with cd and install Mongoose
```` js
npm install mongoose
````
create a new folder
````bash
mkdir models && touch todo.js
you can run multiple script in one line
````
````bash
nano todo.js
````
![image17](https://github.com/user-attachments/assets/8f4cd88f-9c01-4647-beb5-36089552c9cb)

past this code
````js
const mongoose = require('mongoose');
const Schema mongoose.Schema;
//create schema for todo
const TodoSchema= new Schema ((
action: {
type: String,
required: [true, 'The todo text field is required']
1)
//create model for todo
const Todo mongoose.model('todo', TodoSchema);
module.exports = Todo;
````
![image18](https://github.com/user-attachments/assets/1659b78e-56e3-4887-92a9-603205670be4)


now we need to update our routes

````js
const express = require ('express');
const router express.Router();
const Todo = require('../models/todo');
router.get('/todos', (req, res, next) => {
//this will return all the data, exposing only the id and action field to the client
Todo.find({}, 'action')
.then(data => res.json(data))
.catch(next)
});
router.post('/todos', (req, res, next) > {
if(req.body.action) {
Todo.create(req.body)
.then (data => res.json(data))
.catch(next)
Jelse {
res.json({
error: "The input field is empty"
1)
});
router.delete('/todos/:id', (req, res, next) => {
Todo.findOneAndDelete({"id": req.params.id))
.then (data => res.json(data))
.catch(next)
})
module.exports = router
````
next our application will be the MongoDB Database
#MongoDB Database
We need a database where we will store our data. For this we will make use of mLab. mLab provides MongoDB database as a service solution (DBaaS), so to make life easy, you will need to sign up for a shared clusters free account, which is ideal for our use case. Sign up [here](https://www.mongodb.com/products/try-free/platform/atlas-signup-from-mlab). Follow the sign up process, select AWS as the cloud provider, and choose a region near you.

![image](https://github.com/user-attachments/assets/82e5178d-ab26-4f1a-a6e5-a4edbd5124c4)

![image](https://github.com/user-attachments/assets/0a323765-6724-4b75-9c39-10cdcdb554ca)

cocpy past your database connection string
![image](https://github.com/user-attachments/assets/82f9dc36-61ce-4d99-b81c-107974f21ec8)
don't forget to add your IP or access anywhere
![image](https://github.com/user-attachments/assets/e232a140-451b-45f7-b0d2-bac8d4113c25)
create a .env file in the root folder
````bash
mkdir .env
past your Database connection string DB=`your DB connection`
````
after this update the index.js
````bash     
nano index.js
````
````js
const express = require('express');
const bodyParser = require('body-parser'); const mongoose = require('mongoose');
const routes = require('./routes/api');
const path = require('path'); require('dotenv').config();
const app = express();
const port process.env.PORT || 5000;
//connect to the database
mongoose.connect (process.env.DB, { useNewUrlParser: true, useUnifiedTopology: true }) .then(() => console.log("Database connected successfully'))
.catch(err => console.log(err));
//since mongoose promise is depreciated, we overide it with node's promise
mongoose. Promise = global. Promise;
app.use((req, res, next) => [
res.header("Access-Control-Allow-Origin", "\*");
res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
next();
});
app.use (bodyParser.json());
app.use('/api', routes);
app.use ((err, req, res, next) {
console.log(err);
next();
});
app.listen(port, () => {
console.log("Server running on port ${port)) });
````
start your server by running node index.js
DB will connect succssfully 
![image](https://github.com/user-attachments/assets/1b5fd4b7-7531-46ce-a29d-aa285f6be529)

#Testing Backend code With Postman
![image](https://github.com/user-attachments/assets/8c454731-2330-4749-b16a-16180b63a46e)

##Frontend Creation
‼‼‼ t2.micro is to small for running react JS make sure you update into t3.micro or more since it needs 2GB RAM
````js
npx create-react-app client
````
this will create a react app with folder name client in the parent folder
![image](https://github.com/user-attachments/assets/7c7b0039-b967-4c07-a296-57de07fe08da)

there are some dependacies that must be installed first
````js
npm install concurrently --save-dev
````
this package will help us to run both the frontend and backend concurrently
````js 
npm install nodeman --save-dev
````
this package will help us to run the node when ever we save changes

in the ToDo folder open package.json file and add the following after test script
````js
"scripts": [
"start": "node index.js",
"start-watch": "nodemon index.js",
"dev": "concurrently \"npm run start-watch\" \"cd client && npm start\""
},
````
configure proxy in package.json

````bash
cd client && nano package.json
````
add the key value pair in the package.json
````bash
nano package.json
 "proxy": "http://localhost:5000"
````
run 
````js
npm run dev
````

‼‼❗❗❗❗❗ make sure you open TCP port 3000 in your EC2


#creating your react components
````bash
cd client && cd src && mkdir components && touch Input.js ListTodo.js Todo.js
````
![image22](https://github.com/user-attachments/assets/3595c361-9f3b-4a3c-87f2-6bbc6c4643c6)

copy and past te following into Input component
````js
import React, ( Component from 'react'; import axios from 'axios';
class Input extends Component {
state = [
action:
}
addTodo = () => {
const task = (action: this.state.action)
if (task.action && task.action.length > 0) {
axios.post('/api/todos, task) .then(res => { if(res.data) { this.props.getTodos(); this.setState({action: "")) }
})
.catch(err => console.log(err))
Jelse {
console.log('input field required')
handleChange = (e) => {
this.setState({
action: e.target.value
1)
render() {
let { action} = this.state;
return (
<input type="text" onChange={this.handleChange) value=(action) />
<div>
<button onClick={this.addTodo]>add todo</button>
</div>
)}
}
}
export default Input
````

To make use of Axios, which is a promise based HTTP client for the browser and node you need it to install in the parent folder which is ToDo
````js
npm install axios
````
Go to ListTodo.js copy past the following

````js
import React from 'react';

const ListTodo = ({ todos, deleteTodo }) => {
  return (
    <ul>
      {todos && todos.length > 0 ? (
        todos.map(todo => {
          return (
            <li key={todo._id} onClick={() => deleteTodo(todo._id)}>
              {todo.action}
            </li>
          );
        })
      ) : (
        <li>No todo(s) left</li>
      )}
    </ul>
  );
}

export default ListTodo;
````
in your Todo.js write the followinf code
````js
import React, { Component } from 'react';
import axios from 'axios';
import Input from './Input';
import ListTodo from './ListTodo';

class Todo extends Component {
  state = {
    todos: []
  }

  componentDidMount() {
    this.getTodos();
  }

  getTodos = () => {
    axios.get('/api/todos')
      .then(res => {
        if (res.data) {
          this.setState({ todos: res.data });
        }
      })
      .catch(err => console.log(err));
  }

  deleteTodo = (id) => {
    axios.delete(`/api/todos/${id}`)
      .then(res => {
        if (res.data) {
          this.getTodos();
        }
      })
      .catch(err => console.log(err));
  }

  render() {
    let { todos } = this.state;
    return (
      <div>
        <h1>My Todo(s)</h1>
        <Input getTodos={this.getTodos} />
        <ListTodo todos={todos} deleteTodo={this.deleteTodo} />
      </div>
    );
  }
}

export default Todo;
````

move to te src folder 
````bash
cd.. && nano App.js
````
````js
import React, { useState } from 'react';
import ListTodo from './ListTodo'; // Adjust the path if necessary

const App = () => {
  const [todos, setTodos] = useState([]);

  const deleteTodo = (id) => {
    setTodos(todos.filter(todo => todo._id !== id));
  };

  return (
    <div>
      <h1>Todo List</h1>
      <ListTodo todos={todos} deleteTodo={deleteTodo} />
    </div>
  );
};

export default App;
````
after saving next up will be some styling go to App.css 
````js
.app {
  max-width: 600px;
  margin: 50px auto;
  padding: 20px;
  background: #282c34;
  border-radius: 8px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

.todo-input {
  display: flex;
  flex-direction: column;
}

.todo-input input {
  padding: 10px;
  border: none;
  border-radius: 4px;
  margin-bottom: 10px;
}

.todo-input button {
  padding: 10px;
  border: none;
  border-radius: 4px;
  background-color: #61dafb;
  color: #282c34;
  cursor: pointer;
  font-size: 16px;
}

.todo-list {
  list-style-type: none;
  padding: 0;
}

.todo-item {
  padding: 10px;
  background: #444;
  margin: 5px 0;
  border-radius: 4px;
  cursor: pointer;
  transition: background 0.3s;
}

.todo-item:hover {
  background: #555;
}

@media only screen and (min-width: 300px) {
  .app {
    width: 80%;
  }
  .todo-input input {
    width: 100%;
  }
  .todo-input button {
    width: 100%;
    margin-top: 15px;
    margin-left: 0;
  }
}

@media only screen and (min-width: 640px) {
  .app {
    width: 60%;
  }
  .todo-input input {
    width: 50%;
  }
  .todo-input button {
    width: 30%;
    margin-left: 10px;
    margin-top: 0;
  }
}
````
index.css
````js
body {
  margin: 0;
  padding: 0;
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Oxygen", "Ubuntu", "Cantarell", "Fira Sans", "Droid Sans", "Helvetica Neue", sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  box-sizing: border-box;
  background-color: #282c34;
  color: #787a80;
}

code {
  font-family: source-code-pro, Menlo, Monaco, Consolas, "Courier New", monospace;
}
````

in the parent folder run 
````js
npm run dev
````
![image23](https://github.com/user-attachments/assets/222b8d5c-f4d6-490e-b3a2-b33921cbc0b3)
### Congratulation ✨🎉🎊🎉🎉🎉 you have completed your MERN STACK Deployment on EC2
