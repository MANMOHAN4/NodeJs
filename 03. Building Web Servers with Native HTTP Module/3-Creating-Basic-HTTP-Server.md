# Creating a Basic HTTP Server with Core Node.js Module

## Introduction

Before starting to build practical servers, it is important to understand the theoretical concepts. Now, let's move forward with the practical implementation of creating a server using Node.js.

## Setting Up the Project

### Creating the Project Directory

Create a new directory for the HTTP server project:

```
mkdir http-server
cd http-server
```

### Initializing the Project

Initialize a new Node.js project with default settings:

```
npm init -y
```

This creates a `package.json` file with default configurations.

### Installing Type Definitions

To ensure proper code completion and type support in VS Code, install the Node.js type definitions:

```
npm install @types/node
```

### Creating the Entry Point File

Create an `index.js` file which will be the entry point of the application:

```
touch index.js
```

## Building Your First Server

### Importing the HTTP Module

The HTTP module is a built-in Node.js module that helps create a server:

```javascript
const http = require("node:http");
```

You can verify that HTTP is a built-in module by checking the official Node.js documentation.

### Creating a Server Instance

Create a server using the `http.createServer()` method:

```javascript
const server = http.createServer();
```

The `createServer()` method creates a server instance and returns it.

### Making the Server Listen on a Port

Bind the server to a specific port using the `listen()` method:

```javascript
server.listen(8000, () => {
  console.log("HTTP server is up and running on port 8000");
});
```

- **Port 8000** is used for development purposes
- The callback function runs only if the server successfully binds to the port
- The callback provides confirmation that the server is running

### Running the Server

To run the server, execute:

```
node index.js
```

You should see the output:

```
HTTP server is up and running on port 8000
```

To stop the server, press `Ctrl + C` in the terminal.

## Understanding the Request-Response Cycle

### The HTTP Callback Function

The `createServer()` method takes a callback function with two parameters:

```javascript
http.createServer((request, response) => {
  // This function runs for every incoming request
});
```

- **request** object: Contains information about the incoming request from the client
- **response** object: Used to construct and send a response back to the client

### Handling Requests

Every time a client makes a request to the server, the callback function is invoked. You can add logic to handle the request:

```javascript
http.createServer((request, response) => {
  console.log("I got an incoming request");
});
```

### Sending a Response

You must always send a response to the client. If you don't send a response, the browser will hang indefinitely waiting for one.

To send a response, use the `response.writeHead()` and `response.end()` methods:

```javascript
response.writeHead(200);
response.end("Thanks for visiting my server");
```

- `writeHead(200)` - Sets the HTTP status code to 200 (success)
- `end()` - Sends the response body and closes the connection

## Complete Basic Server Example

```javascript
const http = require("node:http");

const server = http.createServer((request, response) => {
  console.log("I got an incoming request");
  response.writeHead(200);
  response.end("Thanks for visiting my server");
});

server.listen(8000, () => {
  console.log("HTTP server is up and running on port 8000");
});
```

## Testing the Server

1. Run the server: `node index.js`
2. Open a web browser and navigate to `http://localhost:8000`
3. You should see the message: "Thanks for visiting my server"
4. In the terminal, you should see: "I got an incoming request"

## Understanding Request and Response Objects

### Request Object

The `request` object contains information about the incoming request:

- **request.method** - The HTTP method used (GET, POST, etc.)
- **request.url** - The URL path being accessed
- **request.headers** - The headers sent by the client (key-value pairs)

Example:

```javascript
console.log(request.headers);
```

### Response Object

The `response` object is used to send data back to the client:

- **response.writeHead(statusCode)** - Sets the HTTP status code and headers
- **response.end(data)** - Sends the response body and closes the connection

## Port Numbers

### What is a Port?

A port is like a room number on a server machine. Think of it this way:

- A physical machine (like your MacBook) is like a house
- Multiple services can run on a single machine
- Each service listens on a different port to avoid conflicts

### Port Analogy

If a physical machine has an IP address of `192.168.1.1`, you can have:

- Database service listening on port 3001
- Node.js application listening on port 8000
- Another service listening on port 3002

When you want to access a service on a machine, you specify:

- The **IP address** of the machine
- The **port** of the service

For example: `192.168.1.1:8000` means "connect to the service on port 8000 of the machine at 192.168.1.1"

### localhost

`localhost` is a special hostname that refers to your local machine. So `localhost:8000` means "connect to port 8000 on my own machine".

### Port Flexibility

You can run your server on any port:

- `8000`, `8001`, `8002`, `8300`, etc.
- The only requirement is that no other service should be using the same port on your machine

## Auto-Reloading Server on Code Changes

In Node.js, the server does not automatically restart when you change the code. You need to manually stop and restart it each time you make changes.

To avoid this repetitive process, you can use the `--watch` flag (in newer Node.js versions) or use tools like `nodemon`.

### Using Node.js Watch Flag

Update your `package.json` start script:

```json
{
  "scripts": {
    "start": "node --watch index.js"
  }
}
```

Run the server:

```
npm start
```

Now whenever you save changes to your code, the server will automatically restart.

## Key Concepts

- The HTTP module is a built-in Node.js module for creating servers
- `http.createServer()` creates a server instance
- The callback function in `createServer()` runs for every incoming request
- You must always send a response using `response.writeHead()` and `response.end()`
