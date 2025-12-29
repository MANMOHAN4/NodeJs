# Installing and Configuring Drizzle ORM with PostgreSQL

## Why Choose Drizzle Over Prisma

When selecting an ORM, it's a critical decision because changing ORMs after significant development is difficult and requires rewriting the entire service layer. Both Prisma and Drizzle are popular options, but they have different strengths.

### Prisma Characteristics

**Advantages:**
- Very flexible and compatible with multiple databases
- Works with MongoDB, MySQL, and PostgreSQL
- You can switch databases easily with minimal code changes
- Unified interface across different database types

**Disadvantages:**
- Performance issues with foreign key relation joins
- When querying a user with all their friends, Prisma makes two separate queries to the database
- Less efficient when handling complex relationships

### Drizzle Characteristics

**Advantages:**
- Built on top of Prisma internally but with optimizations
- More efficient querying system with PostgreSQL and MySQL
- Executes foreign key joins in a single database connection
- Better performance for relational queries

**Disadvantages:**
- Does not support NoSQL databases like MongoDB
- Only works with SQL databases

**Recommendation:** For projects using PostgreSQL or MySQL, Drizzle is the better choice because it provides superior performance when dealing with relational data and foreign key joins.

## Drizzle Database Support

Drizzle supports multiple SQL databases:
- PostgreSQL
- MySQL
- SQLite
- PlanetScale
- Turso
- D1
- And more SQL databases

## Project Directory Structure

Before setting up Drizzle, establish a proper directory structure:

```
project-root/
├── drizzle/
│   ├── db/
│   │   ├── index.js
│   │   └── schema.js
│   ├── migrations/
│   └── drizzle.config.js
├── package.json
├── .env
└── other files
```

## Step 1: Create the Drizzle Folder

Create a folder named `drizzle` in your project root. This folder will contain:
- All configuration files
- All schemas
- All project structure

## Step 2: Create drizzle.config.js

In the root of your project, create a file named `drizzle.config.js`. This file contains the configuration for Drizzle.

### Configuration File Content

```javascript
const { defineConfig } = require("drizzle-kit");

module.exports = defineConfig({
  dialect: "postgresql",
  schema: "./drizzle/db/schema.js",
  out: "./drizzle/migrations",
  dbCredentials: {
    url: process.env.DATABASE_URL
  }
});
```

### Configuration Explanation:

**dialect:** Specifies the database type
- Set to `"postgresql"` for PostgreSQL
- Other options: `"mysql"`, `"sqlite"`, etc.

**schema:** Path to the schema file
- Points to where you define your database tables
- Example: `"./drizzle/db/schema.js"`

**out:** Output directory for migrations
- Drizzle generates migration files here
- Example: `"./drizzle/migrations"`

**dbCredentials:** Database connection credentials
- Contains the database URL
- The URL is read from environment variables (`process.env.DATABASE_URL`)

## Step 3: Create Environment Variables

Create a `.env` file in your project root:

```
DATABASE_URL=postgresql://postgres:admin@localhost:5432/bookstore
```

This connects to the PostgreSQL database set up with Docker.

## Step 4: Install Required Packages

```bash
npm install drizzle-orm pg dotenv
npm install -D drizzle-kit
```

### Package Explanations:

**drizzle-orm:**
- The main ORM library for querying and interacting with the database
- Used for sending and receiving data from the database

**pg:**
- PostgreSQL driver for Node.js
- Required by Drizzle to connect to PostgreSQL

**dotenv:**
- Loads environment variables from the .env file

**drizzle-kit:** (development dependency)
- Provides additional CLI tools and utilities
- Different from drizzle-orm
- Used for schema management and migrations
- Not required in production

### Difference Between drizzle-orm and drizzle-kit:

**drizzle-orm:**
- Handles data operations (CRUD operations)
- Executes queries and manages data flow

**drizzle-kit:**
- Provides tooling and utilities beyond data operations
- Includes schema generation and UI for database management
- Includes migration tools
- Extra features not directly related to data querying

## Step 5: Create Database Connection

Create `drizzle/db/index.js`:

```javascript
require("dotenv").config();
const { drizzle } = require("drizzle-orm/node-postgres");
const pg = require("pg");

const client = new pg.Client({
  connectionString: process.env.DATABASE_URL
});

client.connect();

const db = drizzle(client);

module.exports = db;
```

### Explanation:

1. Load environment variables using dotenv
2. Import the drizzle function from the PostgreSQL driver
3. Create a PostgreSQL client with the database URL from environment variables
4. Connect the client to the database
5. Create a drizzle instance wrapping the client
6. Export the drizzle instance for use in your application

## Step 6: Define Database Schema

Create `drizzle/db/schema.js` to define your database tables.

### Example User Table Schema:

```javascript
const { pgTable, serial, varchar, integer } = require("drizzle-orm/pg-core");

const users = pgTable("users", {
  id: serial("id").primaryKey(),
  name: varchar("name", { length: 255 }).notNull(),
  email: varchar("email", { length: 255 }).notNull().unique(),
  age: integer("age")
});

module.exports = { users };
```

### Column Definition Explanation:

**id: serial("id").primaryKey()**
- Creates an auto-incrementing integer column
- Serves as the unique identifier for each user
- Automatically increments with each new record

**name: varchar("name", { length: 255 }).notNull()**
- Creates a variable-length string column (up to 255 characters)
- The column name in the database is "name"
- `.notNull()` means this field is required

**email: varchar("email", { length: 255 }).notNull().unique()**
- Variable-length string column (up to 255 characters)
- `.notNull()` means email is required
- `.unique()` ensures no duplicate emails in the database

**age: integer("age")**
- Creates an integer column (no constraints)
- Optional field (can be null)

## Data Types Available

Drizzle provides various data types:
- `serial()` - Auto-incrementing integer
- `integer()` - Integer values
- `varchar()` - Variable-length strings
- `text()` - Large text
- `boolean()` - True/False values
- `date()` - Date values
- `timestamp()` - Date and time values
- And many more

## Using the Database Connection

In your application files (controllers, routes):

```javascript
const db = require("./drizzle/db");
const { users } = require("./drizzle/db/schema");

// Get all users
const allUsers = await db.select().from(users);

// Get a specific user
const user = await db.select().from(users).where(/* condition */);

// Insert a new user
await db.insert(users).values({ name: "John", email: "john@example.com" });

// Update a user
await db.update(users).set({ age: 30 }).where(/* condition */);

// Delete a user
await db.delete(users).where(/* condition */);
```

## Best Practices

As a professional recommendation, **always read the documentation**. This prevents unwanted bugs and ensures you're using the library optimally. Drizzle's documentation is comprehensive and provides guidance on all features and edge cases.

## Summary

Setting up Drizzle involves:
1. Creating the project structure with a `drizzle` folder
2. Installing required packages (drizzle-orm, pg, dotenv)
3. Creating environment variables with the database URL
4. Creating a database connection file
5. Defining your schema with tables and columns
6. Configuring drizzle.config.js with proper settings

Once set up, Drizzle provides a type-safe, efficient way to interact with PostgreSQL and MySQL databases from your Node.js application.