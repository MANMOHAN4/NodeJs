# Using Indexes in PostgreSQL to Speed Up Queries

## Introduction to Query Optimization

In this lesson, we implement a search functionality that demonstrates the importance of database indexes for query performance.

## Implementing Search Functionality

### Requirement: Search Books by Title

The task is to create a route that allows users to search for books by title. Instead of creating a separate route, we can use query parameters.

### Query Parameters

Query parameters are additional parameters passed in the URL without creating new routes.

**URL Format:**
```
http://localhost:8000/books?search=node
```

- The `?` indicates the start of query parameters
- `search=node` is the parameter
- The key is `search`
- The value is `node`

### Accessing Query Parameters in Express

In an Express route handler, access query parameters using `req.query`:

```javascript
const search = req.query.search;
```

### Testing Query Parameters

Test with and without the search parameter:

**Without search parameter:**
```
http://localhost:8000/books
```
- Returns all books
- `req.query.search` is `undefined`

**With search parameter:**
```
http://localhost:8000/books?search=node
```
- Should return only books containing "node" in the title
- `req.query.search` is `"node"`

## Implementing Search Logic

### SQL LIKE Operator with Case Insensitivity

Use the `ILIKE` operator in PostgreSQL for case-insensitive pattern matching:

```javascript
const getAllBooks = async (req, res) => {
  const { search } = req.query;

  if (search) {
    const books = await db
      .select()
      .from(booksTable)
      .where(ilike(booksTable.title, `%${search}%`));
    return res.json(books);
  }

  const books = await db.select().from(booksTable);
  return res.json(books);
};
```

### ILIKE Operator Explanation

**ILIKE:** Case-insensitive LIKE operator
- `ILIKE` matches patterns regardless of uppercase or lowercase
- "Node" and "node" are treated the same

**Pattern Syntax:**
- `%${search}%` creates a pattern with wildcards on both sides
- `%` is a wildcard that matches any characters
- This pattern means: "anything followed by the search term followed by anything"

**Examples:**
- Search for "node" matches:
  - "node" (exact match)
  - "Node" (case-insensitive)
  - "Node.js" (contains "node")
  - "Learning Node" (contains "node")
  - "Advanced NodeJS Book" (contains "node")

### Implementation Details

**With Search:**
```javascript
const books = await db
  .select()
  .from(booksTable)
  .where(ilike(booksTable.title, `%${search}%`));
```

**Without Search:**
```javascript
const books = await db.select().from(booksTable);
```

The difference:
- With search: Adds a `.where()` clause to filter results
- Without search: Returns all books

## Performance Considerations

### The Problem: Slow Searches

When you execute:
```sql
SELECT * FROM books WHERE title ILIKE '%node%'
```

PostgreSQL must:
1. Scan every single row in the books table
2. Check if the title matches the pattern
3. Return matching rows

This is a **full table scan**. With millions of records, this becomes very slow.

### Solution: Database Indexes

An **index** is a data structure that speeds up data retrieval. Instead of scanning every row, the database can:
1. Use the index to find matching rows quickly
2. Return results in milliseconds instead of seconds

### Creating an Index on Title

Before executing searches, create an index:

```sql
CREATE INDEX idx_books_title ON books(title);
```

**How it Works:**
- The database creates a sorted structure of all titles
- When you search, instead of checking every row, it uses this sorted structure
- Much faster retrieval, similar to using an index in a book instead of reading every page

## When to Use Indexes

### Use Indexes For:
- Columns frequently used in WHERE clauses
- Columns used in search operations
- Columns used in JOIN conditions
- Columns that are frequently filtered

### Don't Overuse Indexes:
- Too many indexes slow down INSERT and UPDATE operations
- Each index requires additional storage
- Indexes must be updated every time data changes
- Trade-off between read performance and write performance

## Search Implementation Complete

The final implementation:
1. Accepts a search query parameter
2. If search is provided, filters results using ILIKE
3. If no search, returns all books
4. Uses indexes for performance optimization

### Complete Handler

```javascript
const getAllBooks = async (req, res) => {
  const { search } = req.query;

  if (search) {
    const books = await db
      .select()
      .from(booksTable)
      .where(ilike(booksTable.title, `%${search}%`));
    return res.json(books);
  }

  const books = await db.select().from(booksTable);
  return res.json(books);
};
```

## Testing Search Functionality

### Test Cases

**Test 1: Search with results**
```
GET /books?search=node
```
Returns only books with "node" in the title

**Test 2: Search with no results**
```
GET /books?search=xyz
```
Returns an empty array

**Test 3: No search parameter**
```
GET /books
```
Returns all books

**Test 4: Case-insensitive search**
```
GET /books?search=NODE
GET /books?search=Node
GET /books?search=node
```
All three return the same results (case-insensitive)

## Query Parameter Best Practices

1. **Use lowercase in URLs:** `?search=` not `?Search=`
2. **URL encode special characters:** Spaces become `%20`, etc.
3. **Validate query parameters:** Check for null/undefined
4. **Use meaningful names:** `?search=` is clearer than `?q=`

## Index Recommendations

For the bookstore application:
- Index on `books.title` (for search functionality)
- Index on `books.authorId` (for foreign key lookups)
- Index on `authors.email` (if searching by email)

## Performance Monitoring

In PostgreSQL, you can analyze query performance:
```sql
EXPLAIN ANALYZE SELECT * FROM books WHERE title ILIKE '%node%';
```

This shows:
- Whether the index is being used
- Query execution time
- Number of rows scanned

## Summary

Search functionality is implemented by:
1. Accepting query parameters (`?search=value`)
2. Using SQL ILIKE for case-insensitive pattern matching
3. Adding wildcards (`%`) for flexible matching
4. Creating indexes for performance optimization

The combination of ILIKE pattern matching and database indexes provides fast, case-insensitive search capabilities suitable for production applications.