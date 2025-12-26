# HTTP Methods and Status Codes Explained for Backend Development

## The Backend Developer's Role

As a backend developer, the primary responsibility is to create a server that can:

- Accept incoming requests from clients
- Perform necessary operations (database queries, business logic, etc.)
- Send back appropriate responses

## Understanding Request-Response Cycle

1. **Client sends a request** - The client initiates the communication
2. **Server processes the request** - The server takes time to process (for example, 5 seconds)
3. **Server sends a response** - The server sends back the processed response
4. **Client closes connection** - The client closes the connection after receiving the response

Note: There is a possibility that the client can keep the connection open, which is known as **server-sent events**.

## HTTP Methods

HTTP methods define the type of action the client wants to perform on the server. When a client sends a request, it must include an HTTP method.

### GET Request

GET requests are used when a client wants to **read or retrieve data** from the server. Examples include:

- See all the tweets
- Watch a video
- See comments on a video

A GET request does not send data to the server in the request body; it only retrieves information.

### POST Request

POST requests are used when a client wants to **send data to the server** to create a new resource. Examples include:

- Create a tweet
- Upload a video
- Create a comment below a video

A POST request includes data in the request body that should be processed and stored by the server.

### PATCH Request

PATCH requests are used when a client wants to **update some existing data**. It is used to modify resources that already exist on the server.

### DELETE Request

DELETE requests are used when a client wants to **delete some data** from the server.

### PUT Request

PUT request is another method that can be used for updating resources.

## HTTP Status Codes

HTTP status codes are numeric values **sent by the server** in the response to indicate the result of the request. These are important for communicating the outcome to the client.

### Status Code Categories

Status codes are grouped by their first digit:

#### 100-199 - Informational Response

#### 200-299 - Success Response

#### 300-399 - Redirect

#### 400-499 - Client Error

#### 500-599 - Server Error

### Common Status Codes

- **200** - Success, everything is okay
- **201** - Success, a new resource was created
- **400** - Bad request, the client's request was invalid
- **401** - Unauthorized, authentication is required
- **403** - Forbidden, the client does not have permission
- **404** - Not found, the requested resource does not exist
- **500** - Internal server error, something went wrong on the server

## Best Practices for Backend Development

A good API or good backend should follow HTTP conventions by:

- Using appropriate HTTP methods (GET for retrieval, POST for creation, PATCH for updates, DELETE for deletion)
- Returning appropriate status codes that match the result of the operation
- Not violating HTTP standards
