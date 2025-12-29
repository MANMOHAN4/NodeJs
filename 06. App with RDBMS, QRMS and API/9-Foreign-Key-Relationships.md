# Adding Foreign Key Relationships: Authors and Books

## Overview

In this lesson, we implement author management with full CRUD operations and establish foreign key relationships between books and authors.

## Creating Author Routes

Create a file named `author.routes.js`:

```javascript
const express = require("express");
const { eq } = require("drizzle-orm");
const db = require("../drizzle/db");
const { authorsTable } = require("../drizzle/db/schema");

const router = express.Router();

module.exports = router;
```

This sets up the basic structure for author routes.

## Implementing Author Controller Functions

### Get All Authors

```javascript
router.get("/", async (req, res) => {
  const authors = await db.select().from(authorsTable);
  return res.json(authors);
});
```

**Implementation:**
- Simple SELECT query from the authors table
- Returns an array of all authors
- Same pattern as getting all books

### Get Author by ID

```javascript
router.get("/:id", async (req, res) => {
  const [author] = await db
    .select()
    .from(authorsTable)
    .where(eq(authorsTable.id, req.params.id));

  if (!author) {
    return res.status(404).json({
      error: `Author with ID ${req.params.id} does not exist`
    });
  }

  return res.json(author);
});
```

**Implementation:**
- Uses WHERE clause to filter by ID
- Destructures the first element from the array
- Returns 404 error if author not found
- Returns author details if found

### Create Author

```javascript
router.post("/", async (req, res) => {
  const { firstName, lastName, email } = req.body;

  const result = await db
    .insert(authorsTable)
    .values({
      firstName: firstName,
      lastName: lastName,
      email: email
    })
    .returning(authorsTable.id);

  const [{ id }] = result;

  return res.json({
    message: "Author created successfully",
    id: id
  });
});
```

**Implementation:**
- Destructures required fields from request body
- `firstName` is required
- `lastName` is optional
- `email` is required
- Insert operation returns the generated ID
- Returns success message with new author ID

### Delete Author

```javascript
router.delete("/:id", async (req, res) => {
  // Delete the author
  await db
    .delete(authorsTable)
    .where(eq(authorsTable.id, req.params.id));

  return res.json({ message: "Author deleted successfully" });
});
```

**Important Consideration:**
The simple delete operation shown above has a critical issue: **Deleting an author with books creates orphaned books** (books without valid authors).

## Handling Foreign Key Relationships

### The Problem: Orphaned Records

When you delete an author who has written books:
- Books remain in the database with now-invalid `authorId`
- This violates data integrity

### Solutions

#### Solution 1: Cascade Delete
```sql
ALTER TABLE books ADD CONSTRAINT fk_author_id 
FOREIGN KEY (author_id) REFERENCES authors(id) 
ON DELETE CASCADE;
```

- Automatically deletes all books when the author is deleted
- Drastic approach - loses data

#### Solution 2: Prevent Delete
```javascript
router.delete("/:id", async (req, res) => {
  // Check if author has books
  const books = await db
    .select()
    .from(booksTable)
    .where(eq(booksTable.authorId, req.params.id));

  if (books.length > 0) {
    return res.status(400).json({
      error: "Cannot delete author with existing books"
    });
  }

  await db
    .delete(authorsTable)
    .where(eq(authorsTable.id, req.params.id));

  return res.json({ message: "Author deleted successfully" });
});
```

- Checks if author has any books
- Returns error if books exist
- Only allows deletion if no books associated
- Preserves data integrity

#### Solution 3: Set to NULL
```sql
ALTER TABLE books ADD CONSTRAINT fk_author_id 
FOREIGN KEY (author_id) REFERENCES authors(id) 
ON DELETE SET NULL;
```

- Books become unassigned (null author)
- Useful when author information isn't critical

## Setting Up the Routes in Main Application

In your main `index.js` or `server.js`:

```javascript
const authorRoutes = require("./routes/author.routes");

app.use("/authors", authorRoutes);
```

This makes author routes available at:
- GET `/authors` - Get all authors
- GET `/authors/:id` - Get specific author
- POST `/authors` - Create author
- DELETE `/authors/:id` - Delete author

## Complete Author Routes File

```javascript
const express = require("express");
const { eq } = require("drizzle-orm");
const db = require("../drizzle/db");
const { authorsTable, booksTable } = require("../drizzle/db/schema");

const router = express.Router();

// Get all authors
router.get("/", async (req, res) => {
  const authors = await db.select().from(authorsTable);
  return res.json(authors);
});

// Get author by ID
router.get("/:id", async (req, res) => {
  const [author] = await db
    .select()
    .from(authorsTable)
    .where(eq(authorsTable.id, req.params.id));

  if (!author) {
    return res.status(404).json({
      error: `Author with ID ${req.params.id} does not exist`
    });
  }

  return res.json(author);
});

// Create author
router.post("/", async (req, res) => {
  const { firstName, lastName, email } = req.body;

  const result = await db
    .insert(authorsTable)
    .values({
      firstName: firstName,
      lastName: lastName,
      email: email
    })
    .returning(authorsTable.id);

  const [{ id }] = result;

  return res.json({
    message: "Author created successfully",
    id: id
  });
});

// Delete author
router.delete("/:id", async (req, res) => {
  // Check if author has books
  const authorBooks = await db
    .select()
    .from(booksTable)
    .where(eq(booksTable.authorId, req.params.id));

  if (authorBooks.length > 0) {
    return res.status(400).json({
      error: "Cannot delete author with existing books"
    });
  }

  await db
    .delete(authorsTable)
    .where(eq(authorsTable.id, req.params.id));

  return res.json({ message: "Author deleted successfully" });
});

module.exports = router;
```

## Testing Author Routes

### Workflow

1. **Create an author first** (POST /authors)
   - Required: firstName, email
   - Optional: lastName

2. **Get all authors** (GET /authors)
   - Verify author was created

3. **Create books** (POST /books)
   - Use the author ID from step 1
   - Now books are linked to the author

4. **Try to delete author** (DELETE /authors/:id)
   - Should fail if books exist
   - Should succeed if no books exist

## Foreign Key Importance

Foreign keys ensure:
- **Referential Integrity:** Books can only reference valid authors
- **Data Consistency:** No orphaned records
- **Relationship Validation:** Database enforces relationship rules

## Summary

Author management implementation includes:
1. GET endpoint for retrieving all authors
2. GET endpoint for retrieving specific author by ID
3. POST endpoint for creating new authors
4. DELETE endpoint with data integrity checks
5. Foreign key relationship handling between books and authors

The key lesson is that when dealing with relationships, you must consider:
- Data integrity
- Preventing orphaned records
- Proper error handling and user feedback
- Business logic rules for deletion