# Introduction to Express Framework and Its Core Features

## The Problem with Raw HTTP Module

When building applications with Node.js using the raw HTTP module, the code becomes difficult to manage and maintain. Even with a small codebase, the code structure is messy and unorganized.

Consider a scenario where you're working in a company with a large codebase that includes database integration, Redis integration, rate limiting, and multiple features. With five to six developers on the team, the amount of complexity would increase significantly. Without a proper framework, you cannot rely on switch cases and manual implementations to handle all the routing and logic. This approach does not provide a good developer experience.

## What is Express.js?

Express.js is a very popular and battle-tested _framework_ in the Node.js ecosystem that has been in the market for years and will continue to remain relevant. It addresses the problems associated with using the raw HTTP module.

Express.js internally uses the HTTP package to create servers, but it provides a much cleaner and more structured way to write your code. Instead of manually managing routes and requests, Express abstracts away the messy parts and creates a more maintainable and organized codebase.

### Key Benefits of Express.js

- **Consistency**: Express creates consistency among developers, ensuring that everyone follows a structured approach
- **Code Organization**: It takes away the messy parts of server creation and creates code in a more structured and maintainable way
- **Declarative Syntax**: Express provides a clean and declarative way to define routes and handle requests

## Hello World Example with Express

Here is how clean Express.js code looks compared to the raw HTTP module:

```javascript
const express = require("express");
const app = express();

app.get("/", (req, res) => {
  res.send("Hello World");
});

app.listen(3000, () => {
  console.log("Server is running on port 3000");
});
```

This is extremely declarative and clean. You simply:

1. Create an Express application
2. Define a GET request handler for a path
3. Send a response
4. Listen on a port

## What You Need to Learn with Express

When coding a server, many things come into play:

- **Header Parsing**: Understanding how to parse request headers
- **Body Parsing**: Knowing how to parse request bodies
- **Response Sending**: Understanding how to send different types of responses
- **Code Organization**: Learning how to declutter your code and move code into separate files
- **Middleware**: Understanding how to use and create middleware

All of these aspects are covered and made easier by Express.js.

## Why Express.js is Industry Standard

All companies, whether big or small, extensively use Express.js. It is the _standard framework_ for building Node.js applications. If you master Express, you can easily adapt to other frameworks and libraries as well.
