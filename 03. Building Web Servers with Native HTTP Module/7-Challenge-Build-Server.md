# Challenge: Build a Custom Task with Native HTTP Server

## Project Overview

This is a hands-on challenge to apply everything learned about HTTP servers, routing, and request handling. The goal is to build a functional server that handles multiple routes and logs all incoming requests.

## Challenge Requirements

### Setup

1. Create an `index.js` file
2. Initialize the project with `npm init -y`
3. Install type definitions: `npm install @types/node`
4. Configure the start script to use `node --watch index.js`

### Tasks to Implement

Your server must handle the following routes and requirements:

#### Task 1: GET Request on Root Path

- **Route:** `/`
- **Method:** GET
- **Response:** Send a simple hello message from the server
- **Status Code:** 200

Example response: `Hello from the server`

#### Task 2: GET Request on Contact Us Path

- **Route:** `/contact-us`
- **Method:** GET
- **Response:** Send email and contact number to the user
- **Status Code:** 200

Example response: `Sure, you can contact me at email@example.com and phone: +91-9999-9999`

#### Task 3: POST Request on Tweet Path

- **Route:** `/tweet`
- **Method:** POST
- **Response:** Indicate that a tweet was created (as if it was stored in a database)
- **Status Code:** 201

Example response: `Your tweet was created`

#### Task 4: GET Request on Tweet Path

- **Route:** `/tweet`
- **Method:** GET
- **Response:** Send all tweets from the database (mimicked with hardcoded tweets)
- **Status Code:** 200

Example response:

```
Tweet 1
Tweet 2
Tweet 3
```

#### Task 5: Logging All Incoming Requests

- **Requirement:** Store all incoming requests with timestamps to a `log.txt` file
- **Format:** Each log entry should include:
  - The timestamp when the request was made
  - The HTTP method (GET, POST, etc.)
  - The path that was requested

Example log entry:

```
2024-12-24T10:30:45.123Z - GET - /contact-us
2024-12-24T10:30:50.456Z - POST - /tweet
2024-12-24T10:30:55.789Z - GET - /tweet
```

### Important Implementation Notes

1. **Check the HTTP Method First** - Before checking the path, first verify that the request method is allowed for that route
2. **Handle Not Found Cases** - For any path that is not defined, respond with a 404 status code and a "You are lost" message
3. **Method Validation** - If a method is not supported for a path (e.g., POST on `/`), respond with 404 and "You are lost"
4. **Always Return a Response** - Every request must receive a response to complete the request-response cycle
5. **Use Appropriate Status Codes** - Use 201 for resource creation (POST), 200 for success, 404 for not found

## Implementation Strategy

### Step 1: Create the Basic Server

```javascript
const http = require("node:http");

const server = http.createServer((request, response) => {});

server.listen(8000, () => {
  console.log("HTTP server is running on port 8000");
});
```

### Step 2: Extract Method and URL

```javascript
const method = request.method;
const path = request.url;
```

### Step 3: Create Method Switch

First switch on the HTTP method:

```javascript
if (method === "GET") {
  // Handle GET requests
} else if (method === "POST") {
  // Handle POST requests
} else {
  // Handle other methods or 404
}
```

### Step 4: Create Path Switch

Inside each method handler, switch on the path:

```javascript
if (method === "GET") {
  switch (path) {
    case "/":
      // Handle GET /
      break;
    case "/contact-us":
      // Handle GET /contact-us
      break;
    case "/tweet":
      // Handle GET /tweet
      break;
    default:
    // Handle 404
  }
}
```

### Step 5: Implement Logging

Use the File System module to append logs:

```javascript
const fs = require("node:fs");

// Create log entry
const log = `[${Date.now}]: ${method} - ${path}\n`;

// Append to log file
fs.appendFileSync("log.txt", log, "utf-8");
```

## Complete Server Example

Here is a complete implementation to guide you:

```javascript
const http = require("node:http");
const fs = require("node:fs");

const server = http.createServer((request, response) => {
  const method = request.method;
  const path = request.url;

  const log = `[${Date.now}]: ${method} - ${path}\n`;
  fs.appendFileSync("log.txt", log, "utf-8");

  if (method === "GET") {
    switch (path) {
      case "/":
        return response.writeHead(200).end("Hello from the server");

      case "/contact-us":
        return response
          .writeHead(200)
          .end(
            "Sure, you can contact me at email@example.com and phone: +91-9999-9999"
          );

      case "/tweet":
        return response.writeHead(200).end("Tweet 1\nTweet 2\nTweet 3");

      default:
        return response.writeHead(404).end("You are lost");
    }
  } else if (method === "POST") {
    switch (path) {
      case "/tweet":
        return response.writeHead(201).end("Your tweet was created");

      default:
        return response.writeHead(404).end("You are lost");
    }
  }
});

server.listen(8000, () => {
  console.log("HTTP server is running on port 8000");
});
```

## Testing Your Server

### Using Thunder Client

1. Install Thunder Client extension in VS Code
2. Create requests for each route:
   - GET `http://localhost:8000/`
   - GET `http://localhost:8000/contact-us`
   - POST `http://localhost:8000/tweet`
   - GET `http://localhost:8000/tweet`
   - GET `http://localhost:8000/xyz`

3. Verify:
   - Correct status codes are returned
   - Correct response messages are shown
   - A `log.txt` file is created with all requests logged

### Using Browser

You can also test GET requests directly in your browser:

1. Visit `http://localhost:8000`
2. Visit `http://localhost:8000/contact-us`
3. Visit `http://localhost:8000/tweet`

## Learning Outcomes

After completing this challenge, you will have:

- Built a functional multi-route HTTP server from scratch
- Understood how to implement proper routing logic
- Learned to use appropriate HTTP status codes
- Gained experience with the fs module for file operations
- Practiced logging server requests for debugging and monitoring
- Created a server that follows HTTP conventions and best practices
