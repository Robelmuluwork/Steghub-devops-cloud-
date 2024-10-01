# MEAN Stack Deployment to  Ubuntu in AWS
## MEAN
1. MangoDB--Documnet Database --> Store & Allows to retrive data.
2. Express -- Backend Application Framework --> Makes requests to database for Read & Wirtes.
3. Angular -- Fromtend Application Framework -->Handle Client & Server Request.
4. Node.js -- JavaScript Runttime Environment --> Accepts request & display results to end user.

![image](https://github.com/user-attachments/assets/bdb49cc7-b521-422a-93f4-11054958a0a4)

![image](https://github.com/user-attachments/assets/cb41cd55-fc3c-4691-86c8-2f3f8a58d97a)


other side practice :
https://html-css-js.com/
OSI model
Load Balancing

## Step 0- Prerequisites:
* Launch EC2 instance
![image](https://github.com/user-attachments/assets/c2d5cf88-10a2-4713-ae89-1bff8652868f)

![image](https://github.com/user-attachments/assets/b2553a30-8076-4f17-bc44-d80b2754b1a0)

* connect instance via Cli from wen cosole in aws
* or connect via ssh client
![image](https://github.com/user-attachments/assets/9d1d69b2-075e-4b94-a664-b79fa2c1a479)


## Step 1 - Install NodeJs
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
![image](https://github.com/user-attachments/assets/ec597e9e-4e53-4b02-b73b-75dbef51c710)



```
curl -sL https://deb.nodesource.com/setup_18.x | sudo -E bash -
```

![image](https://github.com/user-attachments/assets/f53bcab1-e683-494a-bc11-22c42b48be50)



* install node.js
```
sudo apt install -y nodejs
```
![image](https://github.com/user-attachments/assets/28ce6cf1-f4c4-41fd-8215-55d436c1843d)


## Step 2- Install MongoDB
1. Download the MongoDB public GPG key
```
sudo apt-get install -y gnupg curl
```
![image](https://github.com/user-attachments/assets/d31ed48e-d9bf-4bf6-ad2c-0be7e0182027)

```
curl -fsSL https://pgp.mongodb.com/server-7.0.asc | sudo gpg --dearmor -o /usr/share/keyrings/mongodb-archive-keyring.gpg
```

2. Add the MongoDB repository
```
echo "deb [ signed-by=/usr/share/keyrings/mongodb-archive-keyring.gpg ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/7.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-7.0.list
```
![image](https://github.com/user-attachments/assets/fa7c2426-0339-422e-ba95-34e7183feb67)



* update
![image](https://github.com/user-attachments/assets/1c09a282-2d03-4e31-8e59-bc906220093d)




* Install MongoDB
  ```
  sudo apt-get install -y mongodb-org
  ```
![image](https://github.com/user-attachments/assets/64fe128b-3514-41ab-a919-65f965605e68)


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
![image](https://github.com/user-attachments/assets/6e8523ec-7ab1-4925-9159-0c4b85ab929d)


* ## Install [npm](https://www.npmjs.com) - Node package manager.
```
sudo apt install -y npm
```
5. Install body-parser package

body-parser package is needed to help process JSON files passed in requests to the server.

```
sudo npm install body-parser
```
![image](https://github.com/user-attachments/assets/76c07966-f37c-4ee8-8d1b-91ee2d57933e)


6. Create the project root folder named ‘Books’

```
mkdir Books && cd Books
```

Initialize the root folder

```
npm init
```

![image](https://github.com/user-attachments/assets/bced0332-7d13-4cf2-b985-6be90009a310)


Add file named server.js to Books folder

```
vim server.js
```

Copy & Past

```
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

![image](https://github.com/user-attachments/assets/f1a7a88f-98d4-4714-96c0-245f15c781da)




* ### Step 3: Install Express and set up routes to the server

  ```
   sudo npm install express mongoose
  ```
  

![image](https://github.com/user-attachments/assets/87eba925-d3e2-4af4-b94c-ed98d0416534)


* In 'Books' folder, create a folder named apps
```
mkdir apps && cd apps
```

* Create a file named routes.js
```
vi routes.js
```

Copy and paste the code below into routes.js

```

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

![image](https://github.com/user-attachments/assets/10e74b53-95c8-4e5a-bc38-6b513ccc0693)


* In the 'apps' folder, create a folder named models
```
mkdir models && cd models
```

* Create a file named book.js

```
vi book.js
```

Copy and paste the code below into 'book.js'

```
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

![image](https://github.com/user-attachments/assets/c23ec96b-957c-4e08-89b0-591b1a395185)



* ## Step 4 - Access the routes with AngularJS


Change the directory back to 'Books'

```
cd ../..
```

![image](https://github.com/user-attachments/assets/380c7c05-c0be-41c7-b213-8822aeadc247)


* Create a folder named public
```
mkdir public && cd public
```

![image](https://github.com/user-attachments/assets/4a10545c-af2e-4a57-8b34-edc7a12453e6)



* Add a file named script.js
```
vi script.js
```

* Copy and paste the Code below (controller configuration defined) into the script.js file.

```
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
vi index.html
```

* Cpoy and paste the code below into index.html file.

```
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

```
ls
```

![image](https://github.com/user-attachments/assets/aa16be23-64bd-4a17-bc8b-16ba6e39a6c3)



Change the directory back up to 'Books'

```
cd ..
```
![image](https://github.com/user-attachments/assets/d0c348f6-5395-4e5e-8606-ff7c6979cf94)


* Start the server by running this command:

  ```
  node server.js
  ```

  ![image](https://github.com/user-attachments/assets/613db937-42b1-44ea-8fef-da4426b6b305)


The server is now up and running, we can connect it via port 3300. You can launch a separate Putty or SSH console to test what curl command returns locally.

```
curl -s http://localhost:3300

```


Edit inbound rules 
![image](https://github.com/user-attachments/assets/ab6ff30f-aacd-44be-b533-b774f5e1f55e)

* Open Browser and opne ip on port 3300
  ```
  https://<publisc-ip-address>:3300

![image](https://github.com/user-attachments/assets/128b272e-1a25-4eba-bb17-895ebc5549a6)





### Conclusion

In this project, we successfully developed a **Book Management System** using the MEAN stack (MongoDB, Express.js, AngularJS, and Node.js). The application allows users to manage a collection of books by adding, viewing, and deleting books through a user-friendly interface.

Key highlights of the project include:
- **Frontend (AngularJS)**: A dynamic and interactive single-page application (SPA) that provides a seamless user experience for managing books.
- **Backend (Node.js and Express.js)**: A robust RESTful API to handle book operations such as retrieving, adding, and deleting books.
- **Database (MongoDB)**: A NoSQL database to store book details, ensuring efficient and scalable data management.
  
During the development process, we integrated the frontend and backend components, ensuring smooth data communication via HTTP requests. Using **Mongoose**, we efficiently modeled and validated our data. The **AngularJS** application interacts with this backend to provide real-time updates without refreshing the page.

This project provided hands-on experience in building full-stack applications, handling RESTful APIs, and working with asynchronous operations. Overall, the project showcases how the MEAN stack can be used to create efficient and scalable web applications.

