# MEAN Stack Deployment on Ubuntu in AWS
## Introduction to the MEAN Stack
The MEAN stack is a powerful combination of technologies used to build modern, full-stack JavaScript applications. It consists of the following:

MongoDB: A NoSQL database where data is stored in flexible, JSON-like documents.
Express.js: A minimalistic framework for Node.js that simplifies the creation of backend APIs and web servers.
Angular: A front-end framework for building single-page applications (SPAs) that provides a rich user experience.
Node.js: A runtime environment that allows JavaScript to run on the server-side.
This stack enables developers to use JavaScript throughout the full stack, from the client (front end) to the server (back end) and database layer, providing efficiency and consistency.

Objective
In this guide, you will deploy a MEAN stack application on an Ubuntu EC2 instance in AWS. You’ll configure the back end with Node.js and Express.js, set up MongoDB for data storage, and build the front end using Angular.

#Prerequisites
Here’s what you need before starting:

AWS Account: You’ll use Amazon Web Services (AWS) to launch an EC2 instance.
EC2 Instance with Ubuntu: Make sure you have a running Ubuntu instance in AWS.
Basic Linux Command Knowledge: You’ll work in the Linux command-line environment.
Familiarity with the MEAN Stack Components: You should know basic concepts related to MongoDB, Express.js, Angular, and Node.js.

![image](https://github.com/user-attachments/assets/bdb49cc7-b521-422a-93f4-11054958a0a4)

![image](https://github.com/user-attachments/assets/cb41cd55-fc3c-4691-86c8-2f3f8a58d97a)


other side practice :
https://html-css-js.com/
OSI model
Load Balancing

## Step 0- Prerequisites:
* Launch EC2 instance
![image](https://github.com/user-attachments/assets/d9ce9b9b-b32e-49ac-9474-6589d93f1327)

![image](https://github.com/user-attachments/assets/b2553a30-8076-4f17-bc44-d80b2754b1a0)

* connect instance via Cli from wen cosole in aws
* or connect via ssh client
![image](https://github.com/user-attachments/assets/9d1d69b2-075e-4b94-a664-b79fa2c1a479)


## Step 1 - Add certificates and Install NodeJs

* node.js is used in this project to set up the Express routes & Angular.js controllers.
* Update ubuntu
```
sudo apt update -y
```
* upgrade ubuntu
```
sudo apt upgrade
```
![image](https://github.com/user-attachments/assets/0714fa64-823b-4530-b455-b4de83dc385f)


* Add certificate
```
sudo apt -y install curl dirmngr apt-transport-https lsb-release ca-certificates
```

```
curl -sL https://deb.nodesource.com/setup_18.x | sudo -E bash -
```

![image](https://github.com/user-attachments/assets/149779cc-3540-4571-b4a1-10df3c7f5b9c)




* install node.js
```
sudo apt install -y nodejs
```
![image](https://github.com/user-attachments/assets/41cc3e24-ef57-428d-9b58-9f1b501349eb)



## Step 2- Install MongoDB
1. Download the MongoDB public GPG key
```
sudo apt-get install -y gnupg curl
```

```
curl -fsSL https://pgp.mongodb.com/server-7.0.asc | sudo gpg --dearmor -o /usr/share/keyrings/mongodb-archive-keyring.gpg
```

2. Add the MongoDB repository
```
echo "deb [ signed-by=/usr/share/keyrings/mongodb-archive-keyring.gpg ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/7.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-7.0.list
```
![image](https://github.com/user-attachments/assets/ba30b7ae-b682-4bb8-85ac-526fb7d6acfc)




* update
````bash
sudo apt-get install -y gnupg curl

````

  ![image](https://github.com/user-attachments/assets/c15ffd34-57e8-4b8c-8b4d-35fe4ba8d711)


* Install MongoDB
  ```
  sudo apt-get install -y mongodb-org
  ```


![image](https://github.com/user-attachments/assets/9c174fbe-d782-4285-bab4-f61ecd685ead)


4. Start and enable MongoDB
```
sudo systemctl start mongod
```
```
sudo systemctl enable mongod
```

```
sudo systemctl status mongod
```
![image](https://github.com/user-attachments/assets/1aa14890-5e5d-4114-b154-a3096df14088)



* ## Install [npm](https://www.npmjs.com) - Node package manager.
```
sudo apt install -y npm
```
5. Install body-parser package

body-parser package is needed to help process JSON files passed in requests to the server.

```
sudo npm install body-parser
```
![image](https://github.com/user-attachments/assets/296bb04e-baf7-44c6-9c78-c35d0526e2b4)



6. Create the project root folder named ‘Books’

```
mkdir Books && cd Books
```

Initialize the root folder

```
npm init
```
![image](https://github.com/user-attachments/assets/2462f907-5f71-4d49-a78f-39626fc0e0c7)



Add file named server.js to Books folder

```
nano server.js
```

Copy & Past

```js
// server.js

const express = require('express');
const bodyParser = require('body-parser');
const mongoose = require('mongoose');
const path = require('path');

const app = express();
const PORT = process.env.PORT || 3300;

// MongoDB connection
mongoose.connect('mongodb://localhost:27017/test', { 
  useNewUrlParser: true, 
  useUnifiedTopology: true 
})
  .then(() => console.log('MongoDB connected'))
  .catch(err => console.error('MongoDB connection error:', err));

// Middleware for serving static files and parsing JSON requests
app.use(express.static(path.join(__dirname, 'public')));
app.use(bodyParser.json());

// Routes
app.get('/', (req, res) => {
  res.send('Welcome to the Home Page');
});

// Import and use additional routes from external file
require('./apps/routes')(app);

// Start the server
app.listen(PORT, () => {
  console.log(`Server running at http://localhost:${PORT}`);
});

```

![image](https://github.com/user-attachments/assets/167563d5-1f85-488c-bdd0-dd60457066b8)





* ### Step 3: Install Express and set up routes to the server

  ```
   sudo npm install express mongoose
  ```
  

![image](https://github.com/user-attachments/assets/a177a8e2-0478-4acf-afc7-c768b59cdf40)



* In 'Books' folder, create a folder named apps
```
mkdir apps && cd apps
```

* Create a file named routes.js
```
vi routes.js
```

Copy and paste the code below into routes.js

``` js

// routes.js

const Book = require('./models/book');
const path = require('path');

module.exports = function(app) {
  
  // GET: Fetch all books
  app.get('/book', async (req, res) => {
    try {
      const books = await Book.find();  // Fetch all books from the database
      res.json(books);                  // Return the list of books as JSON
    } catch (err) {
      res.status(500).json({
        message: 'Error fetching books',
        error: err.message
      });
    }
  });

  // POST: Add a new book
  app.post('/book', async (req, res) => {
    try {
      const book = new Book({
        name: req.body.name,
        isbn: req.body.isbn,
        author: req.body.author,
        pages: req.body.pages
      });
      const savedBook = await book.save();  // Save the new book to the database
      res.status(201).json({
        message: 'Successfully added book',
        book: savedBook
      });
    } catch (err) {
      res.status(400).json({
        message: 'Error adding book',
        error: err.message
      });
    }
  });

  // DELETE: Remove a book by its ISBN
  app.delete('/book/:isbn', async (req, res) => {
    try {
      const result = await Book.findOneAndDelete({ isbn: req.params.isbn });  // Find and delete book by ISBN
      if (!result) {
        return res.status(404).json({ message: 'Book not found' });
      }
      res.json({
        message: 'Successfully deleted the book',
        book: result
      });
    } catch (err) {
      res.status(500).json({
        message: 'Error deleting book',
        error: err.message
      });
    }
  });

  // Fallback route: Serve index.html for all undefined routes
  app.get('*', (req, res) => {
    res.sendFile(path.join(__dirname, '../public', 'index.html'));
  });
};
```

![image](https://github.com/user-attachments/assets/eb60beb2-ade9-4a09-9fb7-1d48a44c2a59)



* In the 'apps' folder, create a folder named models
```
mkdir models && cd models
```

* Create a file named book.js

```
nano book.js
```

Copy and paste the code below into 'book.js'

```js
const mongoose = require('mongoose');

// Define the book schema
const bookSchema = new mongoose.Schema({
    name: { 
        type: String, 
        required: true 
    },
    isbn: { 
        type: String, 
        required: true, 
        unique: true, 
        index: true 
    },
    author: { 
        type: String, 
        required: true 
    },
    pages: { 
        type: Number, 
        required: true, 
        min: 1 
    }
}, 
{
    timestamps: true
});

// Export the Book model
module.exports = mongoose.model('Book', bookSchema);
```

![image](https://github.com/user-attachments/assets/b2f504a2-f4e2-4010-9ed2-e4cee735ca22)




* ## Step 4 - Access the routes with AngularJS


Change the directory back to 'Books'

```
cd ../..
```


* Create a folder named public
```
mkdir public && cd public
```

![image](https://github.com/user-attachments/assets/6b39478d-8d35-4634-99e9-1740ffb267e6)




* Add a file named script.js
```
nano script.js
```

* Copy and paste the Code below (controller configuration defined) into the script.js file.

``` js
angular.module('myApp', [])
.controller('myCtrl', function($scope, $http) {

    // Function to fetch all books from the server
    function fetchBooks() {
        $http.get('/book')
        .then(response => {
            $scope.books = response.data; // Assign the retrieved data to $scope.books
        })
        .catch(error => {
            console.error('Error fetching books:', error);
        });
    }

    // Initial fetch of books when the controller is loaded
    fetchBooks();

    // Function to delete a book based on its ISBN
    $scope.del_book = function(book) {
        $http.delete(`/book/${book.isbn}`)
        .then(() => {
            fetchBooks(); // Refresh the book list after deletion
        })
        .catch(error => {
            console.error('Error deleting book:', error);
        });
    };

    // Function to add a new book
    $scope.add_book = function() {
        const newBook = {
            name: $scope.Name,
            isbn: $scope.Isbn,
            author: $scope.Author,
            pages: $scope.Pages
        };

        $http.post('/book', newBook)
        .then(() => {
            fetchBooks(); // Refresh the book list after adding a new book
            
            // Clear the form fields
            $scope.Name = '';
            $scope.Isbn = '';
            $scope.Author = '';
            $scope.Pages = '';
        })
        .catch(error => {
            console.error('Error adding book:', error);
        });
    };
});
```


* In 'public' folder, create a file named index.html

```
nano index.html
```

* Cpoy and paste the code below into index.html file.

```html
<!DOCTYPE html>
<html ng-app="myApp" ng-controller="myCtrl">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Book Management</title>
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.8.2/angular.min.js"></script>
    <script src="script.js"></script>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 20px;
        }

        table {
            border-collapse: collapse;
            width: 100%;
        }

        th,
        td {
            border: 1px solid #ddd;
            padding: 8px;
            text-align: left;
        }

        th {
            background-color: #f2f2f2;
        }

        input[type="text"],
        input[type="number"] {
            width: 100%;
            padding: 5px;
        }

        button {
            margin-top: 10px;
            padding: 5px 10px;
        }
    </style>
</head>

<body>
    <h1>Book Management</h1>

    <h2>Add New Book</h2>
    <form ng-submit="add_book()">
        <table>
            <tr>
                <td>Name:</td>
                <td><input type="text" ng-model="Name" required></td>
            </tr>
            <tr>
                <td>ISBN:</td>
                <td><input type="text" ng-model="Isbn" required></td>
            </tr>
            <tr>
                <td>Author:</td>
                <td><input type="text" ng-model="Author" required></td>
            </tr>
            <tr>
                <td>Pages:</td>
                <td><input type="number" ng-model="Pages" required></td>
            </tr>
        </table>
        <button type="submit">Add Book</button>
    </form>

    <h2>Book List</h2>
    <table>
        <thead>
            <tr>
                <th>Name</th>
                <th>ISBN</th>
                <th>Author</th>
                <th>Pages</th>
                <th>Action</th>
            </tr>
        </thead>
        <tbody>
            <tr ng-repeat="book in books">
                <td>{{book.name}}</td>
                <td>{{book.isbn}}</td>
                <td>{{book.author}}</td>
                <td>{{book.pages}}</td>
                <td><button ng-click="del_book(book)">Delete</button></td>
            </tr>
        </tbody>
    </table>
</body>

</html>
```


Change the directory back up to 'Books'

```
cd ..
```


* Start the server by running this command:

  ```
  node server.js
  ```

 ![image](https://github.com/user-attachments/assets/e0ac0485-0da7-47b4-8289-2e2ebfc307ec)



The server is now up and running, we can connect it via port 3300. You can launch a separate Putty or SSH console to test what curl command returns locally.

```
curl -s http://localhost:3300

```


Edit inbound rules 
![image](https://github.com/user-attachments/assets/ab6ff30f-aacd-44be-b533-b774f5e1f55e)

* Open Browser and opne ip on port 3300
  ```
  https://<publisc-ip-address>:3300

![image](https://github.com/user-attachments/assets/2de22402-bfe1-4e1a-b807-4f02bd009604)







### Conclusion

In this project, we successfully developed a Book Management System using the MEAN stack, which allows users to efficiently manage a collection of books. The integration of AngularJS for the frontend, Node.js and Express.js for the backend, and MongoDB for the database created a seamless user experience with real-time interaction.

This project provided a valuable hands-on opportunity to work with full-stack development while handling RESTful APIs and asynchronous operations. It further demonstrated how the MEAN stack can be used to develop scalable, high-performance web applications.
During the development process, we integrated the frontend and backend components, ensuring smooth data communication via HTTP requests. Using **Mongoose**, we efficiently modeled and validated our data. The **AngularJS** application interacts with this backend to provide real-time updates without refreshing the page.

This project provided hands-on experience in building full-stack applications, handling RESTful APIs, and working with asynchronous operations. Overall, the project showcases how the MEAN stack can be used to create efficient and scalable web applications.

