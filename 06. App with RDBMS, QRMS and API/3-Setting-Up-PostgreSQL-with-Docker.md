# Setting Up PostgreSQL with Docker for Local Development

## PostgreSQL Setup: Three Approaches

To set up PostgreSQL for development, there are three main mechanisms available:

### Approach 1: Full Local Download

This is the traditional method of installing PostgreSQL directly on your machine.

**Steps:**
- Visit the official PostgreSQL website
- Click on the download button
- Choose the appropriate download for your operating system (Linux, macOS, Windows, BSD, Solaris)
- Install PostgreSQL locally on your machine

This approach gives you full control over the installation process.

### Approach 2: Cloud-Hosted PostgreSQL

Instead of downloading PostgreSQL on your local machine, you can use a cloud-hosted PostgreSQL service.

**How it Works:**
- PostgreSQL is installed and running on a remote server (for example, on AWS)
- You use your local machine to connect to this remote database
- The connection is made over the internet

**Required Information:**
Three things are required to connect:
- URL: The address of the remote server
- Username: Credentials to access the database
- Password: Authentication credentials

**Advantages:**
- No need to install anything locally
- Can spin up a PostgreSQL database with one click using cloud services like Supabase
- Easy to set up and manage

**Disadvantages:**
- Incurs cloud service costs
- Network latency: Since the database is on a remote server, there is physical distance between your server and the database
- Application performance suffers because every database operation requires a round trip to the remote server
- Slower execution compared to local databases

### Approach 3: Docker (Hybrid Approach)

Docker is a containerization technology that provides a hybrid approach combining the best of both worlds.

**Advantages over Full Download:**
- When you fully download PostgreSQL, you typically have only one database instance
- With Docker, you can run multiple isolated PostgreSQL instances on your machine
- Each instance runs in a separate container

**Advantages over Cloud-Hosted:**
- No latency issues - the database runs locally
- No cloud service costs
- Full performance benefits
- Easy to manage multiple database instances

**Advantages over Full Download:**
- More flexible
- Can run multiple versions simultaneously
- Easy to reset or delete and start fresh
- Containerized environment

## Prerequisites

Before setting up PostgreSQL with Docker, ensure that:
- You don't have any existing containers running on port 5432 (the default PostgreSQL port)
- If you have an existing container using this port, you can either:
  - Stop the existing container
  - Change the port mapping to a different port (e.g., 5431)

**Note:** It is recommended to stop existing containers and use the standard port 5432 for easier configuration.

## Docker Setup for PostgreSQL

### Docker Compose File

Create a file named `docker-compose.yml`:

```yaml
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

### Explanation:

**Image:**
- `postgres:17.4` specifies the PostgreSQL version to use (version 17.4 at the time of this lecture)

**Port Mapping:**
- `5432:5432` maps the PostgreSQL port
- The first 5432 is the internal PostgreSQL port (cannot be changed)
- The second 5432 is the port available on your local machine (can be customized)

**Environment Variables:**

These are required for PostgreSQL initialization:

- **POSTGRES_DB**: `bookstore` - This is the name of the default database that will be created
- **POSTGRES_USER**: `postgres` - The username for connecting to the database
- **POSTGRES_PASSWORD**: `admin` - The password for the user

These are the same three pieces of information required whether you use a full download, cloud-hosted, or Docker approach:
- URL (localhost:5432 for local Docker)
- Username (postgres)
- Password (admin)

## Connection String

Once Docker is set up, you connect to PostgreSQL using a connection string:

```
postgresql://postgres:admin@localhost:5432/bookstore
```

This breaks down as:
- `postgresql://` - Protocol
- `postgres` - Username
- `admin` - Password
- `localhost` - Server address
- `5432` - Port number
- `bookstore` - Database name

## Running Docker

To start the PostgreSQL container:

```bash
docker compose up
```

To run it in the background:

```bash
docker compose up -d
```

## Data Persistence

A key advantage of Docker is that even when you restart your machine:
- Your data remains stored in the PostgreSQL database
- You don't lose any information because you're using a real database
- The data persists between sessions

## Summary

Docker provides the optimal solution for local PostgreSQL development by offering:
- The convenience and performance of a local installation
- The flexibility of multiple database instances
- Easy management and reset capabilities
- No cloud costs or latency issues
- Professional development practices aligned with industry standards