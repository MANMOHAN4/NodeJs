# Advanced Middleware Concepts and Custom Middleware Implementation

## Types of Middlewares

There are different types of middleware that can be implemented in Express applications based on their scope and execution pattern.

### Global Middlewares

Global middlewares are middleware functions that run for every request, regardless of the route or request method.

**Characteristics of Global Middlewares:**

- They run every time without exception
- They execute no matter which route the request is directed to
- They must be defined at the very top of your application code
- They will always execute for every incoming request
- Global middlewares are registered using `app.use()`

### Route Level Middlewares

Route level middlewares are middleware functions that only run for specific routes.

**How Route Level Middlewares Work:**

Route level middlewares can be attached to specific routes and only execute when a request matches that particular route.

**Implementation Pattern:**

```javascript
app.get("/path", middlewareFunction, (req, res) => {
  // Route handler
});
```

When a request comes to this specific route, the following execution order occurs:

1. The global middlewares execute first
2. The route level middleware executes
3. The route handler executes
4. The response is sent back

**Multiple Route Level Middlewares:**

You can chain multiple middlewares on a single route:

```javascript
app.get("/path", middleware1, middleware2, (req, res) => {
  // Route handler
});
```

When multiple middlewares are used on a route:

1. `middleware1` executes first
2. When `middleware1` calls `next()`, the next function points to `middleware2`
3. When `middleware2` calls `next()`, the next function points to the route handler
4. The route handler executes

**Extracting and Reusing Middlewares:**

You can extract middleware functions and define them separately to reuse them across multiple routes:

```javascript
function loggerMiddleware(req, res, next) {
  // Logging logic
  next();
}

function customMiddleware(req, res, next) {
  console.log("I am a custom middleware");
  next();
}

app.use(loggerMiddleware);
app.get("/route", loggerMiddleware, customMiddleware, (req, res) => {
  // Route handler
});
```

### Route Specific Path Middlewares

Middlewares can also be registered to execute only when a specific path matches, regardless of the HTTP method.

**Implementation:**

```javascript
app.use("/books", middlewareFunction);
```

**Behavior:**

- This middleware will run for ANY HTTP method (GET, POST, PUT, DELETE, etc.) on the `/books` path
- It does not need to match the entire path structure
- Any request to `/books` or paths starting with `/books` will trigger this middleware
- This is useful when you need a middleware to apply to all requests on a specific resource

**Execution Flow:**

When a request matches the path, the middleware executes and can either:

1. End the request-response cycle by sending a response
2. Modify the request object
3. Call `next()` to pass control to the next middleware or route handler

## Middleware Execution Semantics

### Terminating the Request-Response Cycle

Middleware can terminate the request-response cycle by sending a response without calling `next()`:

```javascript
function middlewareName(req, res, next) {
  res.send("Response sent");
  // Not calling next() terminates the cycle
}
```

### Continuing to Next Middleware

Middleware continues the chain by calling `next()`:

```javascript
function middlewareName(req, res, next) {
  // Perform operations
  next(); // Passes control to next middleware or route handler
}
```

When `next()` is called:

- In a global middleware, `next()` points to the next global middleware or to route matching
- In a route level middleware, `next()` points to the next route level middleware or the route handler

### Practical Middleware Patterns

**Pattern 1: Global Logger**

```javascript
app.use((req, res, next) => {
  // Logging logic for all requests
  next();
});
```

**Pattern 2: Route Specific Middleware**

```javascript
app.get("/books", middlewareFunction, (req, res) => {
  // This middleware only runs for this specific route
  res.send("Books list");
});
```

**Pattern 3: Path Based Middleware**

```javascript
app.use("/books", (req, res, next) => {
  // This runs for all requests to /books path
  // Regardless of HTTP method
  next();
});
```

## Router Middlewares

Router middlewares are middlewares that are registered on Express Router instances. These are covered in depth when discussing routers, as they require understanding the router concept first.

## Important Notes on Middleware

### Error Handling in Middleware

Starting with Express 5.0, middleware functions that return a promise will automatically handle rejection by throwing an error. This changes how asynchronous middleware should be handled and errors should be managed in modern versions of Express.

## Key Takeaways

- **Global middlewares** apply to all requests
- **Route level middlewares** apply to specific routes
- **Path based middlewares** apply to specific paths regardless of HTTP method
- Middleware can be chained together for sequential processing
- Middleware can either terminate or continue the request-response cycle
- Understanding middleware is essential for building robust and maintainable Express applications
