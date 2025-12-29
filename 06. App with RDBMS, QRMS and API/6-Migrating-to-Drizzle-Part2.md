# Migrating Book Store to Drizzle ORM: Part 2 - Query Logic

## Overview

In this part, we refactor the book controllers to use Drizzle ORM with a real PostgreSQL database instead of an in-memory database.

## Controller Implementation

### Get All Books

```javascript
const getAllBooks = async (req, res) => {
  const books = await db.select().from(booksTable);
  return res.json(books);
};
```

**Explanation:**
- `db.select()` - Initiates a SELECT query
- `.from(booksTable)` - Specifies which table to select from
- Database operations return promises because they are asynchronous
- `await` keyword waits for the database to return the results
- Database queries are asynchronous because the database sits on a different server
- Returns an array of all books

### Get Book by ID

```javascript
const getBookById = async (req, res) => {
  const [book] = await db
    .select()
    .from(booksTable)
    .where(eq(booksTable.id, req.params.id))
    .limit(1);

  if (!book) {
    return res.status(404).json({ error: "Book not found" });
  }

  return res.json(book);
};
```

**Explanation:**
- UUID IDs are no longer strings, so previous validation checks can be removed
- `eq()` function is imported from drizzle-orm for equality comparisons
- `where(eq(booksTable.id, req.params.id))` adds a WHERE clause to filter results
- `limit(1)` ensures only one result is returned
- The query returns an array (even if it's only one book)
- Destructure the first element: `const [book] = ...`
- Check if the book exists; return 404 if not found
- Return the book if found

### Create a Book

```javascript
const createBook = async (req, res) => {
  const { title, authorId, description } = req.body;

  const result = await db
    .insert(booksTable)
    .values({
      title: title,
      authorId: authorId,
      description: description
    })
    .returning(booksTable.id);

  const [{ id }] = result;

  return res.json({ message: "Book created successfully", id: id });
};
```

**Explanation:**
- Destructure required fields from the request body
- `db.insert(booksTable)` initiates an INSERT query
- `.values()` specifies the column values to insert
- `.returning(booksTable.id)` tells the database to return the generated ID
- The database automatically generates a UUID for the book
- `result` is an array containing the inserted record information
- Destructure the ID from the result: `const [{ id }] = result`
- Return success message with the new book's ID

### Delete Book by ID

```javascript
const deleteBookById = async (req, res) => {
  await db
    .delete(booksTable)
    .where(eq(booksTable.id, req.params.id));

  return res.json({ message: "Book deleted successfully" });
};
```

**Explanation:**
- `db.delete(booksTable)` initiates a DELETE query
- `.where()` clause is **critical** - without it, ALL records would be deleted
- `eq(booksTable.id, req.params.id)` ensures only the specified book is deleted
- Must always include a WHERE clause when deleting to prevent accidental data loss
- Returns a success message after deletion

## Asynchronous Operations

Database operations are asynchronous because:
1. The database typically runs on a different server
2. Network communication takes time (latency)
3. Database processing takes time
4. You cannot block the application waiting for a response

Therefore:
- Always use `async/await` with database operations
- Always mark controller functions as `async`
- Always `await` database queries

## Error Handling Considerations

The current implementation is basic. For production:
- Add input validation
- Add error handling for database failures
- Add try-catch blocks
- Validate data types
- Sanitize user inputs

## Testing Functionality

### Database Connection Verification

Before testing, ensure:
1. Docker PostgreSQL container is running: `docker compose up -d`
2. Application server is running: `npm start`
3. Tables are created in the database

### Testing with Tools

You can test the API endpoints using:
- Thunderclient VS Code extension
- Postman application
- Direct database queries using Drizzle Studio

### Alternative: Direct Database Creation

If you cannot test without author routes:
1. Create authors directly in the database using Drizzle Studio
2. Note their IDs
3. Use those IDs when creating books through the API

## Summary of Query Operations

| Operation | Method | Syntax |
|-----------|--------|--------|
| Select All | SELECT | `db.select().from(table)` |
| Select with Condition | SELECT WHERE | `db.select().from(table).where(eq(...))` |
| Insert | INSERT | `db.insert(table).values({...}).returning(...)` |
| Delete | DELETE | `db.delete(table).where(eq(...))` |

## Key Concepts Recap

1. **Asynchronous Operations:** All database queries return promises
2. **WHERE Clauses:** Essential for filtering and preventing accidental mass operations
3. **Returning Values:** Use `.returning()` to get generated IDs after insertion
4. **Error Handling:** Basic checks like existence validation are necessary
5. **Database Constraints:** The database enforces data integrity

## Remaining Tasks

To fully test the bookstore application:
1. Implement author CRUD operations (GET all, GET by ID, CREATE, DELETE)
2. Implement the POST endpoint to create books
3. Test all endpoints with Postman or similar tool
4. Handle any bugs that appear during testing

## Production Considerations

For a production-ready application:
- Add comprehensive error handling
- Add input validation and sanitization
- Add authentication and authorization
- Add logging and monitoring
- Add database transaction support
- Add proper HTTP status codes
- Add request/response validation
- Document all API endpoints