# Migrating Book Store to Drizzle ORM: Part 1 - Schema Setup

## Overview of the Task

The goal of this migration is to transform the bookstore application from using an in-memory database to using a real PostgreSQL database through Drizzle ORM.

### Current Problem with In-Memory Database

The existing bookstore application has the following structure:
- Multiple routes for handling book operations
- Several controllers managing business logic
- **Critical Issue:** Uses an in-memory database
- Data is stored in arrays that exist only in memory
- **Data Loss:** When the server restarts, all data is lost
- No data persistence

### Solution: Use Real Database

By migrating to a real database (PostgreSQL with Drizzle ORM):
- Data persists across server restarts
- Data is stored permanently in the database
- Professional-grade data management

## Setting Up Docker Compose for PostgreSQL

Create a `docker-compose.yml` file in your project root:

```yaml
version: "3"

services:
  postgres:
    image: postgres:17.4
    ports:
      - "5432:5432"
    environment:
      POSTGRES_DB: bookstore
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: admin
```

### Configuration Details:

**Image:** `postgres:17.4`
- Uses PostgreSQL version 17.4 (the latest at the time of this lecture)

**Port Mapping:** `"5432:5432"`
- Maps the internal PostgreSQL port to your local machine's port
- Both are set to 5432 (the standard PostgreSQL port)
- Note: Ensure no other containers are using port 5432 on your machine

**Database Name:** `bookstore`
- The default database created will be named "bookstore"

**Credentials:**
- Username: `postgres`
- Password: `admin`

## Installing Required Dependencies

Install the necessary npm packages:

```bash
npm install dotenv drizzle-orm pg drizzle-kit
```

### Package Details:

- **dotenv:** For loading environment variables from .env file
- **drizzle-orm:** The ORM for database operations
- **pg:** PostgreSQL driver for Node.js
- **drizzle-kit:** Tools for schema management and migrations

## Environment Configuration

Create a `.env` file in your project root:

```
DATABASE_URL=postgresql://postgres:admin@localhost:5432/bookstore
```

This connection string consists of:
- Protocol: `postgresql://`
- Username: `postgres`
- Password: `admin`
- Host: `localhost`
- Port: `5432`
- Database Name: `bookstore`

## Project Structure Setup

Create the following directory structure:

```
project/
├── drizzle/
│   ├── db/
│   │   ├── index.js
│   │   └── schema.js
│   └── ... (other files)
├── .env
├── package.json
└── ... (other application files)
```

## Creating the Database Connection

Create `drizzle/db/index.js`:

```javascript
require("dotenv").config();
const { drizzle } = require("drizzle-orm/node-postgres");

const db = drizzle(process.env.DATABASE_URL);

module.exports = db;
```

This file:
- Loads environment variables
- Imports the drizzle function
- Creates a database connection using the DATABASE_URL from environment variables
- Exports the connection for use throughout the application

## Designing the Schema with Relationships

### Entity Relationship Analysis

The bookstore application needs to manage books and authors. These entities have a relationship:

**Author Entity:**
- Every author has:
  - Unique ID
  - Name (first name and/or last name)
  - Email
  - Age (optional)
  - Profile image URL (optional)
  - Other biographical information

**Book Entity:**
- Every book has:
  - Unique ID
  - Title
  - Description
  - Author ID (foreign key linking to the author)

### Relationship Type: One-to-Many

One author can write multiple books. This creates a one-to-many relationship:
- One author → Many books
- Multiple books → One author per book

### Implementing the Relationship

To establish this relationship:
1. Each book stores the author's ID (called a foreign key)
2. If you try to enter an author ID that doesn't exist in the authors table, the database rejects it
3. This ensures data integrity and prevents orphaned books

### Querying with Relationships

With this structure, you can:
- Query all books by a specific author: "Give me all books where author_id equals X"
- Query a book and get the author details: "Get this book and fetch its author's information"

## Creating the Book Model

Create `drizzle/db/schema.js` to define your models:

### Importing Data Types

```javascript
const { pgTable, uuid, text, varchar } = require("drizzle-orm/pg-core");
const { v4 } = require("uuid");
```

### Book Table Definition

```javascript
const booksTable = pgTable("books", {
  id: uuid("id").primaryKey().defaultValue(v4()),
  title: varchar("title", { length: 255 }).notNull(),
  description: text("description"),
  authorId: uuid("author_id").notNull()
});

module.exports = { booksTable };
```

### Column Definitions:

**id: uuid("id").primaryKey().defaultValue(v4())**
- Creates a UUID (universally unique identifier) column
- Serves as the primary key (unique identifier for each book)
- Uses a default value generator (v4 creates random UUIDs)
- UUID is more secure and scalable than auto-incrementing integers

**title: varchar("title", { length: 255 }).notNull()**
- Variable-length string, maximum 255 characters
- Required field (cannot be null)
- Every book must have a title

**description: text("description")**
- Text field for longer descriptions
- Optional field (can be null)
- No length restriction specified

**authorId: uuid("author_id").notNull()**
- UUID column storing the author's ID
- Required field (cannot be null)
- Links this book to an author
- Will later add a foreign key constraint

## Creating the Author Model

Similarly, create the authors table:

```javascript
const authorsTable = pgTable("authors", {
  id: uuid("id").primaryKey().defaultValue(v4()),
  firstName: varchar("first_name", { length: 255 }).notNull(),
  lastName: varchar("last_name", { length: 255 }),
  email: varchar("email", { length: 255 }).notNull().unique()
});

module.exports = { authorsTable, booksTable };
```

### Column Definitions:

**id: uuid("id").primaryKey().defaultValue(v4())**
- UUID primary key with auto-generated values

**firstName: varchar("first_name", { length: 255 }).notNull()**
- Author's first name
- Required field
- Maximum 255 characters

**lastName: varchar("last_name", { length: 255 })**
- Author's last name
- Optional field

**email: varchar("email", { length: 255 }).notNull().unique()**
- Author's email address
- Required field
- Must be unique (no duplicate emails in the database)

## Data Integrity and Constraints

Key constraints to implement:

1. **Primary Key (id):** Ensures each record is uniquely identifiable
2. **Not Null Constraints:** Ensures required fields always have values
3. **Unique Constraints:** Ensures no duplicate emails for authors
4. **Foreign Key:** Links books to authors (ensures books reference valid authors)

## Next Steps

After creating the schema:
1. Set up migrations to push the schema to the database
2. Create controllers for CRUD operations
3. Implement routes for book and author management
4. Test the functionality with the real database

## Benefits of This Approach

- **Data Persistence:** Data survives server restarts
- **Scalability:** Real databases handle large volumes of data efficiently
- **Integrity:** Database constraints prevent invalid data
- **Relationships:** Foreign keys maintain data consistency across tables
- **Professional:** Aligns with industry-standard practices