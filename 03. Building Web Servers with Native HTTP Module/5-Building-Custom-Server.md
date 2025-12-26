# Building a Custom Server with Node.js: Hands-on Coding

## Project Setup

### Initialize the Project

Start a new Node.js project from scratch:

```
npm init -y
```

This creates a `package.json` file with default settings.

### Install Type Definitions

Install Node.js type definitions for better IDE support:

```
npm install @types/node
```

### Create the Entry Point File

Create an `index.js` file as the main server file.

## Building the Server Step by Step

### Step 1: Import the HTTP Module

```javascript
const http = require("http");
```

### Step 2: Create the Server

```javascript
const server = http.createServer((request, response) => {
  // Callback function for every request
});
```

### Step 3: Make the Server Listen

```javascript
server.listen(8000, () => {
  console.log("Server is running on port 8000");
});
```

### Step 4: Configure npm Scripts

Update your `package.json` to include a start script:

```json
{
  "scripts": {
    "start": "node index.js"
  }
}
```

You can now start the server with:

```
npm start
```

## Handling Requests and Responses

### Logging Incoming Requests

Inside the server callback, log each incoming request:

```javascript
http.createServer((request, response) => {
  console.log("I got an incoming request");
});
```

### Sending a Response

Always send a response to complete the request-response cycle:

```javascript
http.createServer((request, response) => {
  console.log("I got an incoming request");

  response.writeHead(200);
  response.end("Okay");
});
```

## Accessing Request Information

### Getting Request Headers

You can access headers from the request object:

```javascript
console.log(request.headers);
```

This logs all headers sent by the client. You can also access specific headers:

```javascript
const authHeader = request.headers.authorization;
```

### Getting Request Method

You can check what HTTP method was used:

```javascript
console.log(request.method);
```

This will log: `GET`, `POST`, `PATCH`, `DELETE`, etc.

### Getting Request URL

You can get the URL path being accessed:

```javascript
console.log(request.url);
```

This will log the path like `/`, `/contact-us`, `/about`, etc.

## Using npm Scripts Efficiently

Instead of manually restarting the server on every code change, use the Node.js watch feature. Update your `package.json`:

```json
{
  "scripts": {
    "start": "node --watch index.js"
  }
}
```

Now when you change your code and save, the server automatically restarts without needing to press Ctrl+C and run the command again.

## Building Routes with Switch Statements

Once you can access the request information, you can implement routing logic to handle different paths differently.

### Basic Routing with Switch

```javascript
const http = require("http");

http
  .createServer((request, response) => {
    const url = request.url;

    switch (url) {
      case "/":
        response.writeHead(200);
        response.end("You are on the homepage");
        break;

      case "/contact-us":
        response.writeHead(200);
        response.end("Contact me at email@example.com");
        break;

      case "/about":
        response.writeHead(200);
        response.end("I am a software engineer");
        break;

      default:
        response.writeHead(404);
        response.end("You are lost");
    }
  })
  .listen(8000);
```

### Testing Routes

Test your server with different paths:

1. `http://localhost:8000/` - Returns "You are on the homepage"
2. `http://localhost:8000/contact-us` - Returns contact information
3. `http://localhost:8000/about` - Returns about information
4. `http://localhost:8000/xyz` - Returns "You are lost" with 404 status

## Handling Multiple HTTP Methods

You can also create routes that respond differently based on the HTTP method:

### Checking Request Method

```javascript
const method = request.method;

switch (method) {
  case "GET":
    // Handle GET request
    break;

  case "POST":
    // Handle POST request
    break;

  case "DELETE":
    // Handle DELETE request
    break;
}
```

### Combined Method and Path Routing

You can combine both method and path checking:

```javascript
http
  .createServer((request, response) => {
    const method = request.method;
    const url = request.url;

    // First switch on method
    if (method === "GET") {
      // Then switch on path
      switch (url) {
        case "/":
          response.writeHead(200);
          response.end("Homepage");
          break;

        case "/tweets":
          response.writeHead(200);
          response.end("Tweet 1\nTweet 2\nTweet 3");
          break;

        default:
          response.writeHead(404);
          response.end("Not found");
      }
    } else if (method === "POST") {
      switch (url) {
        case "/tweet":
          response.writeHead(201);
          response.end("Tweet created successfully");
          break;

        default:
          response.writeHead(404);
          response.end("Not found");
      }
    }
  })
  .listen(8000);
```

## Sending Different Status Codes

Use appropriate HTTP status codes for different scenarios:

```javascript
// Success
response.writeHead(200);
response.end("Success");

// Resource created
response.writeHead(201);
response.end("Resource created");

// Not found
response.writeHead(404);
response.end("Not found");

// Server error
response.writeHead(500);
response.end("Server error");
```

## Complete Server Example

Here's a complete server with proper routing and status codes:

```javascript
const http = require("http");

http
  .createServer((request, response) => {
    const method = request.method;
    const url = request.url;

    if (method === "GET") {
      switch (url) {
        case "/":
          response.writeHead(200);
          response.end("Home page");
          return;

        case "/contact-us":
          response.writeHead(200);
          response.end("Contact: email@example.com, Phone: +91-9999-9999");
          return;

        case "/about":
          response.writeHead(200);
          response.end("I am a software engineer");
          return;

        case "/tweets":
          response.writeHead(200);
          response.end("Tweet 1\nTweet 2\nTweet 3");
          return;

        default:
          response.writeHead(404);
          response.end("You are lost");
          return;
      }
    } else if (method === "POST") {
      switch (url) {
        case "/tweet":
          response.writeHead(201);
          response.end("Your tweet was created");
          return;

        default:
          response.writeHead(404);
          response.end("You are lost");
          return;
      }
    }
  })
  .listen(8000, () => {
    console.log("Server is running on port 8000");
  });
```

## Key Concepts

- Use `request.method` to check the HTTP method (GET, POST, etc.)
- Use `request.url` to check the path being accessed
- Use `request.headers` to access request headers
- Set appropriate status codes with `response.writeHead(statusCode)`
- Always send a response to complete the request-response cycle
