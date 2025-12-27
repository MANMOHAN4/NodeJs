# REST API Design Principles for Modern Backend Development

## What is a REST API?

A **REST API** stands for **Representational State Transfer API**, also known as a **RESTful API**. It is an _architectural style_ for designing backend applications and APIs. It's important to note that REST is a general architectural concept, not specific to Node.js or Express. This architectural style applies regardless of the programming language you use, whether you're using Java, Rust, JavaScript, or any other language.

## Key Principles of REST APIs

To call your API a RESTful API, you must follow certain key principles. These principles ensure your API is well-designed, scalable, and maintainable.

### 1. Communication Over HTTP

All communication in a REST API happens over the **HTTP protocol**. This is straightforward and forms the foundation of RESTful APIs.

### 2. Statelessness

This is the most important principle of REST APIs: **there should be absolutely no state maintained on the server**.

Statelessness means that if a user sends a request to your server, you cannot store any user-related data in the server's memory. You cannot maintain state in your server for individual users.

If you need to store user-related information, you must use a database such as:

- PostgreSQL
- MongoDB
- Redis
- Other databases

But you cannot store this information in the server's memory.

Statelessness is crucial because, in reality, servers automatically scale up and down. When a server scales down, the data stored in its memory is lost. If you store user-related information in the server's memory, that data will be lost when the server scales. This violates the principle of statelessness and makes your servers not truly scalable.

### 3. Client-Server Architecture

Your API should follow a **client-server architecture**, which means:

- **Client**: A separate application (can be a website, iOS app, Android app, or any other client application)
- **Server**: Your backend API server

The client and server should be completely separate applications with no code coupling.

The backend server should NOT be responsible for rendering the UI. You should not send HTML or CSS data from the server to the client. Instead:

- The server should only send **data in a specific format** like JSON
- The client application is responsible for rendering this data on the user interface

This separation ensures that the client and server can evolve independently.

### 4. Uniform Interface

There should be a **uniform and predictable interface** for your API. This means using HTTP methods like GET, POST, PUT, and DELETE in a predictable and common-sense way.

#### Example of Predictable Interface

If you create a GET route at `/tweets`, it should obviously return all tweets. This is predictable and makes common sense.

However, if you design it so that a GET request to `/tweets` creates a new tweet instead of returning all tweets, this is not predictable and violates the principle of uniform interface. There's no common sense in this design.

#### Why Uniform Interface Matters

When you tell someone that there is a GET route at `/tweets`, they should immediately understand that this route will return all tweets. This predictability makes the API easy to use and understand.

### 5. Cacheability

The responses that you send from the backend should be **cacheable**. This means:

- Servers can mark responses as cacheable
- This allows the client to cache the responses locally
- When the same request is made again, the client can use the cached response instead of making another server request

Cacheability improves performance and reduces the load on your server.

## Building RESTful APIs

### Following REST Principles

When building REST APIs, you should follow all these principles to create:

- **Highly performant** APIs
- **Highly available** APIs
- **Highly scalable** APIs

### Companies Use RESTful APIs

Approximately 99% of companies use RESTful APIs for their backend systems. This makes it essential to understand and implement REST principles correctly.

## Testing Your REST APIs

When developing REST APIs, you need a way to test them. Since you don't have a UI client like a React application during development, you can use API Client tools.

## Key Takeaways

- REST is an architectural style that applies to any programming language or framework
- Statelessness is the most critical principle—store data in databases, not in server memory
- Client-server separation keeps frontend and backend independent
- Uniform interface means using HTTP methods predictably
- Cacheability improves performance and scalability
