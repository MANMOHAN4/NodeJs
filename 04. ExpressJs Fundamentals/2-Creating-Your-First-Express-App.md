# Creating Your First Express App and File Structure Setup

## Getting Started with Express.js

### Creating a Project Directory

Start by navigating to your coding directory and create a new folder for your Express project.

### Initializing the Project

Once inside your project folder, initialize a new Node.js project:

```bash
npm init
```

This creates a `package.json` file that tracks your project metadata and dependencies.

## Installing Dependencies

### Installing Type Definitions for Node.js

Before installing Express, install the type definitions for Node.js to get better code suggestions in your editor:

```bash
npm install @types/node
```

### Installing Express

Now install Express.js:

```bash
npm install express
```

When you install Express, it also installs all of its dependencies. If you check your `node_modules` folder, you'll see many packages like `accepts`, `array-flatten`, `body-parser`, and others. This is because Express depends on many internal packages, and npm automatically pulls all of them.

## Installing Type Definitions for Express

To get code suggestions and type hints for Express in your editor, install the types for Express:

```bash
npm install @types/express
```

## Creating Your First Express Application

### Creating the index.js File

Create a new file called `index.js` in your project folder:

```javascript
const express = require("express");

const app = express();
```

### Understanding the Code

- `const express = require('express')`: This imports the Express module
- `const app = express()`: This creates an Express application instance that you'll use to define routes and configure your server

### Defining Routes

#### GET Request to Homepage

```javascript
app.get("/", (req, res) => {
  res.end("home page");
});
```

This creates a route that listens for GET requests on the root path (`/`). When a request comes in, it returns the response `home page`.

#### GET Request to Contact Us

```javascript
app.get("/contact-us", (req, res) => {
  res.end("contact me at my email address");
});
```

This route responds to GET requests on the `/contact-us` path.

#### POST Request to Tweets

```javascript
app.post("/tweets", (req, res) => {
  // Handle the request
});
```

### Understanding Request and Response

- **Request (req)**: An object that contains information about the incoming HTTP request from the client, including parameters, headers, and body data
- **Response (res)**: An object that you use to send a response back to the client

### Starting the Server

To make your server listen on a specific port, add this at the end of your code:

```javascript
app.listen(3000, () => {
  console.log("Server is running on port 3000");
});
```

This tells your Express application to listen for incoming requests on port 3000.

## Running Your Application

To start your server, run:

```bash
node index.js
```

Your server will now listen for requests on the specified port, and you can make HTTP requests to the defined routes.
