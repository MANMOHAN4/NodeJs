# Book Store API

### What You Will Build

The Book Store API will have the following features:

- A server that listens on a specific port (port 8000)
- Routes to manage books in the bookstore
- Full CRUD operations (Create, Read, Update, Delete) for books

### Application Routes

The API will have the following routes:

- **GET /books**: Returns all books in the bookstore
- **GET /books/:id**: Returns a specific book by its ID (for example, `/books/1`, `/books/2`)
- **POST /books**: Creates a new book in the bookstore
- **DELETE /books/:id**: Deletes a specific book by its ID

## Project Initialization

### Step 1: Create Project Folder and Initialize

Start by creating a new project folder and initializing Node.js:

```bash
mkdir express-book-store
cd express-book-store
npm init
```

When prompted for the package name, you can enter something like `express-book-store`.

### Step 2: Install Express

Install Express.js:

```bash
npm install express
```

### Step 3: Install Type Definitions

Install type definitions for Node.js and Express:

```bash
npm install @types/node --save-dev
npm install @types/express --save-dev
```

#### Understanding --save-dev

When you use the `--save-dev` flag (or `-D` for short), the package is installed as a **development dependency** rather than a regular dependency.

**When to use `--save-dev`:**

Development dependencies are packages that you only need while developing your application on your local machine. You don't need them on a production server.

For example:

- `@types/node`: You only need this for type hints while coding in development
- `@types/express`: You only need this for code suggestions while developing

**Why this matters:**

Once your project is ready for production, you don't want to include development dependencies on your production server. These packages can increase the size of your deployment (sometimes 2-3 MB or more). Since they're only needed during development, excluding them from production reduces the package size and deployment complexity.

**Regular dependencies:**

Some packages, like Express itself, are needed in production. If Express is not on the production server, your application will crash. So Express is installed as a regular dependency:

```bash
npm install express
```

## Creating the Server

### Step 1: Set Up the Start Script

Configure a start script in your `package.json` that uses `nodemon` to automatically restart your server when you make changes:

```json
"scripts": {
  "start": "node --watch index.js"
}
```

### Step 2: Create index.js

Create an `index.js` file with the basic server setup:

```javascript
const express = require("express");

const app = express();
const port = 8000;

app.listen(port, () => {
  console.log(`HTTP server is running on port ${port}`);
});
```

This code:

1. Imports Express
2. Creates an Express application
3. Sets the port to 8000
4. Starts the server listening on that port

## Creating the Bookstore Data

Since you don't have a database yet, you'll store book data in a JavaScript array in memory. This array will hold all the books in the bookstore.

### Initialize Books Array

Add this to your `index.js` before the `app.listen()` call:

```javascript
let books = [
  { id: 1, title: "Node.js Book", author: "Manmohan" },
  { id: 2, title: "Express Mastery", author: "Manmohan" },
];
```

The bookstore will maintain this array of books. Each book has:

- **id**: A unique identifier for the book
- **title**: The title of the book
- **author**: The author of the book

## Key Concepts

### RESTful Routing

The routes you're creating follow RESTful API design principles:

- GET requests are for retrieving data
- POST requests are for creating new data
- DELETE requests are for removing data
- Each resource (books) has its own endpoint

### In-Memory Data Storage

While this project uses an array to store books in memory, in a real-world application, you would use a database like MongoDB, PostgreSQL, or MySQL. The concepts you learn here will directly apply when you switch to using a real database.

### Request and Response

- The **request** object contains information from the client about what they want
- The **response** object is what you send back to the client

## Setting Up the POST Route for Creating Books

### Understanding POST Requests

When you make a POST request, the client needs to send a body containing the actual data you want to create. In REST APIs, this data is typically sent in JSON format.

The flow is:

1. The client sends a POST request with a JSON body
2. The client sets headers to indicate the data format (content-type: application/json)
3. The backend reads this JSON data and processes it
4. The backend sends an acknowledgement response

### Initial POST Route

Let's create a POST route for creating books:

```javascript
app.post("/books", (req, res) => {
  res.json({
    message: "This route is under development",
  });
});
```

This creates a basic POST route that acknowledges requests but doesn't do anything yet.

## Understanding Request Body and Headers

### The Problem with Raw Request Body

When you try to access `req.body` directly, you'll find it's `undefined`. This happens because:

```javascript
console.log(req.body); // undefined
```

Even though the client is sending data and setting headers (like `content-type: application/json`), the body is undefined.

### Why the Body is Undefined

Express doesn't automatically parse request bodies. Your backend has no way to know how to parse different types of data—it could be JSON, XML, plain text, or other formats.

The parsing depends on the `content-type` header:

- `application/json`: Should parse as JSON
- `text/plain`: Should parse as plain text
- `application/xml`: Should parse as XML

### Checking Headers

You can check what headers the client is sending:

```javascript
console.log(req.headers);
```

The headers will show what format the client is using, but without parsing middleware, the body remains inaccessible.

## Using Middleware for Body Parsing

### What are Middlewares?

Middlewares are like plugins for Express. They can be:

- Built-in Express middleware
- Installed from npm
- Custom middleware you create

### Using Express JSON Middleware

To parse JSON data sent from the client, use the built-in Express JSON middleware:

```javascript
app.use(express.json());
```

This middleware automatically:

- Detects when content-type is `application/json`
- Parses the incoming JSON data
- Makes the parsed data available in `req.body`

### How It Works

Once you add `app.use(express.json())` at the beginning of your server setup:

```javascript
const express = require("express");
const app = express();

// Add this middleware
app.use(express.json());

// Now your routes can access req.body
app.post("/books", (req, res) => {
  console.log(req.body); // Now this will have the data
});

app.listen(8000);
```

## Implementing the POST Route for Creating Books

### Complete POST Implementation

```javascript
app.post("/books", (req, res) => {
  // Get the latest ID
  const lastBook = books[books.length - 1];
  const newId = lastBook ? lastBook.id + 1 : 1;

  // Create a new book object
  const newBook = {
    id: newId,
    title: req.body.title,
    author: req.body.author,
  };

  // Add the book to the array
  books.push(newBook);

  // Return success response
  res.status(201).json({
    message: "Book created successfully",
    book: newBook,
  });
});
```

### How the POST Route Works

1. **Generate new ID**: Get the ID of the last book in the array and increment it
2. **Create book object**: Create a new book with the title and author from the request body
3. **Add to array**: Push the new book to the books array
4. **Return response**: Send a 201 (Created) status with the new book details

## Implementing the GET Routes

### GET All Books

```javascript
app.get("/books", (req, res) => {
  res.json(books);
});
```

This route simply returns all books in the array as JSON.

### GET Book by ID

```javascript
app.get("/books/:id", (req, res) => {
  // Get the ID from the URL parameter
  const bookId = parseInt(req.params.id);

  // Validate that the ID is a number
  if (isNaN(bookId)) {
    return res.status(400).json({
      error: "ID must be a number",
    });
  }

  // Find the book by ID
  const book = books.find((b) => b.id === bookId);

  // If book not found, return 404
  if (!book) {
    return res.status(404).json({
      error: "Book not found",
    });
  }

  // Return the book
  res.json(book);
});
```

### How the GET by ID Route Works

1. **Extract ID**: Get the ID from the URL parameter (`req.params.id`)
2. **Parse as integer**: Convert the string to a number
3. **Validate ID**: Check if the ID is a valid number
4. **Find book**: Use the `find()` method to search for the book
5. **Handle not found**: Return a 404 error if the book doesn't exist
6. **Return book**: Send the book as JSON

## Implementing the DELETE Route

### DELETE Route Implementation

```javascript
app.delete("/books/:id", (req, res) => {
  // Get the ID from the URL parameter
  const bookId = parseInt(req.params.id);

  // Validate that the ID is a number
  if (isNaN(bookId)) {
    return res.status(400).json({
      error: "ID must be a number",
    });
  }

  // Find the index of the book
  const indexToDelete = books.findIndex((b) => b.id === bookId);

  // If book not found, return 404
  if (indexToDelete < 0) {
    return res.status(404).json({
      error: "Book not found",
    });
  }

  // Delete the book using splice
  books.splice(indexToDelete, 1);

  // Return success response
  res.status(200).json({
    message: "Book deleted successfully",
  });
});
```

### How the DELETE Route Works

1. **Extract and validate ID**: Get the ID from the URL parameter and validate it's a number
2. **Find index**: Use `findIndex()` to find the position of the book in the array
3. **Handle not found**: Return 404 if the book doesn't exist
4. **Delete book**: Use `splice()` to remove the book from the array
   - `splice(indexToDelete, 1)` removes 1 element starting at the specified index
5. **Return success**: Send a success message

## Complete Server Example

Here's the complete code for the Book Store API:

```javascript
const express = require("express");
const app = express();

// Middleware
app.use(express.json());

// Initial books data
let books = [
  { id: 1, title: "JavaScript Basics", author: "Jane Smith" },
  { id: 2, title: "Web Development", author: "John Doe" },
];

// GET all books
app.get("/books", (req, res) => {
  res.json(books);
});

// GET book by ID
app.get("/books/:id", (req, res) => {
  const bookId = parseInt(req.params.id);

  if (isNaN(bookId)) {
    return res.status(400).json({ error: "ID must be a number" });
  }

  const book = books.find((b) => b.id === bookId);

  if (!book) {
    return res.status(404).json({ error: "Book not found" });
  }

  res.json(book);
});

// POST create book
app.post("/books", (req, res) => {
  const lastBook = books[books.length - 1];
  const newId = lastBook ? lastBook.id + 1 : 1;

  const newBook = {
    id: newId,
    title: req.body.title,
    author: req.body.author,
  };

  books.push(newBook);

  res.status(201).json({
    message: "Book created successfully",
    book: newBook,
  });
});

// DELETE book
app.delete("/books/:id", (req, res) => {
  const bookId = parseInt(req.params.id);

  if (isNaN(bookId)) {
    return res.status(400).json({ error: "ID must be a number" });
  }

  const indexToDelete = books.findIndex((b) => b.id === bookId);

  if (indexToDelete < 0) {
    return res.status(404).json({ error: "Book not found" });
  }

  books.splice(indexToDelete, 1);

  res.status(200).json({ message: "Book deleted successfully" });
});

// Start server
app.listen(8000, () => {
  console.log("HTTP server is running on port 8000");
});
```

## Key Takeaways

- Middlewares are essential for parsing request bodies in different formats
- Express provides built-in middleware like `express.json()` for common tasks
- When you move to a real database, you'll use similar route structures but different data operations
