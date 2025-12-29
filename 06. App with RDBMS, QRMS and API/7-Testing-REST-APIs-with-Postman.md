# Testing Your REST APIs Using Postman

## What is Postman?

Postman is a tool widely used in the industry for testing and documenting REST APIs. Despite not being a personal favorite, it is important to become proficient with Postman because of its widespread adoption in professional development.

## Installation

Visit `postman.com` and download Postman for your operating system:
- Windows
- macOS
- Linux

Postman is available for all major platforms, making it universally accessible.

## Getting Started with Postman

### Creating a New Request

After opening Postman, you can create a new request by:
- Pressing `Ctrl + N` (Windows/Linux)
- Pressing `Cmd + N` (macOS)
- Using the File menu

### Request Components

A new request window includes:
- **HTTP Method Dropdown:** GET, POST, PUT, PATCH, DELETE
- **URL Field:** Where you enter the endpoint URL
- **Authorization Tab:** For adding authentication credentials
- **Headers Tab:** For adding custom HTTP headers
- **Body Tab:** For sending request data in JSON or other formats
- **Response Section:** Shows the response from the server with:
  - Status code
  - Response time
  - Headers received
  - Response body

## Testing the Bookstore API

### Starting the Application

Before testing, ensure:
1. Docker container is running: `docker compose up -d`
2. Application server is running: `npm start`

The server should be running on `localhost:8000`

### Basic Testing Example

**Request URL:** `http://localhost:8000/books`

**Method:** GET

**Testing Steps:**
1. Enter the URL in the address field
2. Verify the method is set to GET
3. Click Send
4. Observe the response

**Expected Response:**
```json
[
  {
    "id": "uuid-1",
    "title": "Learn Node JS",
    "description": "A comprehensive guide",
    "authorId": "uuid-author"
  },
  {
    "id": "uuid-2",
    "title": "Learn JS with Me",
    "description": "JavaScript basics",
    "authorId": "uuid-author"
  }
]
```

**Response Details:**
- Status Code: 200 (Success)
- Headers: Information about the response (content-type, etc.)
- Response Time: How long the request took

## Creating Collections for Organization

### Best Practice: Organize with Collections

In industry standards, APIs are organized into collections:

1. Click on **Create a Collection**
2. Name it (e.g., "Bookstore")
3. Add a description (optional)

### Collection Benefits

Collections serve as:
- **Organization:** Group related endpoints together
- **Documentation:** Explain what each endpoint does
- **Sharing:** Can be published and shared with team members
- **Frontend Documentation:** Frontend developers can reference the collection

### Adding Requests to Collections

Once you have a successful API call:
1. Click the **Save** button
2. Enter a descriptive name (e.g., "Get All Books")
3. Select the collection to save it in
4. Click Save

**Good naming conventions:**
- "Get All Books"
- "Get Book by ID"
- "Create Book"
- "Delete Book by ID"
- "Get All Authors"
- etc.

### Collection Documentation

Once you have added requests to a collection:
1. Click on the collection name
2. Add a description for the collection
3. Include:
   - Overview of the API
   - Authentication requirements
   - Base URL information
   - General usage guidelines

**Example Description:**
```
Welcome to the Official APIs for Managing Bookstore

This API allows you to:
- Retrieve all books
- Get a specific book by ID
- Create new books
- Delete books
- Manage authors
```

## Collections Features

### Description Editor

- Use Markdown editor for formatting
- Use postman editor for Postman-specific syntax
- Write clear, professional descriptions

### Pre-request Scripts

Write scripts that run before each request:
- Set up test data
- Generate authentication tokens
- Set variables

### Scripts

Write scripts that run after each request:
- Validate responses
- Extract data from responses
- Run test assertions

### Variables

Define variables specific to the collection:
- **Base URL:** Instead of hardcoding `localhost:8000`, use a variable
- **API Keys:** Store authentication tokens
- **Dynamic Values:** Extract IDs from responses for use in subsequent requests

**Example Variable Setup:**
```
baseUrl = http://localhost:8000
```

Instead of using the full URL in each request, use:
```
{{baseUrl}}/books
```

## Publishing Collections

### Viewing Documentation

After creating a collection:
1. Click "View Complete Collection" or go to collection settings
2. A documentation page is generated automatically
3. The documentation shows:
   - All endpoints in the collection
   - Code examples for multiple languages:
     - JavaScript
     - Python
     - cURL
     - Ruby
     - PHP
     - And many more
   - Required parameters
   - Response examples

### Publishing to the Internet

To publish your collection for team access:
1. Click the **Publish** button
2. Log in to your Postman account
3. The collection will be published with a public URL
4. Share the URL with team members (frontend developers, other backend developers, etc.)

**Benefits:**
- Frontend developers can see all available endpoints
- Team members can test the API without setting up locally
- Serves as official API documentation
- Can be shared with external partners or clients
- Provides a standardized reference

## Team Collaboration

### Inviting Team Members

In Postman, you can:
- Invite teammates to view the collection
- Assign different access levels
- Comment on specific requests
- Collaborate on API testing

### Sharing Collections

Collections can be:
- Shared via direct links
- Embedded in documentation websites
- Versioned for different API releases
- Synced across team members

## Environment Management

### Creating Environments

Postman supports multiple environments (development, testing, production):

1. Create an environment for localhost
2. Create an environment for testing server
3. Create an environment for production

Each environment can have different variable values:

**Development Environment:**
```
baseUrl = http://localhost:8000
```

**Production Environment:**
```
baseUrl = https://api.bookstore.com
```

Switch between environments easily without changing requests.

## Testing Different Endpoints

### Testing POST Request

**URL:** `http://localhost:8000/books`

**Method:** POST

**Body (JSON):**
```json
{
  "title": "New Book Title",
  "authorId": "author-uuid",
  "description": "Book description"
}
```

**Steps:**
1. Set method to POST
2. Enter URL
3. Click on Body tab
4. Select JSON format
5. Enter request body
6. Click Send

### Testing DELETE Request

**URL:** `http://localhost:8000/books/{book-id}`

**Method:** DELETE

**Steps:**
1. Set method to DELETE
2. Enter URL with the book ID to delete
3. Click Send

## Key Postman Features

| Feature | Purpose |
|---------|---------|
| Collections | Organize API endpoints |
| Environments | Manage different deployment settings |
| Variables | Store and reuse values |
| Pre-request Scripts | Execute code before requests |
| Tests | Validate responses |
| Documentation | Generate API documentation |
| Publish | Share API documentation publicly |

## Professional Workflow

1. **Create Collection:** Group related endpoints
2. **Add Requests:** Save each API call with descriptive names
3. **Use Variables:** For URLs, API keys, and dynamic values
4. **Write Tests:** Validate responses automatically
5. **Document:** Add descriptions and examples
6. **Publish:** Share with team and external users

## Summary

Postman is essential for:
- Testing APIs during development
- Documenting API endpoints
- Sharing API specifications with teams
- Collaborating on API design
- Automating testing
- Managing different environments

While it may not be the preferred tool for everyone, proficiency with Postman is a valuable skill in professional development environments.