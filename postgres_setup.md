# Setting Up PostgreSQL for Galaxy

This guide helps you install PostgreSQL and configure a new database and user for running Galaxy.

---

## 1. Install PostgreSQL

If PostgreSQL is not already installed, use the appropriate command for your operating system:

### On Ubuntu / Debian:

```bash
sudo apt update
sudo apt install postgresql postgresql-contrib
```
---

##  2. Create a Galaxy Database and User

1. **Enter PostgreSQL prompt** as the `postgres` superuser:

```bash
sudo -u postgres psql
```

2. **Run the following SQL commands inside the `psql` prompt**:

```sql
-- 1. Create the user with a password
CREATE ROLE galaxydbuser WITH LOGIN PASSWORD 'admin';

-- 2. Grant privileges to create databases (optional for Galaxy)
ALTER ROLE galaxydbuser WITH CREATEDB;

-- 3. Create the database owned by the user
CREATE DATABASE galaxy_database OWNER galaxydbuser;

-- 4. Exit the psql prompt
\q
```

---

##  3. Configure Galaxy to Use the Database

In your `galaxy.yml` configuration file, add or modify the following line:

```yaml
database_connection: postgresql://galaxydbuser:admin@localhost/galaxy_database
```

> **Note:** Make sure this line is not commented out (no `#` at the beginning), and replace credentials as needed.
