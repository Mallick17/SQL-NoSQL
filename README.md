# SQL (Structured Query Language)
- SQL is a standard language for storing, manipulating and retrieving data in databases.

# NoSQL (Not Only SQL)
- NoSQL stands for "Not Only SQL" and refers to a category of non-relational databases that offer flexibility and scalability for storing and managing data.

## SQL vs. NoSQL

| **Aspect**                | **SQL (Relational Databases)**                                                                 | **NoSQL (Non-relational Databases)**                                                   |
|---------------------------|-----------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|
| **Definition**            | Structured databases using tables with predefined schemas to store data.                      | Flexible databases that store data in various formats without rigid schemas.           |
| **Data Model**            | Tables with rows and columns, based on relational algebra.                                    | Key-value, document, column-family, or graph-based models.                             |
| **Schema**                | Fixed, predefined schema; changes require altering the table structure.                       | Dynamic schema; fields can be added or modified on the fly.                            |
| **Query Language**        | Uses SQL (Structured Query Language) for querying, e.g., SELECT, INSERT, UPDATE.              | Varies by type; uses APIs or query languages like MongoDB Query Language, CQL, etc.    |
| **Scalability**           | Vertical scaling (adding more power to a single server).                                      | Horizontal scaling (adding more servers, sharding, or clustering).                     |
| **Performance**           | Optimized for complex queries and structured data; may struggle with massive datasets.         | High performance for large-scale, unstructured data and high read/write throughput.    |
| **Data Structure**        | Structured data with fixed fields; enforces data integrity via constraints.                   | Handles unstructured, semi-structured, or structured data with flexibility.            |
| **Consistency**           | Strong consistency (ACID transactions: Atomicity, Consistency, Isolation, Durability).        | Eventual consistency (BASE model: Basically Available, Soft state, Eventual consistency).|
| **Joins**                 | Supports complex joins across multiple tables for relational queries.                         | Limited or no support for joins; data is often denormalized or aggregated upfront.     |
| **Use Cases**             | Financial systems, ERP, CRM, applications requiring complex queries and transactions.          | Big data, real-time analytics, IoT, content management, social media platforms.        |
| **Examples**              | MySQL, PostgreSQL, Oracle, SQL Server.                                                       | MongoDB, Cassandra, DynamoDB, Redis, Neo4j.                                            |
| **Data Integrity**        | Enforces strict integrity through primary keys, foreign keys, and constraints.                | Relies on application logic for data integrity; less rigid enforcement.                |
| **Flexibility**           | Less flexible due to rigid schema; changes can be complex and time-consuming.                 | Highly flexible; accommodates evolving data structures easily.                         |
| **Transaction Support**   | Strong support for multi-row transactions with rollback capabilities.                         | Limited transaction support; varies by database (e.g., MongoDB supports transactions).  |
| **Ease of Use**           | Requires knowledge of SQL and schema design; steeper learning curve for complex systems.       | Easier to start with due to schema-less design, but query complexity may vary.         |
| **Data Volume**           | Suitable for moderate to large datasets with structured data.                                 | Ideal for massive datasets, especially unstructured or semi-structured data.          |
| **Community & Maturity**  | Mature, with decades of development and widespread adoption.                                  | Relatively newer but rapidly growing with strong community support.                    |


### Key Concepts for Understanding
1. **SQL Databases**:
   - Best for structured data with clear relationships (e.g., customer-order data).
   - Use a fixed schema, making them ideal for applications where data integrity and consistency are critical (e.g., banking systems).
   - Rely on SQL for querying, which is standardized and widely understood.
   - Scaling vertically can be expensive as it requires more powerful hardware.

2. **NoSQL Databases**:
   - Designed for flexibility, handling diverse data types (e.g., JSON, XML, graphs).
   - Schema-less design allows rapid development and adaptation to changing requirements.
   - Scales horizontally, making it cost-effective for large-scale, distributed systems.
   - Trade-offs include eventual consistency, which may not suit applications needing immediate data accuracy.

### Choosing Between SQL and NoSQL
- **Use SQL** when you need strong consistency, complex joins, or structured data (e.g., accounting software).
- **Use NoSQL** for high scalability, flexible data models, or real-time processing of large, unstructured datasets (e.g., social media analytics).


---

# MySQL Dummy Data Generator using Python in `.csv` 

## Complete Documentation: Containerizing CSV Data Augmentation Script with Docker

<details>
    <summary>Click to view the complete documentation</summary>

***

## Project Overview

This documentation covers the setup and execution of a Dockerized Python project that:

- Reads an existing CSV file (`MOCK_DATA.csv`).
- Adds 10,000 dummy data rows with unique modifications.
- Outputs a combined CSV (`MOCK_DATA_10000_more.csv`) for local use.

The containerized approach ensures reproducibility and isolation from local environment issues.

***

## Folder Structure

The working directory `/Users/gyanaranjan.mallick/Downloads/docker_local` should contain:

- `MOCK_DATA.csv` — The original data CSV file.
- `add_dummy_data.py` — Python script to add dummy rows.
- `Dockerfile` — Docker image build instructions.

```plaintext
docker_local/
├── MOCK_DATA.csv
├── add_dummy_data.py
└── Dockerfile
```

***

## Python Script: `add_dummy_data.py`

This script uses pandas to:

- Load the original CSV.
- Sample and generate 10,000 new rows,
- Update key fields (`id`, `email`, `firstname`, `lastname`, `ipaddress`) to keep them unique.
- Save the combined dataset back to a CSV named `MOCK_DATA_10000_more.csv`.

Example script content:

```python
import pandas as pd

def add_dummy_data(input_file='MOCK_DATA.csv', output_file='MOCK_DATA_10000_more.csv', num_new_rows=10000):
    df = pd.read_csv(input_file)
    new_rows = []
    max_id = df['id'].max() if 'id' in df.columns else 0

    for i in range(num_new_rows):
        row = df.sample(n=1).iloc[0].copy()
        row['id'] = max_id + i + 1
        row['email'] = f'dummy{i}@example.com' if 'email' in df.columns else ''
        row['firstname'] = f'FirstName{i}' if 'firstname' in df.columns else ''
        row['lastname'] = f'LastName{i}' if 'lastname' in df.columns else ''
        row['ipaddress'] = f'192.168.{i // 256}.{i % 256}' if 'ipaddress' in df.columns else ''
        new_rows.append(row)

    new_df = pd.DataFrame(new_rows)
    combined_df = pd.concat([df, new_df], ignore_index=True)
    combined_df.to_csv(output_file, index=False)
    print(f'Successfully created {output_file} with original + {num_new_rows} dummy rows')

if __name__ == "__main__":
    add_dummy_data()
```

***

## Dockerfile Content

This defines the Docker image that will run the Python script:

```dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY MOCK_DATA.csv /app/
COPY add_dummy_data.py /app/

RUN pip install pandas

CMD ["python", "add_dummy_data.py"]
```

### Explanation:

- `FROM python:3.11-slim`: Uses a minimal Python 3.11 base image.
- `WORKDIR /app`: Switches working directory inside container to `/app`.
- `COPY MOCK_DATA.csv /app/` and `COPY add_dummy_data.py /app/`: Copy files into container.
- `RUN pip install pandas`: Installs pandas library.
- `CMD ...`: Runs the Python script at container start.

***

## Commands Executed

### 1. Build the Docker Image

Inside `/Users/gyanaranjan.mallick/Downloads/docker_local`, run:

```bash
docker build -t csv_dummy_data .
```

- `-t csv_dummy_data` tags the image.
- `.` sends the current folder as build context to Docker.

### 2. Run the Docker Container with Volume Mount

Mount The folder so the output file is saved locally:

```bash
docker run --rm -v /Users/gyanaranjan.mallick/Downloads/docker_local:/app csv_dummy_data
```

Details:

- `--rm` cleans up container after exit.
- `-v /local/path:/app` mounts folder from Mac into container's `/app`.
- The script writes output `MOCK_DATA_10000_more.csv` into `/app`, which syncs to The Mac.

### 3. Verify Output

Check The local folder `/Users/gyanaranjan.mallick/Downloads/docker_local` for the file

`MOCK_DATA_10000_more.csv` containing the combined data.

***

## Summary

This setup provides a reproducible way to:

- Run data augmentation inside an isolated container.
- Avoid local environment dependency issues.
- Easily share or automate data preparation.

If The original CSV or script changes, simply rebuild the image and rerun the container.

***

[1](https://stackoverflow.com/questions/61262638/how-should-i-containerize-a-python-script-which-reads-a-csv-file)
[2](https://forums.docker.com/t/how-to-create-a-docker-container-when-i-have-two-python-scripts-which-are-dependent-to-each-other/128530)
[3](https://towardsdatascience.com/build-and-run-a-docker-container-for-The-machine-learning-model-60209c2d7a7f/)
[4](https://realpython.com/python-csv/)
[5](https://dev.to/cloudforce/containerizing-python-data-processing-scripts-with-docker-a-step-by-step-guide-166)
[6](https://www.kdnuggets.com/build-The-own-simple-data-pipeline-with-python-and-docker)
[7](https://www.dataquest.io/blog/intro-to-docker-compose/)

</details>

---

# Step-by-Step Guide to Create the `Ola_cab` Table in MySQL

<details>
    <summary>Click to view the Quick Setup</summary>

# Step-by-Step Commands you can run from The Container Shell to:

1. create a database `ola_db`
2. create lookup tables (`City`, `Vendor`, `Owner`)
3. create the `Ola_cab` table with **foreign key** constraints (so MySQL enforces referential integrity)
4. seed the lookup tables with valid IDs
5. load The CSV file `/var/lib/mysql-files/MOCK_DATA_10000_more.csv` into `Ola_cab` (the CSV header is ignored)

---

## 1) Create a SQL script (copy & paste this into The container)

Paste everything between the triple backticks into a new file, for example `/tmp/ola_create_and_load.sql`:

```bash
cat > /tmp/ola_create_and_load.sql <<'SQL'
-- Create database and use it
CREATE DATABASE IF NOT EXISTS ola_db CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
USE ola_db;

-- Create lookup tables for foreign keys
CREATE TABLE IF NOT EXISTS City (
  id INT PRIMARY KEY,
  name VARCHAR(100)
) ENGINE=InnoDB;

CREATE TABLE IF NOT EXISTS Vendor (
  id INT PRIMARY KEY,
  name VARCHAR(100)
) ENGINE=InnoDB;

CREATE TABLE IF NOT EXISTS Owner (
  id INT PRIMARY KEY,
  name VARCHAR(200)
) ENGINE=InnoDB;

-- Create main Ola_cab table (types follow The provided schema).
-- Note: ensure column order below matches the CSV header order.
CREATE TABLE IF NOT EXISTS Ola_cab (
  id INT PRIMARY KEY,
  first_name VARCHAR(100),
  last_name VARCHAR(100),
  email VARCHAR(255),
  gender VARCHAR(10),
  ip_address VARCHAR(45),
  city_id INT,
  cab_type VARCHAR(50),
  vendor_id INT,
  owner_id INT,
  lease_owner_id INT,
  cab_color_id INT,
  cab_model_id INT,
  cab_segment_id INT,
  installment DECIMAL(10,2),
  purchase_from VARCHAR(255),
  color VARCHAR(50),
  model VARCHAR(100),
  engine_number VARCHAR(100),
  chassis_number VARCHAR(100),
  total_purchase_cost DECIMAL(15,2),
  total_payment DECIMAL(15,2),
  policy_number VARCHAR(100),
  company_name VARCHAR(255),
  amount DECIMAL(15,2),
  idv_value DECIMAL(15,2),
  nil_depreciation_value DECIMAL(15,2),
  cab_number VARCHAR(50),
  tally_ledger_name VARCHAR(255),
  gps_number VARCHAR(100),
  registration_number VARCHAR(100),
  is_ac BOOLEAN,
  allow_out_station BOOLEAN,
  owner_come_as_driver BOOLEAN,
  driver_id INT,
  booking_id INT,
  status VARCHAR(50),
  cab_state VARCHAR(50),
  rating DECIMAL(3,2),
  reduce_rating BOOLEAN,
  rating_reduced_at DATETIME,
  points INT,
  current_points INT,
  exit_initiated_at DATETIME,
  exit_initiated_by_id INT,
  exit_initiated_by_role VARCHAR(50),
  exited_at DATETIME,
  exited_by_id INT,
  device_model VARCHAR(100),
  os_version VARCHAR(100),
  driver_app_version VARCHAR(50),
  driver_app_version_updated_at DATETIME,
  fc_end_date DATE,
  policy_end_date DATE,
  policy_start_date DATE,
  purchase_date DATE,
  manufacturing_year YEAR,
  meter_reading INT,
  permit_end_date DATE,
  leased_vehicle BOOLEAN,
  lease_agreement_end_date DATE,
  date_of_commence DATE,
  firstname VARCHAR(100),
  lastname VARCHAR(100),
  ipaddress VARCHAR(45),
  CONSTRAINT fk_olacab_city FOREIGN KEY (city_id) REFERENCES City(id),
  CONSTRAINT fk_olacab_vendor FOREIGN KEY (vendor_id) REFERENCES Vendor(id),
  CONSTRAINT fk_olacab_owner FOREIGN KEY (owner_id) REFERENCES Owner(id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

-- Seed lookup tables using a recursive CTE (generate 1..N rows).
-- Cities 1..100
WITH RECURSIVE seq AS (
  SELECT 1 AS n
  UNION ALL
  SELECT n+1 FROM seq WHERE n < 100
)
INSERT INTO City (id, name)
SELECT n, CONCAT('City_', n)
FROM seq
ON DUPLICATE KEY UPDATE name = VALUES(name);

-- Vendors 1..50
WITH RECURSIVE seq2 AS (
  SELECT 1 AS n
  UNION ALL
  SELECT n+1 FROM seq2 WHERE n < 50
)
INSERT INTO Vendor (id, name)
SELECT n, CONCAT('Vendor_', n)
FROM seq2
ON DUPLICATE KEY UPDATE name = VALUES(name);

-- Owners 1..200
WITH RECURSIVE seq3 AS (
  SELECT 1 AS n
  UNION ALL
  SELECT n+1 FROM seq3 WHERE n < 200
)
INSERT INTO Owner (id, name)
SELECT n, CONCAT('Owner_', n)
FROM seq3
ON DUPLICATE KEY UPDATE name = VALUES(name);

-- Make sure data is committed before load (not strictly necessary in a script, but safe)
FLUSH TABLES;

-- Load CSV into Ola_cab
-- IMPORTANT: adjust the FIELDS/ENCLOSED/TERMINATED rules if The CSV format differs
LOAD DATA INFILE '/var/lib/mysql-files/MOCK_DATA_10000_more.csv'
INTO TABLE Ola_cab
CHARACTER SET utf8mb4
FIELDS TERMINATED BY ',' OPTIONALLY ENCLOSED BY '"' 
LINES TERMINATED BY '\n'
IGNORE 1 LINES
(
  id, first_name, last_name, email, gender, ip_address, city_id, cab_type, vendor_id, owner_id,
  lease_owner_id, cab_color_id, cab_model_id, cab_segment_id, installment, purchase_from, color, model,
  engine_number, chassis_number, total_purchase_cost, total_payment, policy_number, company_name, amount,
  idv_value, nil_depreciation_value, cab_number, tally_ledger_name, gps_number, registration_number, is_ac,
  allow_out_station, owner_come_as_driver, driver_id, booking_id, status, cab_state, rating, reduce_rating,
  rating_reduced_at, points, current_points, exit_initiated_at, exit_initiated_by_id, exit_initiated_by_role,
  exited_at, exited_by_id, device_model, os_version, driver_app_version, driver_app_version_updated_at,
  fc_end_date, policy_end_date, policy_start_date, purchase_date, manufacturing_year, meter_reading,
  permit_end_date, leased_vehicle, lease_agreement_end_date, date_of_commence, firstname, lastname, ipaddress
);

-- Basic verification queries
SELECT COUNT(*) AS total_rows FROM Ola_cab;
SELECT COUNT(*) AS cities FROM City;
SELECT COUNT(*) AS vendors FROM Vendor;
SELECT COUNT(*) AS owners FROM Owner;

SQL
```

---

## 2) Run the script (copy & paste)

From the same shell, run:

```bash
# Run the SQL script (you will be prompted for the MySQL root password)
mysql -u root -p < /tmp/ola_create_and_load.sql
```

Enter the root password when prompted. The script will:

* create DB and tables
* seed City/Vendor/Owner
* import the CSV (ignoring the header line)
* print the counts at the end

---

## 3) Quick interactive verification (optional)

If you prefer to run queries interactively after the load:

```bash
mysql -u root -p
# then inside mysql prompt:
USE ola_db;
SELECT COUNT(*) FROM Ola_cab;
SELECT id, name FROM City LIMIT 5;
SELECT id, name FROM Vendor LIMIT 5;
SELECT id, name FROM Owner LIMIT 5;
SELECT id, first_name, city_id FROM Ola_cab LIMIT 5;
```

---

## Notes, troubleshooting & tips

1. **secure_file_priv**
   The server-side `LOAD DATA INFILE` is allowed only for directories allowed by MySQL’s `secure_file_priv`. By default `/var/lib/mysql-files` is allowed on many installations — you already have the CSV there, which is perfect. If you get an error like `The MySQL server is running with the --secure-file-priv option so it cannot execute this statement`, let me know the error text and I’ll give the fix.

2. **CSV format**

   * I used `FIELDS TERMINATED BY ',' OPTIONALLY ENCLOSED BY '"'` and `LINES TERMINATED BY '\n'`. If The CSV uses `\r\n` line endings, replace `LINES TERMINATED BY '\r\n'`.
   * The `IGNORE 1 LINES` tells MySQL to skip the header row.

3. **Foreign key failures**

   * If a CSV row has a `city_id`, `vendor_id` or `owner_id` that is not present in the seeded lookup tables, MySQL will **reject** that row (foreign key constraint).
   * We seeded City 1..100, Vendor 1..50, Owner 1..200. If The CSV contains IDs outside these ranges, change the seeding ranges or pre-process the CSV to fix IDs. If you prefer MySQL to accept the rows without enforcement, I can give a variant that creates the table **without** foreign keys.

4. **If LOAD DATA fails with data conversion issues**

   * Date/time or boolean formats in the CSV can cause errors. If you hit such an error paste the error message and I’ll adapt the LOAD statement (or provide a Python loader that parses/validates).
   
</details>

You have a CSV with many columns. To import it properly, let's create a MySQL table that matches its structure.

***

<details>
    <summary>Click to view the long setup</summary>

## 1. Connect to MySQL container shell

```bash
docker exec -it local-mysql bash
```

## 2. Log into MySQL

```sql
mysql -u root -p
# Enter the password (rootpass)
```

## 3. Create and use the `appdb2` database

```sql
CREATE DATABASE IF NOT EXISTS appdb2;
USE appdb2;
```

## 4. Create the `Ola_cab` table

Based on The column list, here is a sample `CREATE TABLE` statement with suitable data types. You should adjust data types based on actual data characteristics (length, numeric or text, etc.):

```sql
CREATE TABLE Ola_cab (
  id INT PRIMARY KEY,
  first_name VARCHAR(100),
  last_name VARCHAR(100),
  email VARCHAR(255),
  gender VARCHAR(10),
  ip_address VARCHAR(45),
  city_id INT,
  cab_type VARCHAR(50),
  vendor_id INT,
  owner_id INT,
  lease_owner_id INT,
  cab_color_id INT,
  cab_model_id INT,
  cab_segment_id INT,
  installment DECIMAL(10,2),
  purchase_from VARCHAR(255),
  color VARCHAR(50),
  model VARCHAR(100),
  engine_number VARCHAR(100),
  chassis_number VARCHAR(100),
  total_purchase_cost DECIMAL(15,2),
  total_payment DECIMAL(15,2),
  policy_number VARCHAR(100),
  company_name VARCHAR(255),
  amount DECIMAL(15,2),
  idv_value DECIMAL(15,2),
  nil_depreciation_value DECIMAL(15,2),
  cab_number VARCHAR(50),
  tally_ledger_name VARCHAR(255),
  gps_number VARCHAR(100),
  registration_number VARCHAR(100),
  is_ac BOOLEAN,
  allow_out_station BOOLEAN,
  owner_come_as_driver BOOLEAN,
  driver_id INT,
  booking_id INT,
  status VARCHAR(50),
  cab_state VARCHAR(50),
  rating DECIMAL(3,2),
  reduce_rating BOOLEAN,
  rating_reduced_at DATETIME,
  points INT,
  current_points INT,
  exit_initiated_at DATETIME,
  exit_initiated_by_id INT,
  exit_initiated_by_role VARCHAR(50),
  exited_at DATETIME,
  exited_by_id INT,
  device_model VARCHAR(100),
  os_version VARCHAR(100),
  driver_app_version VARCHAR(50),
  driver_app_version_updated_at DATETIME,
  fc_end_date DATE,
  policy_end_date DATE,
  policy_start_date DATE,
  purchase_date DATE,
  manufacturing_year YEAR,
  meter_reading INT,
  permit_end_date DATE,
  leased_vehicle BOOLEAN,
  lease_agreement_end_date DATE,
  date_of_commence DATE,
  firstname VARCHAR(100),
  lastname VARCHAR(100),
  ipaddress VARCHAR(45)
);
```


> **Notes:**
> - `BOOLEAN` columns in MySQL are typically treated as tiny integers (0/1).
> - Adjust column sizes or types based on The data for best results.

## 5. Verify table creation

```sql
SHOW TABLES;
DESCRIBE Ola_cab;
```

## 6. Find and place the CSV in the allowed directory**

1. Check the secure file privilege path inside container:

```sql
SHOW VARIABLES LIKE 'secure_file_priv';
```

This will show the directory MySQL accepts secure file operations from, e.g., `/var/lib/mysql-files/`.

2. Copy The CSV into that directory inside container, e.g.:

```bash
docker cp /Users/gyanaranjan.mallick/Downloads/docker_local/MOCK_DATA_10000_more.csv local-mysql:/var/lib/mysql-files/
```

3. Then run The import using full path inside that directory, e.g.:

```sql
LOAD DATA INFILE '/var/lib/mysql-files/MOCK_DATA_10000_more.csv'
INTO TABLE Ola_cab
FIELDS TERMINATED BY ','  
ENCLOSED BY '\"'  
LINES TERMINATED BY '\n'
IGNORE 1 ROWS;
```

***

## 1. **What is a MySQL dump?**

A *dump* is a file (usually `.sql`) that contains a database’s schema and/or data — typically created using the `mysqldump` utility or via tools like `phpMyAdmin`, `MySQL Workbench`, or an automated backup script. **MySQL doesn’t automatically create or store dumps** anywhere.

---

### 2. **Where dumps can be found (if they exist):**

It depends on how the dump was created:

| Method                       | Default / Common Dump Location                                        | Notes                                                                                            |
| ---------------------------- | --------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
| **`mysqldump` command**      | Whatever path you specify with `>`                                    | Example: `mysqldump -u root -p appdb > /home/gyan/appdb.sql` → dump is in `/home/gyan/appdb.sql` |
| **`mysqlpump` command**      | Whatever path you specify with `--result-file`                        | Similar idea                                                                                     |
| **`phpMyAdmin` export**      | Browser download directory                                            | Usually `Downloads/` folder                                                                      |
| **Automated backup scripts** | Check `/var/backups/mysql/`, `/backups/`, `/opt/mysql_backups/`, etc. | Depends on The server setup or cron jobs                                                        |
| **Managed RDS instance**     | AWS S3 or RDS snapshots                                               | RDS stores snapshots, not `.sql` dumps, unless exported manually                                 |

---

### 3. **How to check if dumps already exist:**

If you’re on Linux, you can search for `.sql` dump files like this:

```bash
sudo find / -type f -name "*.sql" 2>/dev/null
```

Or, if you know roughly where backups might be stored:

```bash
sudo find /var/backups /home -type f -name "*.sql"
```

---

### 4. **To create a new dump manually:**

```bash
mysqldump -u root -p appdb > /path/to/save/appdb.sql
```

You’ll be prompted for The password, and then the dump file will be created in the location you specify.

</details>

---

# Migrate the dump to the mysql server in local
### Theoretical Overview: Migrating a Database to a Running Container

<details>
    <summary>Click to view the Theoretical Overview</summary>

#### Introduction to Database Migration in Containers
In modern software development and operations, databases are often managed within containerized environments like Docker to ensure portability, scalability, and isolation. The process of migrating a database involves transferring its schema (structure, such as tables and constraints) and data (records) from a source (e.g., a repository or dump file) to a target system, such as a running MySQL container. Theoretically, this migration ensures the database is replicated accurately, preserving relationships (e.g., primary and foreign keys), data integrity, and functionality. Key concepts include:

- **Schema Creation**: Defining the database structure, including tables, indexes, views, and constraints.
- **Data Loading**: Importing records into the tables while handling large datasets efficiently.
- **Integrity Verification**: Checking row counts, checksums (e.g., CRC), and relationships to confirm no data loss or corruption.
- **Container-Specific Considerations**: Containers provide ephemeral storage, so files must be copied into the container's filesystem, permissions adjusted for the database user, and configurations (e.g., enabling features like local file imports) applied to avoid access issues.

The migration is "idempotent" if scripted properly—meaning it can be re-run without duplicating data—and handles errors like format mismatches or path issues through validation steps.

#### Scenario: Migrating an HR Employee Database to a Production Container
Imagine you're a DevOps engineer at a mid-sized company transitioning from a traditional server-based HR system to a cloud-native setup. The existing HR database contains employee records, department details, salary histories, and titles for 300,000+ employees, stored in a repository as schema scripts and data dump files. The goal is to migrate this entire database to a running MySQL container in a Docker environment for better scalability during peak hiring seasons.

1. **Preparation Phase (Source Acquisition and Planning)**:
   - You start by acquiring the database source from a public repository (e.g., a GitHub archive). This includes a main schema script that defines the database structure (e.g., creating tables like "employees" with columns for ID, name, birth date, and hire date, plus constraints like primary keys on employee IDs and foreign keys linking departments to managers).
   - Theoretically, you plan the migration as a "lift and shift": the entire database is treated as a self-contained unit. You identify that the data dumps are in SQL insert format (e.g., bulk INSERT statements for efficiency with large datasets), not raw CSV or tab-separated files, which influences how you'll load the data.

2. **Container Setup and File Transfer**:
   - The target is a pre-running MySQL container, isolated from the host for security. You conceptually "migrate" the database by copying the schema script and data dumps into the container's filesystem (e.g., a secure directory like /var/lib/mysql-files/ to comply with MySQL's file privilege settings).
   - This step ensures the container acts as a standalone environment: files are placed where MySQL can access them, with ownership adjusted to the MySQL user to prevent permission denials. Theoretically, this mimics exporting a database dump from one server and importing it to another, but within the container's isolated namespace.

3. **Schema and Data Migration**:
   - First, the schema is applied to create the empty database structure inside the container. This includes defining relationships (e.g., a foreign key ensuring every department manager references a valid employee).
   - Next, the data is loaded by executing the dump files sequentially. Since the dumps use SQL inserts, the migration script "sources" them, executing thousands of insert operations in batches to populate tables efficiently. For large tables like salaries (millions of rows), this is optimized to avoid memory overflows.
   - If issues arise (e.g., data format mismatches leading to partial loads), the migration includes validation: dropping the database and retrying ensures a clean state.

4. **Integrity and Verification**:
   - Post-migration, you theoretically verify the database by checking table existence, row counts, and data samples. A checksum-based integrity test compares expected vs. actual records and hashes to detect corruption.
   - In the scenario, if the initial migration loads only partial data (e.g., due to misinterpreting dump formats), you diagnose by inspecting dump contents and adjust the loading method (e.g., switching from raw data import to SQL sourcing).

5. **Post-Migration Usage**:
   - The migrated database is now live in the container, ready for queries (e.g., joining employees with salaries for payroll reports). The container can be scaled (e.g., replicated in a cluster) without re-migrating, as the database is self-contained.
   - In The company's scenario, this enables HR teams to query employee data seamlessly, with the container handling high loads during audits.

#### Benefits and Theoretical Considerations
- **Portability**: Containers encapsulate the database, making it easy to deploy across environments (dev, staging, production).
- **Error Handling**: Migrations often face format mismatches (e.g., assuming raw data vs. SQL inserts), resolved through file inspection and method adjustment.
- **Scalability**: For large datasets, batch loading (as in inserts) prevents overloads, with configurations like increased packet sizes aiding efficiency.
- **Idempotency**: The process allows re-runs without data duplication, ideal for iterative testing.

This theoretical approach treats the migration as a holistic transfer of the database entity, ensuring fidelity from source to container without data loss. If applied to other databases, the same principles hold: acquire, transfer, load, verify.

The Employees Database is a sample MySQL database for HR/employee management, containing tables like `employees`, `departments`, `dept_emp`, `dept_manager`, `salaries`, and `titles`. It includes constraints (e.g., primary keys, foreign keys), relationships, and a large dataset (~300,024 employee records and ~2.8 million salary entries) for testing queries, optimization, and data analysis. The data is about 167 MB when exported.

This guide assumes you have basic familiarity with command-line tools. If you're new, use a tool like MySQL Workbench for a graphical interface to import the SQL file instead of the command line.

</details>

### Step-by-Step Guide to Import the Employees Database into a MySQL Container

This guide provides a clean and organized process to import the Employees Database into a MySQL container (e.g., `mysql:8.0`), addressing the issue with the `employees.sql` script where `source` commands incorrectly reference `.dump` files (raw tab-separated data) instead of using `LOAD DATA LOCAL INFILE`. The steps account for container-specific nuances like file paths and permissions, ensuring the database (with ~300,024 employee records and ~2.8M salary entries, ~167 MB) is set up with proper constraints (primary keys, foreign keys). The import may take 30-60sec. The modification of `employees.sql` is moved to immediately follow cloning the repository for clarity.

<details>
    <summary>Click to view Step-by-Step Guide to Import the Employees Database into a MySQL Container</summary>

#### Prerequisites
- **Docker and MySQL Container**: A running MySQL container. Start one if needed:
  ```
  docker run --name mysql-container -e MYSQL_ROOT_PASSWORD=The_password -d -p 3306:3306 mysql:8.0
  ```
  Replace `The_password` with a secure password. Use The container name (e.g., `mysql-container`).
- **Repository Files**: The `test_db` repository from https://github.com/datacharmer/test_db, containing `employees.sql` and `.dump` files (e.g., `load_departments.dump`).
- **User Privileges**: Root user access with full privileges (e.g., CREATE, INSERT, FOREIGN KEY).
- **Disk Space**: At least 500 MB free for data and indexes.
- **Local Infile**: Enabled for `LOAD DATA LOCAL INFILE` (verified in steps).
- **Git**: Installed on the host for cloning (or download ZIP).
- **Container Access**: Ability to run `docker cp` and `docker exec`.

#### Step 1: Clone the Repository and Modify `employees.sql`
1. **Clone the Repository on the Host**:
   ```
   git clone https://github.com/datacharmer/test_db.git
   ```
   Or download the ZIP from https://github.com/datacharmer/test_db, then extract it.
2. **Navigate to the Folder**:
   ```
   cd test_db
   ```
3. **Verify Files**:
   ```
   ls
   ```
   Expect `employees.sql`, `load_departments.dump`, etc.
4. **Modify `employees.sql`**:
   The script uses `source` commands, which expect SQL files, but the `.dump` files are raw tab-separated data. Edit `employees.sql` to use `LOAD DATA LOCAL INFILE`.
   - Backup the file:
     ```
     cp employees.sql employees.sql.bak
     ```
   - Edit with a text editor (e.g., `nano employees.sql` on the host):
     Replace the `source` section (near the end) with:
     ```
     SELECT 'LOADING departments' as 'INFO';
     source /var/lib/mysql-files/load_departments.dump;

     SELECT 'LOADING employees' as 'INFO';
     source /var/lib/mysql-files/load_employees.dump;

     SELECT 'LOADING dept_emp' as 'INFO';
     source /var/lib/mysql-files/load_dept_emp.dump;

     SELECT 'LOADING dept_manager' as 'INFO';
     source /var/lib/mysql-files/load_dept_manager.dump;

     SELECT 'LOADING titles' as 'INFO';
     source /var/lib/mysql-files/load_titles.dump;

     SELECT 'LOADING salaries' as 'INFO';
     source /var/lib/mysql-files/load_salaries1.dump;
     source /var/lib/mysql-files/load_salaries2.dump;
     source /var/lib/mysql-files/load_salaries3.dump;

     source /tmp/test_db/show_elapsed.sql;
     ```
     
   - **Key Changes**: Replaced `source` with `LOAD DATA LOCAL INFILE`; used absolute paths (`/var/lib/mysql-files/`); specified tab-separated format (`\t`); added column mappings; handled `to_date` with `NULLIF` for ongoing records.
   - Save and exit the editor.

#### Step 2: Verify Container Status
1. Check if the MySQL container is running:
   ```
   docker ps
   ```
   Start if needed: `docker start mysql-container`.
2. Get container details:
   ```
   docker ps -a
   ```

#### Step 3: Copy Files to the Container
1. Copy the `test_db` folder to the container:
   ```
   docker cp ./test_db mysql-container:/tmp/test_db
   ```
2. Access the container shell:
   ```
   docker exec -it mysql-container bash
   ```
3. Verify files:
   ```
   ls -al /tmp/test_db
   ```
   Expect `employees.sql`, `load_departments.dump`, etc.

#### Step 4: Enable Local Infile
1. Log into MySQL:
   ```
   mysql -u root -p
   ```
   Enter the root password.
2. Check `local_infile`:
   ```
   SHOW GLOBAL VARIABLES LIKE 'local_infile';
   ```
   If `OFF`, enable it: `SET GLOBAL local_infile = 1;`.
3. Exit MySQL: `exit;`.

#### Step 5: Move and Set Permissions for `.dump` Files
1. Move `.dump` files to `/var/lib/mysql-files` (for `secure_file_priv` compatibility):
   ```
   mv /tmp/test_db/*.dump /var/lib/mysql-files/
   chmod -R 644 /var/lib/mysql-files/*.dump
   chown -R mysql:mysql /var/lib/mysql-files
   ```
2. Verify `secure_file_priv`:
   ```
   mysql -u root -p
   SHOW VARIABLES LIKE 'secure_file_priv';
   ```
   If set to `/var/lib/mysql-files/`, proceed; if empty, `/tmp/test_db` may work.

#### Step 6: Run the Import
1. From the container shell:
   ```
   mysql -u root -p --local-infile=1 < /tmp/test_db/employees.sql
   ```
   Or from the host:
   ```
   docker exec -i mysql-container mysql -u root -p --local-infile=1 < ./test_db/employees.sql
   ```
2. Monitor output (e.g., "LOADING departments"). The script creates the `employees` database, tables with PK/FK constraints, and loads data.
3. For partitioned tables (better performance): Use `employees_partitioned.sql` instead (edit similarly if needed).

#### Step 7: Verify the Import
1. Log into MySQL:
   ```
   mysql -u root -p
   ```
2. Switch database: `USE employees;`
3. List tables: `SHOW TABLES;` (Expect 8, including views like `current_dept_emp`).
4. Check rows: `SELECT COUNT(*) FROM employees;` (~300,024); `SELECT COUNT(*) FROM salaries;` (~2,844,047).
5. Run integrity test:
   ```
   mysql -u root -p -t < /tmp/test_db/test_employees_md5.sql
   ```
   Expect "OK" for all tables.

#### Step 8: Explore the Database
- Sample queries:
  ```
  SELECT * FROM employees LIMIT 10;
  SELECT e.first_name, s.salary FROM employees e JOIN salaries s ON e.emp_no = s.emp_no LIMIT 10;
  ```
- Documentation: https://dev.mysql.com/doc/employee/en/ for schema.
- Drop/reload: `DROP DATABASE employees;` then repeat Step 6.

#### Troubleshooting
- **Local Infile Errors**: Add `local_infile=1` to `/etc/mysql/my.cnf`; restart container (`docker restart mysql-container`).
- **File Not Found**: Verify paths in `employees.sql`; re-copy repo:
  ```
  docker cp ./test_db mysql-container:/tmp/test_db
  ```
- **Permission Denied**: Check `ls -al /var/lib/mysql-files/*.dump`.
- **Data Format Issues**: Inspect with `head /var/lib/mysql-files/load_departments.dump`; adjust `FIELDS TERMINATED BY` (e.g., to `','`).
- **Memory/Timeout**: Set `SET GLOBAL max_allowed_packet = 268435456;`.
- **Logs**: `tail -n 50 /var/log/mysql/error.log`.
- **Interactive Run**: `mysql -u root -p --local-infile=1`, then `SOURCE /tmp/test_db/employees.sql;`.
- **GUI Access**: Use MySQL Workbench on host, connect to `localhost:3306`.


<details>
    <summary>Click to view Queries after the DB Setup</summary>

Now that the Employees Database has been successfully migrated into the MySQL container with all tables correctly populated (e.g., ~300,024 rows in `employees`, ~2,844,047 in `salaries`), let’s explore the database with a variety of SQL queries to test and analyze the data in different styles. The queries below cover basic data retrieval, aggregations, joins, filtering, sorting, and advanced analytics, tailored to the Employees Database schema (available at https://dev.mysql.com/doc/employee/en/). These queries help verify data integrity, explore relationships, and extract meaningful insights, suitable for an HR system context.

The database includes:
- **Tables**: `employees` (employee details), `departments` (department info), `dept_emp` (employee-department assignments), `dept_manager` (department managers), `salaries` (salary history), `titles` (job titles), plus views `current_dept_emp` and `dept_emp_latest_date`.
- **Key Relationships**: Foreign keys link `dept_emp` and `dept_manager` to `employees` and `departments`; `salaries` and `titles` to `employees`.

Below are queries categorized by purpose and style, designed to test the data comprehensively.

---

### Queries to Test and Explore the Employees Database

#### 1. Basic Data Retrieval
These queries fetch raw data to verify table contents and explore individual records.

- **Query 1: View Sample Employee Records**
  - Purpose: Check employee data for correctness.
  - Query:
    ```sql
    SELECT emp_no, first_name, last_name, gender, hire_date
    FROM employees
    LIMIT 5;
    ```
  - Expected Output: Displays 5 employee records, e.g.:
    ```
    +--------+------------+-----------+--------+------------+
    | emp_no | first_name | last_name | gender | hire_date  |
    +--------+------------+-----------+--------+------------+
    | 10001  | Georgi     | Facello   | M      | 1986-06-26 |
    | 10002  | Bezalel    | Simmel    | F      | 1985-11-21 |
    | 10003  | Parto      | Bamford   | M      | 1986-08-28 |
    | 10004  | Chirstian  | Koblick   | M      | 1986-12-01 |
    | 10005  | Kyoichi    | Maliniak  | M      | 1989-09-12 |
    +--------+------------+-----------+--------+------------+
    ```

- **Query 2: List All Departments**
  - Purpose: Verify the `departments` table.
  - Query:
    ```sql
    SELECT dept_no, dept_name
    FROM departments
    ORDER BY dept_no;
    ```
  - Expected Output: Lists 9 departments, e.g.:
    ```
    +---------+------------------+
    | dept_no | dept_name        |
    +---------+------------------+
    | d001    | Marketing        |
    | d002    | Finance          |
    | d003    | Human Resources  |
    ...
    +---------+------------------+
    ```

#### 2. Joins to Explore Relationships
These queries combine tables to test foreign key relationships and data consistency.

- **Query 3: Employee Current Department**
  - Purpose: Join `employees` with `current_dept_emp` and `departments` to show each employee’s current department.
  - Query:
    ```sql
    SELECT e.emp_no, e.first_name, e.last_name, d.dept_name
    FROM employees e
    JOIN current_dept_emp cde ON e.emp_no = cde.emp_no
    JOIN departments d ON cde.dept_no = d.dept_no
    LIMIT 10;
    ```
  - Expected Output: Shows employee names and their current department, e.g.:
    ```
    +--------+------------+-----------+------------------+
    | emp_no | first_name | last_name | dept_name        |
    +--------+------------+-----------+------------------+
    | 10001  | Georgi     | Facello   | Development      |
    | 10002  | Bezalel    | Simmel    | Sales            |
    ...
    ```

- **Query 4: Employee Salary History**
  - Purpose: Join `employees` with `salaries` to view salary records.
  - Query:
    ```sql
    SELECT e.emp_no, e.first_name, e.last_name, s.salary, s.from_date, s.to_date
    FROM employees e
    JOIN salaries s ON e.emp_no = s.emp_no
    WHERE e.emp_no = 10001
    ORDER BY s.from_date;
    ```
  - Expected Output: Shows salary history for employee 10001, e.g.:
    ```
    +--------+------------+-----------+--------+------------+------------+
    | emp_no | first_name | last_name | salary | from_date  | to_date    |
    +--------+------------+-----------+--------+------------+------------+
    | 10001  | Georgi     | Facello   | 60117  | 1986-06-26 | 1987-06-26 |
    | 10001  | Georgi     | Facello   | 62102  | 1987-06-26 | 1988-06-25 |
    ...
    ```

#### 3. Aggregation Queries
These queries summarize data to test counts, averages, and trends.

- **Query 5: Average Salary by Department**
  - Purpose: Calculate the average current salary per department.
  - Query:
    ```sql
    SELECT d.dept_name, ROUND(AVG(s.salary), 2) AS avg_salary
    FROM departments d
    JOIN current_dept_emp cde ON d.dept_no = cde.dept_no
    JOIN salaries s ON cde.emp_no = s.emp_no
    WHERE s.to_date = '9999-01-01'
    GROUP BY d.dept_name
    ORDER BY avg_salary DESC;
    ```
  - Expected Output: Shows average salaries, e.g.:
    ```
    +------------------+------------+
    | dept_name        | avg_salary |
    +------------------+------------+
    | Sales            | 80668.12   |
    | Marketing        | 78890.45   |
    ...
    +------------------+------------+
    ```

- **Query 6: Employee Count by Gender**
  - Purpose: Count employees by gender to verify data distribution.
  - Query:
    ```sql
    SELECT gender, COUNT(*) AS employee_count
    FROM employees
    GROUP BY gender;
    ```
  - Expected Output: Shows gender distribution, e.g.:
    ```
    +--------+---------------+
    | gender | employee_count |
    +--------+---------------+
    | M      | 180000        |
    | F      | 120024        |
    +--------+---------------+
    ```

#### 4. Filtering and Sorting
These queries test conditional logic and ordering.

- **Query 7: Recent Hires**
  - Purpose: Find employees hired in the last 5 years (relative to the dataset’s latest date, ~2002).
  - Query:
    ```sql
    SELECT emp_no, first_name, last_name, hire_date
    FROM employees
    WHERE hire_date >= '1997-01-01'
    ORDER BY hire_date DESC
    LIMIT 5;
    ```
  - Expected Output: Lists recent hires, e.g.:
    ```
    +--------+------------+-----------+------------+
    | emp_no | first_name | last_name | hire_date  |
    +--------+------------+-----------+------------+
    | 499999 | Sachin     | Tsukuda   | 2000-01-28 |
    ...
    ```

- **Query 8: High Earners**
  - Purpose: Identify employees with current salaries above 100,000.
  - Query:
    ```sql
    SELECT e.emp_no, e.first_name, e.last_name, s.salary
    FROM employees e
    JOIN salaries s ON e.emp_no = s.emp_no
    WHERE s.salary > 100000 AND s.to_date = '9999-01-01'
    ORDER BY s.salary DESC
    LIMIT 5;
    ```
  - Expected Output: Shows top earners, e.g.:
    ```
    +--------+------------+-----------+--------+
    | emp_no | first_name | last_name | salary |
    +--------+------------+-----------+--------+
    | 43624  | Tokuyasu   | Pesch     | 158220 |
    ...
    ```

#### 5. Advanced Analytics
These queries use subqueries, window functions, or complex joins for deeper insights.

- **Query 9: Current Managers with Tenure**
  - Purpose: List current department managers with their tenure duration.
  - Query:
    ```sql
    SELECT d.dept_name, e.first_name, e.last_name, 
           DATEDIFF(CURDATE(), dm.from_date) AS tenure_days
    FROM dept_manager dm
    JOIN employees e ON dm.emp_no = e.emp_no
    JOIN departments d ON dm.dept_no = d.dept_no
    WHERE dm.to_date = '9999-01-01'
    ORDER BY tenure_days DESC;
    ```
  - Expected Output: Shows managers and their tenure, e.g.:
    ```
    +------------------+------------+-----------+-------------+
    | dept_name        | first_name | last_name | tenure_days |
    +------------------+------------+-----------+-------------+
    | Development      | Leon       | DasSarma  | 14500       |
    ...
    ```

- **Query 10: Employee Salary Ranking by Department**
  - Purpose: Rank employees by salary within their current department using a window function.
  - Query:
    ```sql
    SELECT e.emp_no, e.first_name, e.last_name, d.dept_name, s.salary,
           RANK() OVER (PARTITION BY d.dept_no ORDER BY s.salary DESC) AS salary_rank
    FROM employees e
    JOIN current_dept_emp cde ON e.emp_no = cde.emp_no
    JOIN departments d ON cde.dept_no = d.dept_no
    JOIN salaries s ON e.emp_no = s.emp_no
    WHERE s.to_date = '9999-01-01'
    LIMIT 10;
    ```
  - Expected Output: Ranks employees by salary within departments, e.g.:
    ```
    +--------+------------+-----------+------------------+--------+-------------+
    | emp_no | first_name | last_name | dept_name        | salary | salary_rank |
    +--------+------------+-----------+------------------+--------+-------------+
    | 43624  | Tokuyasu   | Pesch     | Development      | 158220 | 1           |
    ...
    ```

#### 6. Data Integrity Checks
These queries validate the database’s consistency.

- **Query 11: Check for Orphaned Records**
  - Purpose: Ensure no `dept_emp` records reference non-existent employees (testing foreign key integrity).
  - Query:
    ```sql
    SELECT de.emp_no, de.dept_no
    FROM dept_emp de
    LEFT JOIN employees e ON de.emp_no = e.emp_no
    WHERE e.emp_no IS NULL;
    ```
  - Expected Output: Empty result set (due to foreign key constraints).

- **Query 12: Count Active Employees**
  - Purpose: Count employees with ongoing department assignments.
  - Query:
    ```sql
    SELECT COUNT(*) AS active_employees
    FROM current_dept_emp
    WHERE to_date = '9999-01-01';
    ```
  - Expected Output: ~240,124 active assignments.

#### Testing Approach
- **Run Sequentially**: Execute each query in MySQL to confirm expected row counts and data patterns.
- **Validate Relationships**: Queries 3, 4, and 9 test joins and foreign keys.
- **Check Aggregations**: Queries 5 and 6 ensure correct grouping and calculations.
- **Explore Edge Cases**: Queries 7 and 8 test filtering for specific conditions.
- **Use Analytics**: Queries 9 and 10 leverage advanced SQL for insights.
- **Verify Integrity**: Queries 11 and 12 confirm data consistency.

#### Running Queries
1. Log into MySQL:
   ```
   docker exec -it mysql-container mysql -u root -p
   USE employees;
   ```
2. Copy and paste each query, observing the output.
3. Compare results with expected counts from `test_employees_md5.sql`:
   ```
   mysql -u root -p -t < /tmp/test_db/test_employees_md5.sql
   ```

#### Notes
- **Schema Reference**: Use https://dev.mysql.com/doc/employee/en/ for table details.
- **Performance**: For large joins (e.g., Query 10), ensure `max_allowed_packet` is sufficient:
  ```
  SET GLOBAL max_allowed_packet = 268435456;
  ```
- **GUI Option**: Use MySQL Workbench (`localhost:3306`) to run queries visually.
- **Custom Analysis**: Adapt queries for specific HR needs, e.g., salary trends or promotion history.
   
</details>

<details>
    <summary>Click to view the Errors Faced and issue solved</summary>

### Documentation: Errors Faced and Solutions for Importing the Employees Database into a MySQL Container

This documentation outlines the errors encountered while importing the Employees Database into a MySQL container (using the `mysql:8.0` image) and the steps taken to resolve them. The Employees Database, sourced from https://github.com/datacharmer/test_db, contains ~300,024 employee records and ~2,844,047 salary entries (~167 MB). The process involved troubleshooting issues related to incorrect data loading, file format mismatches, and missing files, ensuring the database was correctly imported with all tables populated as expected.

---

#### Environment
- **MySQL Version**: 8.0.43 (MySQL Community Server - GPL)
- **Container**: Docker container (`mysql-container`)
- **Repository**: `test_db` from https://github.com/datacharmer/test_db
- **Files**: `employees.sql`, `load_*.dump` files (e.g., `load_employees.dump`, `load_salaries1.dump`), `show_elapsed.sql`, `test_employees_md5.sql`
- **Expected Tables**: 8 (`departments`, `employees`, `dept_emp`, `dept_manager`, `titles`, `salaries`, `current_dept_emp`, `dept_emp_latest_date`)
- **Expected Row Counts** (per `test_employees_md5.sql`):
  - `departments`: 9
  - `employees`: 300,024
  - `dept_emp`: 331,603
  - `dept_manager`: 24
  - `titles`: 443,308
  - `salaries`: 2,844,047

---

#### Errors Encountered and Solutions

##### Error 1: Incorrect Row Counts in Tables
**Description**:
- After running `employees.sql`, the `employees` and `salaries` tables contained only 1 row each instead of ~300,024 and ~2,844,047, respectively.
- The integrity test (`test_employees_md5.sql`) showed mismatched record counts and CRCs:
  ```
  +--------------+------------------+----------------------------------+
  | table_name   | found_records    | found_crc                        |
  +--------------+------------------+----------------------------------+
  | departments  |                1 | 44654a97e80b0a21d8152d7340d8eee4 |
  | dept_emp     |                0 |                                  |
  | dept_manager |                0 |                                  |
  | employees    |                1 | 6b93bc3d003ec1d24aac3b272f0c2920 |
  | salaries     |                1 | 77e4ce80a0e26e76bdb99088a057460c |
  | titles       |                1 | b76e1781057c58f750cb599ff9ad3664 |
  +--------------+------------------+----------------------------------+
  ```
- Inspecting `employees` showed invalid data:
  ```
  +--------+------------+------------+-----------+--------+------------+
  | emp_no | birth_date | first_name | last_name | gender | hire_date  |
  +--------+------------+------------+-----------+--------+------------+
  |      0 | 0000-00-00 |            |           |        | 0000-00-00 |
  +--------+------------+------------+-----------+--------+------------+
  ```

**Cause**:
- The `employees.sql` script was modified to use `LOAD DATA LOCAL INFILE` commands, assuming the `.dump` files (e.g., `load_employees.dump`) were tab-separated raw data.
- Inspection revealed the `.dump` files contained SQL `INSERT` statements (e.g., `INSERT INTO employees VALUES (10001,'1953-09-02','Georgi','Facello','M','1986-06-26'),`), not raw data.
- `LOAD DATA LOCAL INFILE` misinterpreted the `INSERT` statements, resulting in a single invalid row per table.

**Solution**:
1. **Inspected `.dump` Files**:
   ```
   head -n 5 /var/lib/mysql-files/load_employees.dump
   ```
   Confirmed the files contained `INSERT` statements, not tab-separated data.
2. **Reverted `employees.sql`**:
   - Backed up the modified script:
     ```
     cp /tmp/test_db/employees.sql /tmp/test_db/employees.sql.bak3
     ```
   - Edited `/tmp/test_db/employees.sql` to restore `source` commands, matching the SQL format of the `.dump` files:
     ```
     -- Sample employee database 
     -- See changelog table for details
     -- Copyright (C) 2007,2008, MySQL AB
     -- 
     -- Original data created by Fusheng Wang and Carlo Zaniolo
     -- http://www.cs.aau.dk/TimeCenter/software.htm
     -- http://www.cs.aau.dk/TimeCenter/Data/employeeTemporalDataSet.zip
     -- 
     -- Current schema by Giuseppe Maxia 
     -- Data conversion from XML to relational by Patrick Crews
     -- 
     -- This work is licensed under the 
     -- Creative Commons Attribution-Share Alike 3.0 Unported License. 
     -- To view a copy of this license, visit 
     -- http://creativecommons.org/licenses/by-sa/3.0/ or send a letter to 
     -- Creative Commons, 171 Second Street, Suite 300, San Francisco, 
     -- California, 94105, USA.
     -- 
     -- DISCLAIMER
     -- To the best of our knowledge, this data is fabricated, and
     -- it does not correspond to real people. 
     -- Any similarity to existing people is purely coincidental.
     -- 

     DROP DATABASE IF EXISTS employees;
     CREATE DATABASE IF NOT EXISTS employees;
     USE employees;

     SELECT 'CREATING DATABASE STRUCTURE' as 'INFO';

     DROP TABLE IF EXISTS dept_emp,
                          dept_manager,
                          titles,
                          salaries, 
                          employees, 
                          departments;

     /*!50503 set default_storage_engine = InnoDB */;
     /*!50503 select CONCAT('storage engine: ', @@default_storage_engine) as INFO */;

     CREATE TABLE employees (
         emp_no      INT             NOT NULL,
         birth_date  DATE            NOT NULL,
         first_name  VARCHAR(14)     NOT NULL,
         last_name   VARCHAR(16)     NOT NULL,
         gender      ENUM ('M','F')  NOT NULL,    
         hire_date   DATE            NOT NULL,
         PRIMARY KEY (emp_no)
     );

     CREATE TABLE departments (
         dept_no     CHAR(4)         NOT NULL,
         dept_name   VARCHAR(40)     NOT NULL,
         PRIMARY KEY (dept_no),
         UNIQUE  KEY (dept_name)
     );

     CREATE TABLE dept_manager (
        emp_no       INT             NOT NULL,
        dept_no      CHAR(4)         NOT NULL,
        from_date    DATE            NOT NULL,
        to_date      DATE            NOT NULL,
        FOREIGN KEY (emp_no)  REFERENCES employees (emp_no)    ON DELETE CASCADE,
        FOREIGN KEY (dept_no) REFERENCES departments (dept_no) ON DELETE CASCADE,
        PRIMARY KEY (emp_no,dept_no)
     ); 

     CREATE TABLE dept_emp (
         emp_no      INT             NOT NULL,
         dept_no     CHAR(4)         NOT NULL,
         from_date   DATE            NOT NULL,
         to_date     DATE            NOT NULL,
         FOREIGN KEY (emp_no)  REFERENCES employees   (emp_no)  ON DELETE CASCADE,
         FOREIGN KEY (dept_no) REFERENCES departments (dept_no) ON DELETE CASCADE,
         PRIMARY KEY (emp_no,dept_no)
     );

     CREATE TABLE titles (
         emp_no      INT             NOT NULL,
         title       VARCHAR(50)     NOT NULL,
         from_date   DATE            NOT NULL,
         to_date     DATE,
         FOREIGN KEY (emp_no) REFERENCES employees (emp_no) ON DELETE CASCADE,
         PRIMARY KEY (emp_no,title, from_date)
     ); 

     CREATE TABLE salaries (
         emp_no      INT             NOT NULL,
         salary      INT             NOT NULL,
         from_date   DATE            NOT NULL,
         to_date     DATE            NOT NULL,
         FOREIGN KEY (emp_no) REFERENCES employees (emp_no) ON DELETE CASCADE,
         PRIMARY KEY (emp_no, from_date)
     ); 

     CREATE OR REPLACE VIEW dept_emp_latest_date AS
         SELECT emp_no, MAX(from_date) AS from_date, MAX(to_date) AS to_date
         FROM dept_emp
         GROUP BY emp_no;

     CREATE OR REPLACE VIEW current_dept_emp AS
         SELECT l.emp_no, dept_no, l.from_date, l.to_date
         FROM dept_emp d
             INNER JOIN dept_emp_latest_date l
             ON d.emp_no=l.emp_no AND d.from_date=l.from_date AND l.to_date = d.to_date;

     FLUSH /*!50503 binary */ LOGS;

     SELECT 'LOADING departments' as 'INFO';
     source /var/lib/mysql-files/load_departments.dump;

     SELECT 'LOADING employees' as 'INFO';
     source /var/lib/mysql-files/load_employees.dump;

     SELECT 'LOADING dept_emp' as 'INFO';
     source /var/lib/mysql-files/load_dept_emp.dump;

     SELECT 'LOADING dept_manager' as 'INFO';
     source /var/lib/mysql-files/load_dept_manager.dump;

     SELECT 'LOADING titles' as 'INFO';
     source /var/lib/mysql-files/load_titles.dump;

     SELECT 'LOADING salaries' as 'INFO';
     source /var/lib/mysql-files/load_salaries1.dump;
     source /var/lib/mysql-files/load_salaries2.dump;
     source /var/lib/mysql-files/load_salaries3.dump;

     source /tmp/test_db/show_elapsed.sql;
     ```

3. **Moved `.dump` Files**:
   Ensured all `.dump` files were in `/var/lib/mysql-files/`:
   ```
   mv /tmp/test_db/*.dump /var/lib/mysql-files/
   chmod -R 644 /var/lib/mysql-files/*.dump
   chown -R mysql:mysql /var/lib/mysql-files
   ```
   Verified with:
   ```
   ls -al /var/lib/mysql-files/*.dump
   ```
4. **Dropped and Re-Imported Database**:
   ```
   mysql -u root -p
   DROP DATABASE employees;
   exit;
   mysql -u root -p < /tmp/test_db/employees.sql
   ```
   - Removed `--local-infile=1` since `source` doesn’t require it.
   - Monitored output to confirm successful loading of each table.

**Verification**:
- Checked row counts:
  ```
  mysql -u root -p
  USE employees;
  SELECT COUNT(*) FROM employees;  -- Returned ~300,024
  SELECT COUNT(*) FROM salaries; -- Returned ~2,844,047
  ```
- Ran integrity test:
  ```
  mysql -u root -p -t < /tmp/test_db/test_employees_md5.sql
  ```
  Confirmed all tables showed "OK" with matching record counts and CRCs.

---

##### Error 2: Failed to Open `show_elapsed.sql`
**Description**:
- During import, the error occurred:
  ```
  ERROR at line 170: Failed to open file 'show_elapsed.sql', error: 2
  ```
- This halted the script after data loading but didn’t affect table population (as it’s the last command).

**Cause**:
- The `employees.sql` script referenced `source show_elapsed.sql`, but the file was in `/tmp/test_db/`, not the current directory or `/var/lib/mysql-files/`.

**Solution**:
1. **Verified File Location**:
   ```
   ls -al /tmp/test_db/show_elapsed.sql
   ```
   Confirmed the file existed in `/tmp/test_db/`.
2. **Updated `employees.sql`**:
   Modified the last line to use the absolute path:
   ```
   source /tmp/test_db/show_elapsed.sql;
   ```
3. **Ensured Permissions**:
   ```
   chmod 644 /tmp/test_db/show_elapsed.sql
   chown mysql:mysql /tmp/test_db/show_elapsed.sql
   ```
4. **Re-Ran Import**:
   As part of Error 1’s solution, re-ran the import, which resolved this error since the correct path was used.

**Verification**:
- The import completed without the `show_elapsed.sql` error.
- `show_elapsed.sql` executed, displaying the elapsed time for the import.

---

##### Error 3: Incorrect Command Execution Path
**Description**:
- Attempting to run the import from the container shell failed:
  ```
  docker exec -i mysql-container mysql -u root -p --local-infile=1 < ./test_db/employees.sql
  bash: ./test_db/employees.sql: No such file or directory
  ```
- Additionally, `docker ps` failed inside the container:
  ```
  bash: docker: command not found
  ```

**Cause**:
- The command was run inside the container, where `./test_db/` refers to the container’s filesystem, but the file was on the host.
- `docker` commands are not available inside the container unless Docker is installed there.

**Solution**:
1. **Corrected Command Execution**:
   - Ran the import from the container shell using the correct path:
     ```
     mysql -u root -p < /tmp/test_db/employees.sql
     ```
   - Alternatively, from the host:
     ```
     docker exec -i mysql-container mysql -u root -p < ./test_db/employees.sql
     ```
2. **Ran `docker ps` on Host**:
   - Executed `docker ps` from the host to verify the container (`ac5354034ecb`) was running:
     ```
     docker ps
     ```
   - Confirmed the container ID matched `mysql-container`.

**Verification**:
- The import succeeded when using the correct path.
- `docker ps` on the host showed the container running.

---

#### Final Verification
After applying the solutions:
1. **Checked Database**:
   ```
   mysql -u root -p
   SHOW DATABASES;
   USE employees;
   SHOW TABLES;
   ```
   Confirmed 8 tables: `current_dept_emp`, `departments`, `dept_emp`, `dept_emp_latest_date`, `dept_manager`, `employees`, `salaries`, `titles`.
2. **Verified Row Counts**:
   ```
   SELECT COUNT(*) FROM employees;  -- ~300,024
   SELECT COUNT(*) FROM salaries; -- ~2,844,047
   ```
3. **Inspected Data**:
   ```
   SELECT * FROM employees LIMIT 5;
   ```
   Showed valid data, e.g.:
   ```
   +--------+------------+------------+-----------+--------+------------+
   | emp_no | birth_date | first_name | last_name | gender | hire_date  |
   +--------+------------+------------+-----------+--------+------------+
   |  10001 | 1953-09-02 | Georgi     | Facello   | M      | 1986-06-26 |
   |  10002 | 1964-06-02 | Bezalel    | Simmel    | F      | 1985-11-21 |
   ...
   ```
4. **Ran Integrity Test**:
   ```
   mysql -u root -p -t < /tmp/test_db/test_employees_md5.sql
   ```
   All tables showed "OK" with matching counts and CRCs.

---

#### Additional Notes
- **Performance**: The import took ~5-30 minutes due to the large dataset. Increasing `max_allowed_packet` helped if memory errors occurred:
  ```
  mysql -u root -p
  SET GLOBAL max_allowed_packet = 268435456;
  ```
- **GUI Access**: MySQL Workbench on the host (`localhost:3306`) was recommended for visual inspection.
- **Other Databases**: The process can be adapted for other datasets (e.g., Sakila) from https://dev.mysql.com/doc/index-other.html.
- **Troubleshooting Tools**:
  - Logs: `tail -n 50 /var/log/mysql/error.log`
  - Interactive import: `mysql -u root -p; SOURCE /tmp/test_db/employees.sql;`

---

#### Lessons Learned
1. **Verify File Formats**: Always inspect `.dump` files (e.g., `head load_employees.dump`) to confirm whether they contain SQL `INSERT` statements or raw data before choosing `source` or `LOAD DATA LOCAL INFILE`.
2. **Use Absolute Paths**: Ensure `source` commands use absolute paths (e.g., `/tmp/test_db/show_elapsed.sql`) to avoid file-not-found errors.
3. **Context for Commands**: Run `docker` commands on the host, not inside the container, unless Docker is installed there.
4. **Iterative Testing**: Use interactive imports and log checks to catch errors early.

This documentation captures the errors faced, their root causes, and the precise steps to resolve them, ensuring a successful import of the Employees Database.

</details>

If issues persist, share error outputs or specific issues with `ola_create_and_load.sql`. For other databases (e.g., Sakila, World), adapt this process using dumps from https://dev.mysql.com/doc/index-other.html.

</details>

---

### Documentation: Exporting and Restoring the Entire Employees Database in a MySQL Container

This documentation outlines the process of exporting (dumping) the entire `employees` database (~167 MB, including all 8 tables: `employees`, `departments`, `dept_emp`, `dept_manager`, `titles`, `salaries`, `current_dept_emp`, `dept_emp_latest_date`) from a running MySQL container (`mysql-container`, MySQL 8.0.43) and restoring it in a new container. It incorporates the successful steps from previous interactions, corrects the errors in the provided document, and retains the structure and examples for clarity. The corrected commands ensure a full database dump, addressing the issues encountered in the provided steps (e.g., ambiguous table-only commands and lack of container context). The process ensures portability, data integrity, and compatibility with MySQL’s database context requirements.

<details>
    <summary>Click to view the Steps</summary>

#### Objective
To create a complete, portable SQL dump of the `employees` database, including all tables, schemas, data, views, and foreign key relationships, transfer it to the local machine, and restore it in a new MySQL container for future use or migration.

#### Environment
- **MySQL Version**: 8.0.43 (MySQL Community Server - GPL)
- **Source Container**: `mysql-container` (running on port 3306)
- **New Container**: `new-mysql-container` (to be created on port 3307)
- **Database**: `employees` (~300,024 rows in `employees`, ~2,844,047 in `salaries`)
- **Files**: SQL dump file (`employees_full_dump.sql`)
- **Tools**: `mysqldump` for export, `mysql` for import, Docker for container management

#### The Key Point: Schema vs. Database
In MySQL, tables exist within a database (schema) context, and `mysqldump` requires specifying the database name to provide this context. The dump includes:
- **Schema**: `CREATE TABLE` statements defining columns, constraints, and indexes.
- **Data**: `INSERT` statements for table rows.
- **Database Context**: `CREATE DATABASE` and `USE database;` statements for proper restoration.
- **Dependencies**: Foreign keys (e.g., `employees.emp_no` referenced by `salaries`) and views (e.g., `current_dept_emp`) require the full database context to maintain integrity.

**Challenges Without Schema**:
1. Data-only dumps (`--no-create-info`) require an identical table structure in the target, risking errors if absent.
2. Missing foreign keys, indexes, or views can break relationships or functionality.
3. Data type mismatches may occur without schema definitions.

**Correct Understanding**:
| Command | What Gets Dumped | Schema/Database Included? |
|---------|------------------|---------------------------|
| `mysqldump --databases employees` | Entire database (all 8 tables, schema, data, views) | ✅ Database schema + all tables |
| `mysqldump employees employees` | `employees` table (schema + data) within `employees` database | ✅ Database context + 1 table |
| `mysqldump employees departments salaries` | 3 tables (schema + data) within `employees` database | ✅ Database context + 3 tables |

#### Why This Matters
1. **Database Context Requirement**: Tables cannot be dumped without referencing their database (e.g., `mysqldump employees_table` is invalid; `mysqldump employees employees` is correct).
2. **Foreign Key Dependencies**: The `employees` database has inter-table relationships (e.g., `salaries` references `employees.emp_no`), so a full database dump ensures all dependencies are included.
3. **Portability**: Including `CREATE DATABASE` simplifies restoration in any MySQL environment.
4. **Integrity**: Dumping the entire database preserves views and constraints, critical for the `employees` database’s functionality.

##### Step 1: Dump the Entire Employees Database
1. **Access the Source Container**:
   ```
   docker exec -it mysql-container bash
   ```
   - Purpose: Access the container’s filesystem to run `mysqldump`.
2. **Create the Full Database Dump**:
   ```
   mysqldump -u root -p --databases employees --single-transaction > /var/lib/mysql-files/employees_full_dump.sql
   ```
   - **Options**:
     - `-u root`: Root user (replace if different).
     - `-p`: Prompts for password.
     - `--databases employees`: Includes `CREATE DATABASE employees` for easy restoration.
     - `--single-transaction`: Ensures consistency for InnoDB tables without locking.
     - Output: `/var/lib/mysql-files/employees_full_dump.sql` (~167-200 MB).
   - Entered root password.
   - Purpose: Generated a complete SQL dump with schema, data, views, and constraints for all 8 tables.
3. **Verify the Dump File**:
   ```
   ls -al /var/lib/mysql-files/employees_full_dump.sql
   head -n 20 /var/lib/mysql-files/employees_full_dump.sql
   ```
   - Expected: File starts with `-- MySQL dump`, `CREATE DATABASE employees`, `USE employees;`, and `CREATE TABLE` statements for all tables.
4. **Set Permissions**:
   ```
   chmod 644 /var/lib/mysql-files/employees_full_dump.sql
   chown mysql:mysql /var/lib/mysql-files/employees_full_dump.sql
   ```
   - Purpose: Ensures MySQL can access the file.

##### Step 2: Transfer the Dump to the Local Machine
1. **Exit the Container**:
   ```
   exit
   ```
2. **Copy to Host**:
   ```
   docker cp mysql-container:/var/lib/mysql-files/employees_full_dump.sql ./employees_full_dump.sql
   ```
   - Purpose: Transfers the dump to the host for backup or sharing.
3. **Verify Locally**:
   ```
   ls -al ./employees_full_dump.sql
   head -n 20 ./employees_full_dump.sql
   ```
   - Size: ~167-200 MB.
4. **Optional: Compress for Storage**:
   ```
   gzip ./employees_full_dump.sql
   ```
   - Creates `employees_full_dump.sql.gz` (decompress with `gunzip` before restoration).

##### Step 3: Set Up a New MySQL Container
1. **Create a New Container**:
   ```
   docker run --name new-mysql-container -e MYSQL_ROOT_PASSWORD=The_password -d -p 3307:3306 mysql:8.0
   ```
   - Port 3307 avoids conflict with `mysql-container` (port 3306).
   - Replace `The_password` with a secure password.
2. **Verify Container**:
   ```
   docker ps
   ```
   - Confirms `new-mysql-container` is running.

##### Step 4: Restore the Database in the New Container
1. **Copy the Dump File**:
   - If compressed, decompress:
     ```
     gunzip ./employees_full_dump.sql.gz
     ```
   - Copy to the new container:
     ```
     docker cp ./employees_full_dump.sql new-mysql-container:/var/lib/mysql-files/employees_full_dump.sql
     ```
2. **Access the New Container**:
   ```
   docker exec -it new-mysql-container bash
   ```
3. **Set Permissions**:
   ```
   chmod 644 /var/lib/mysql-files/employees_full_dump.sql
   chown mysql:mysql /var/lib/mysql-files/employees_full_dump.sql
   ```
4. **Import the Dump**:
   ```
   mysql -u root -p < /var/lib/mysql-files/employees_full_dump.sql
   ```
   - Or from the host:
     ```
     docker exec -i new-mysql-container mysql -u root -p < ./employees_full_dump.sql
     ```
   - Enter root password.
   - Import time: 5-30 minutes.
   - The `--databases` option ensures `CREATE DATABASE employees` is included, so no manual database creation is needed.
5. **Optional: Enable Local Infile** (for future operations):
   ```
   mysql -u root -p
   SET GLOBAL local_infile = 1;
   exit;
   ```

##### Step 5: Verify the Restored Database
1. **Log into MySQL**:
   ```
   docker exec -it new-mysql-container mysql -u root -p
   ```
2. **Check Databases and Tables**:
   ```
   SHOW DATABASES;
   USE employees;
   SHOW TABLES;
   ```
   - Expected: `employees` database with 8 tables: `departments`, `employees`, `dept_emp`, etc.
3. **Verify Row Counts**:
   ```
   SELECT COUNT(*) FROM employees;  -- ~300,024
   SELECT COUNT(*) FROM salaries;  -- ~2,844,047
   ```
4. **Inspect Sample Data**:
   ```
   SELECT * FROM employees LIMIT 5;
   ```
   - Expected:
     ```
     +--------+------------+------------+-----------+--------+------------+
     | emp_no | birth_date | first_name | last_name | gender | hire_date  |
     +--------+------------+------------+-----------+--------+------------+
     | 10001  | 1953-09-02 | Georgi     | Facello   | M      | 1986-06-26 |
     ...
     ```
5. **Run Integrity Test**:
   - Copy test script:
     ```
     docker cp ./test_db/test_employees_md5.sql new-mysql-container:/var/lib/mysql-files/test_employees_md5.sql
     ```
   - Run:
     ```
     mysql -u root -p -t < /var/lib/mysql-files/test_employees_md5.sql
     ```
   - Expected: "OK" for all tables, confirming correct row counts and CRCs.

##### Step 6: Practical Examples for Table-Level Dumps
For reference, here are corrected examples for dumping specific tables (though the main process focuses on the full database):

- **Dump Single Table (e.g., `employees`)**:
  ```
  docker exec -it mysql-container bash
  mysqldump -u root -p employees employees > /var/lib/mysql-files/employees_only.sql
  docker cp mysql-container:/var/lib/mysql-files/employees_only.sql ./employees_only.sql
  ```
  - Output: Schema and data for the `employees` table, with `USE employees;`.

- **Dump Multiple Tables (e.g., `departments` and `salaries`)**:
  ```
  mysqldump -u root -p employees departments salaries > /var/lib/mysql-files/dept_salary_only.sql
  docker cp mysql-container:/var/lib/mysql-files/dept_salary_only.sql ./dept_salary_only.sql
  ```

- **Dump Data Only (No Schema)**:
  ```
  mysqldump -u root -p --no-create-info employees employees > /var/lib/mysql-files/employees_data_only.sql
  docker cp mysql-container:/var/lib/mysql-files/employees_data_only.sql ./employees_data_only.sql
  ```
  - Note: Requires identical table structure in the target.

- **Dump Department-Related Tables**:
  ```
  mysqldump -u root -p employees departments dept_emp dept_manager > /var/lib/mysql-files/dept_tables.sql
  docker cp mysql-container:/var/lib/mysql-files/dept_tables.sql ./dept_tables.sql
  ```

**Restoring a Single Table Example**:
- Copy to new container:
  ```
  docker cp ./employees_only.sql new-mysql-container:/var/lib/mysql-files/employees_only.sql
  ```
- Import (ensure `employees` database exists):
  ```
  docker exec -it new-mysql-container mysql -u root -p employees < /var/lib/mysql-files/employees_only.sql
  ```
- Handle foreign keys (if other tables reference `employees`):
  ```
  SET FOREIGN_KEY_CHECKS = 0;
  SOURCE /var/lib/mysql-files/employees_only.sql;
  SET FOREIGN_KEY_CHECKS = 1;
  ```

##### Step 7: Cleanup and Storage
1. **Exit and Stop (if Testing)**:
   - Exit shell: `exit`.
   - Stop new container (optional):
     ```
     docker stop new-mysql-container
     ```
2. **Archive Dump**:
   - Store in a backup directory or repository:
     ```
     mv ./employees_full_dump.sql ~/backups/
     tar -czf ~/backups/employees_backup.tar.gz ~/backups/employees_full_dump.sql
     ```
3. **Future Use**: Repeat Steps 3-5 for restoration in other containers.

#### The Generated File: `employees_full_dump.sql`
The full database dump includes:
```sql
-- MySQL dump 10.13  Distrib 8.0.43, for Linux (x86_64)
--
-- Host: localhost    Database: employees
-- ------------------------------------------------------
-- Server version	8.0.43

/*!40101 SET @OLD_CHARACTER_SET_CLIENT=@@CHARACTER_SET_CLIENT */;
/*!40101 SET @OLD_CHARACTER_SET_RESULTS=@@CHARACTER_SET_RESULTS */;
/*!40101 SET @OLD_COLLATION_CONNECTION=@@COLLATION_CONNECTION */;
/*!50503 SET NAMES utf8mb4 */;

--
-- Database: `employees`
--

CREATE DATABASE IF NOT EXISTS `employees`;
USE `employees`;

--
-- Table structure for table `employees`
--
DROP TABLE IF EXISTS `employees`;
CREATE TABLE `employees` (
  `emp_no` int NOT NULL,
  `birth_date` date NOT NULL,
  `first_name` varchar(14) NOT NULL,
  `last_name` varchar(16) NOT NULL,
  `gender` enum('M','F') NOT NULL,
  `hire_date` date NOT NULL,
  PRIMARY KEY (`emp_no`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;

--
-- Dumping data for table `employees`
--
LOCK TABLES `employees` WRITE;
/*!40000 ALTER TABLE `employees` DISABLE KEYS */;
INSERT INTO `employees` VALUES (10001,'1953-09-02','Georgi','Facello','M','1986-06-26');
-- ... more INSERT statements
/*!40000 ALTER TABLE `employees` ENABLE KEYS */;
UNLOCK TABLES;

-- ... Similar structure for departments, dept_emp, dept_manager, salaries, titles, views
```

**Key Elements**:
- `CREATE DATABASE` and `USE employees;` for context.
- `CREATE TABLE` for each table’s schema.
- `INSERT` statements for data.
- Views like `current_dept_emp` included.

#### Why This Design Makes Sense
1. **Tables Belong to Databases**: MySQL requires database context for tables, ensuring correct placement during import.
2. **Foreign Key Dependencies**: The `employees` database has inter-table relationships (e.g., `salaries` references `employees.emp_no`), so a full dump preserves integrity.
3. **Import Compatibility**: `CREATE DATABASE` and `USE` statements make the dump portable across environments.
4. **Complete Backup**: Includes views, constraints, and data for disaster recovery.

#### Real-World Scenarios
1. **Sharing with a Colleague**:
   - Share `employees_full_dump.sql`:
     ```
     mysqldump -u root -p --databases employees --single-transaction > /var/lib/mysql-files/employees_full_dump.sql
     docker cp mysql-container:/var/lib/mysql-files/employees_full_dump.sql ./employees_full_dump.sql
     ```
   - Colleague imports:
     ```
     docker cp ./employees_full_dump.sql their-container:/var/lib/mysql-files/employees_full_dump.sql
     docker exec -i their-container mysql -u root -p < /var/lib/mysql-files/employees_full_dump.sql
     ```

2. **Updating Existing Data**:
   - Dump data only for specific tables (if schema exists):
     ```
     mysqldump -u root -p --no-create-info employees employees > /var/lib/mysql-files/employees_data_only.sql
     docker cp mysql-container:/var/lib/mysql-files/employees_data_only.sql ./employees_data_only.sql
     ```
   - Import:
     ```
     docker exec -i new-mysql-container mysql -u root -p employees < ./employees_data_only.sql
     ```

3. **Complex Dependencies**:
   - Dump related tables (e.g., `employees`, `salaries`, `titles`):
     ```
     mysqldump -u root -p employees employees salaries titles > /var/lib/mysql-files/related_tables.sql
     docker cp mysql-container:/var/lib/mysql-files/related_tables.sql ./related_tables.sql
     ```

#### Best Practice Summary
1. **Full Database Dump**: Use `--databases` for complete portability.
2. **Single Table Export**: Include schema unless the target has an identical structure.
3. **Related Tables**: Dump all dependent tables to preserve foreign keys.
4. **Data-Only Updates**: Use `--no-create-info` for existing schemas.
5. **Container Context**: Use `docker cp` and `/var/lib/mysql-files/` for file management.
6. **Verification**: Always check row counts and run integrity tests post-import.

#### Errors Faced and Resolutions
The provided steps failed due to several issues, which were resolved as follows:

1. **Error: Ambiguous Table-Only Dump Commands**
   - **Issue**: Commands like `mysqldump employees_table` were invalid, as MySQL requires the database name (e.g., `employees`).
   - **Resolution**: Used `mysqldump -u root -p --databases employees` for a full database dump, ensuring correct context. For single tables, used `mysqldump -u root -p employees employees`.

2. **Error: Confusion Over Full vs. Single-Table Dumps**
   - **Issue**: The provided steps mixed single-table examples (e.g., `mysqldump employees employees`) with the goal of a full database dump, causing confusion.
   - **Resolution**: Focused on a full database dump with `mysqldump --databases employees`, retaining single-table examples for reference.

3. **Error: Missing Container Context**
   - **Issue**: Commands like `mysqldump -u username -p employees` didn’t account for Docker’s filesystem or `secure_file_priv`.
   - **Resolution**: Ran `mysqldump` inside the container, saved to `/var/lib/mysql-files/`, and used `docker cp` for file transfers:
     ```
     docker cp mysql-container:/var/lib/mysql-files/employees_full_dump.sql ./employees_full_dump.sql
     ```

4. **Error: Incomplete Restoration Guidance**
   - **Issue**: The provided steps didn’t specify creating the database or handling permissions in the new container.
   - **Resolution**: Used `--databases` to include `CREATE DATABASE`, set permissions (`chmod 644`, `chown mysql:mysql`), and imported with:
     ```
     mysql -u root -p < /var/lib/mysql-files/employees_full_dump.sql
     ```

5. **Potential Issue: Foreign Key Constraints**
   - **Issue**: Not encountered, but restoring single tables (e.g., `employees`) could fail due to dependencies (e.g., `salaries`).
   - **Resolution**: Included all tables in the full dump. For single-table restores, disable foreign keys temporarily:
     ```
     SET FOREIGN_KEY_CHECKS = 0;
     SOURCE /var/lib/mysql-files/employees_only.sql;
     SET FOREIGN_KEY_CHECKS = 1;
     ```

6. **Potential Issue: Slow Import or Memory Limits**
   - **Issue**: Large dumps (~167 MB) could hit memory limits.
   - **Resolution**: Increased `max_allowed_packet`:
     ```
     mysql -u root -p
     SET GLOBAL max_allowed_packet = 268435456;
     ```

#### Verification and Testing
- Post-import, verified with:
  ```
  SELECT COUNT(*) FROM employees;  -- ~300,024
  SELECT COUNT(*) FROM salaries;  -- ~2,844,047
  SELECT * FROM employees LIMIT 5;
  mysql -u root -p -t < /var/lib/mysql-files/test_employees_md5.sql
  ```
- Confirmed all tables, row counts, and CRCs matched expected values.

#### Conclusion
This corrected process successfully dumped the entire `employees` database and restored it in a new container, addressing the provided document’s errors by using proper `mysqldump` syntax, Docker file management, and full database context. The dump is portable for sharing, backup, or migration, with single-table examples provided for flexibility. If further issues arise, share error logs or outputs for targeted assistance.

<details>
    <summary>Click to view the quick steps to execute</summary>


### Step-by-Step Guide to Dump the Entire Employees Database and Restore It in Another Container

Now that the Employees Database is running successfully in The current MySQL container (`mysql-container`), we'll export (dump) the entire database (including all tables, schema, data, views, and constraints) to a SQL file on The local machine. Then, we'll restore it in a new MySQL container. This process ensures the full database (~167 MB) is portable and can be migrated without data loss. The dump will include everything: tables like `employees` (~300,024 rows), `salaries` (~2,844,047 rows), and relationships via foreign keys.

#### Prerequisites
- The current container (`mysql-container`) is running with the `employees` database populated.
- Docker is installed on the host.
- Root access to MySQL (with password set).
- Disk space: At least 200 MB free on the host for the dump file.

#### Step 1: Dump the Entire Employees Database
1. **Access the Container Shell**:
   ```
   docker exec -it mysql-container bash
   ```
2. **Create the Full Database Dump**:
   Use `mysqldump` to export the entire `employees` database to a SQL file:
   ```
   mysqldump -u root -p --databases employees --single-transaction > /var/lib/mysql-files/employees_full_dump.sql
   ```
   - **Options Explained**:
     - `-u root`: Root user (replace if different).
     - `-p`: Prompts for password.
     - `--databases employees`: Includes `CREATE DATABASE` for easy restore.
     - `--single-transaction`: Ensures consistency without locking tables.
     - `> /var/lib/mysql-files/employees_full_dump.sql`: Saves to a secure directory.
   - Enter the root password when prompted.
   - The dump includes schema (`CREATE TABLE`), data (`INSERT`), and views.
3. **Verify the Dump File**:
   ```
   ls -al /var/lib/mysql-files/employees_full_dump.sql
   head -n 20 /var/lib/mysql-files/employees_full_dump.sql
   ```
   - Expected: File size ~167-200 MB; starts with `-- MySQL dump` header, followed by `CREATE DATABASE employees` and table definitions.
4. **Set Permissions** (if needed):
   ```
   chmod 644 /var/lib/mysql-files/employees_full_dump.sql
   chown mysql:mysql /var/lib/mysql-files/employees_full_dump.sql
   ```

#### Step 2: Copy the Dump File to The Local Machine
1. **Exit the Container**:
   ```
   exit
   ```
2. **Transfer the Dump File**:
   Copy from the container to the host:
   ```
   docker cp mysql-container:/var/lib/mysql-files/employees_full_dump.sql ./employees_full_dump.sql
   ```
3. **Verify Locally**:
   ```
   ls -al ./employees_full_dump.sql
   head -n 20 ./employees_full_dump.sql
   ```
4. **Optional: Compress for Storage**:
   ```
   gzip ./employees_full_dump.sql
   ```
   - Creates `employees_full_dump.sql.gz` (decompress with `gunzip` before restore).

#### Step 3: Set Up a New MySQL Container
1. **Start a New Container**:
   Create a fresh MySQL container:
   ```
   docker run --name new-mysql-container -e MYSQL_ROOT_PASSWORD=The_password -d -p 3307:3306 mysql:8.0
   ```
   - Uses port 3307 to avoid conflict with the original container (port 3306).
   - Replace `The_password` with a secure password.
2. **Verify New Container**:
   ```
   docker ps
   ```
   - Look for `new-mysql-container` running.

#### Step 4: Restore the Database in the New Container
1. **Copy the Dump File to the New Container**:
   ```
   docker cp ./employees_full_dump.sql new-mysql-container:/var/lib/mysql-files/employees_full_dump.sql
   ```
2. **Access the New Container Shell**:
   ```
   docker exec -it new-mysql-container bash
   ```
3. **Set Permissions**:
   ```
   chmod 644 /var/lib/mysql-files/employees_full_dump.sql
   chown mysql:mysql /var/lib/mysql-files/employees_full_dump.sql
   ```
4. **Import the Dump**:
   ```
   mysql -u root -p < /var/lib/mysql-files/employees_full_dump.sql
   ```
   - Enter the root password.
   - This recreates the `employees` database and all tables/data.
   - Import time: 5-30 minutes for the full dataset.
5. **Optional: Enable Local Infile (if needed for future operations)**:
   ```
   mysql -u root -p
   SET GLOBAL local_infile = 1;
   exit;
   ```

#### Step 5: Verify the Restored Database
1. **Log into MySQL in the New Container**:
   ```
   mysql -u root -p
   ```
2. **Check Databases and Switch**:
   ```
   SHOW DATABASES;
   USE employees;
   ```
   - Expect `employees` in the list.
3. **List Tables**:
   ```
   SHOW TABLES;
   ```
   - Expect 8 tables: `departments`, `employees`, `dept_emp`, etc.
4. **Check Row Counts**:
   ```
   SELECT COUNT(*) FROM employees;  -- ~300,024
   SELECT COUNT(*) FROM salaries;   -- ~2,844,047
   ```
5. **Inspect Sample Data**:
   ```
   SELECT * FROM employees LIMIT 5;
   ```
   - Expect valid employee records.
6. **Run Integrity Test** (Copy Test Script First)**:
   - Copy `test_employees_md5.sql` to the new container:
     ```
     docker cp ./test_db/test_employees_md5.sql new-mysql-container:/var/lib/mysql-files/test_employees_md5.sql
     ```
   - Run:
     ```
     mysql -u root -p -t < /var/lib/mysql-files/test_employees_md5.sql
     ```
   - Expect "OK" for all tables.

#### Step 6: Cleanup and Future Use
1. **Exit and Stop (if Testing)**:
   - Exit shell: `exit`.
   - Stop the new container (optional):
     ```
     docker stop new-mysql-container
     ```
2. **Store the Dump**:
   - Keep `employees_full_dump.sql` in a safe location (e.g., Git repository or cloud storage) for future restores.
   - To restore in another environment, repeat Steps 3-5.
3. **Optional: Compress and Archive**:
   ```
   tar -czf employees_backup.tar.gz ./employees_full_dump.sql
   ```

#### Troubleshooting
- **Import Errors** (e.g., "Duplicate entry"): Drop the database first:
  ```
  mysql -u root -p
  DROP DATABASE IF EXISTS employees;
  ```
- **Foreign Key Issues**: Disable temporarily during import:
  ```
  mysql -u root -p -e "SET FOREIGN_KEY_CHECKS=0;" employees < /var/lib/mysql-files/employees_full_dump.sql
  mysql -u root -p -e "SET FOREIGN_KEY_CHECKS=1;" employees
  ```
- **Large Dump Slowdown**: Increase `max_allowed_packet` in the new container:
  ```
  mysql -u root -p
  SET GLOBAL max_allowed_packet = 268435456;
  ```
- **File Not Found**: Verify paths with `ls -al /var/lib/mysql-files/employees_full_dump.sql`.
- **Logs**: Check for errors:
  ```
  tail -n 50 /var/log/mysql/error.log
  ```

This process creates a complete, portable dump of the Employees Database and restores it in a new container, ensuring you can replicate the setup anywhere. If you encounter issues, share the error output for further guidance!
   
</details>

</details>

---

### Step-by-Step Guide to Delete a Table, Back Up Specific Tables, and Restore in a New MySQL Container

Since we have successfully migrated the entire `employees` database to a new MySQL container (`new-mysql-container`) and verified its functionality, you now want to:
1. Delete a specific table (e.g., `employees`) from the `employees` database in the original container (`mysql-container`).
2. Take a backup (dump) of specific tables (e.g., `employees`, `departments`, and `salaries`) from the original container.
3. Restore these tables in the new container (`new-mysql-container`) to ensure they are correctly recreated.

This process builds on the previous successful full database dump and restore, focusing on table-level operations while addressing dependencies (e.g., foreign keys) and container-specific requirements. Below is a detailed guide with corrected commands, incorporating lessons from prior interactions and the provided document, ensuring the process is robust and portable.


<details>
    <summary>Click to view Prerequisites and Steps</summary>

#### Prerequisites
- **Environment**:
  - **Source Container**: `mysql-container` (MySQL 8.0.43, port 3306) with the `employees` database (~300,024 rows in `employees`, ~2,844,047 in `salaries`).
  - **New Container**: `new-mysql-container` (port 3307) with the `employees` database already restored.
  - **Tables**: 8 tables (`employees`, `departments`, `dept_emp`, `dept_manager`, `titles`, `salaries`, `current_dept_emp`, `dept_emp_latest_date`).
- **Tools**: `mysqldump` for backup, `mysql` for import/deletion, Docker for container management.
- **Disk Space**: ~50-100 MB for specific table dumps.
- **Access**: Root credentials for both containers.

#### Key Considerations
- **Foreign Key Dependencies**: The `employees` table is referenced by `salaries`, `titles`, `dept_emp`, and `dept_manager`. Deleting it requires disabling foreign key checks to avoid errors.
- **Schema Context**: As noted in the provided document, table dumps must include the database context (`employees`) to ensure portability and correct restoration.
- **Portability**: Dumps include `CREATE TABLE` statements to recreate the schema, avoiding issues with missing structures in the target.

---

### Step-by-Step Process

#### Step 1: Delete the `employees` Table in the Original Container
1. **Access the Source Container**:
   ```
   docker exec -it mysql-container mysql -u root -p
   ```
   - Enter the root password.
2. **Disable Foreign Key Checks**:
   - Since `employees` is referenced by other tables, disable foreign keys to allow deletion:
     ```
     SET FOREIGN_KEY_CHECKS = 0;
     ```
3. **Delete the `employees` Table**:
   ```
   USE employees;
   DROP TABLE employees;
   ```
4. **Re-enable Foreign Key Checks**:
   ```
   SET FOREIGN_KEY_CHECKS = 1;
   ```
5. **Verify Deletion**:
   ```
   SHOW TABLES;
   SELECT COUNT(*) FROM employees;
   ```
   - Expect: `employees` missing from `SHOW TABLES` and an error for the `SELECT` query (table doesn’t exist).
6. **Exit MySQL**:
   ```
   exit;
   ```

#### Step 2: Back Up Specific Tables (`employees`, `departments`, `salaries`)
1. **Access the Container Shell**:
   ```
   docker exec -it mysql-container bash
   ```
   - Note: The `employees` table is deleted in `mysql-container`, so you’ll need to use the `new-mysql-container` (where the full database was previously restored) to dump the tables.
2. **Exit and Switch to New Container**:
   ```
   exit
   docker exec -it new-mysql-container bash
   ```
3. **Create the Table-Specific Dump**:
   - Dump the `employees`, `departments`, and `salaries` tables:
     ```
     mysqldump -u root -p employees employees departments salaries --single-transaction > /var/lib/mysql-files/specific_tables_dump.sql
     ```
     - **Options**:
       - `-u root`: Root user.
       - `-p`: Prompts for password.
       - `employees employees departments salaries`: Specifies the database and tables.
       - `--single-transaction`: Ensures consistency without locking.
       - Output: `/var/lib/mysql-files/specific_tables_dump.sql` (~100 MB).
     - Enter the root password.
4. **Verify the Dump File**:
   ```
   ls -al /var/lib/mysql-files/specific_tables_dump.sql
   head -n 20 /var/lib/mysql-files/specific_tables_dump.sql
   ```
   - Expected: Starts with `-- MySQL dump`, `USE employees;`, and `CREATE TABLE` statements for `employees`, `departments`, and `salaries`.
5. **Set Permissions**:
   ```
   chmod 644 /var/lib/mysql-files/specific_tables_dump.sql
   chown mysql:mysql /var/lib/mysql-files/specific_tables_dump.sql
   ```

#### Step 3: Transfer the Dump to the Local Machine
1. **Exit the Container**:
   ```
   exit
   ```
2. **Copy to Host**:
   ```
   docker cp new-mysql-container:/var/lib/mysql-files/specific_tables_dump.sql ./specific_tables_dump.sql
   ```
3. **Verify Locally**:
   ```
   ls -al ./specific_tables_dump.sql
   head -n 20 ./specific_tables_dump.sql
   ```
4. **Optional: Compress for Storage**:
   ```
   gzip ./specific_tables_dump.sql
   ```
   - Creates `specific_tables_dump.sql.gz`.

#### Step 4: Restore the Tables in the Original Container
1. **Copy the Dump File**:
   - If compressed, decompress:
     ```
     gunzip ./specific_tables_dump.sql.gz
     ```
   - Copy to `mysql-container`:
     ```
     docker cp ./specific_tables_dump.sql mysql-container:/var/lib/mysql-files/specific_tables_dump.sql
     ```
2. **Access the Original Container**:
   ```
   docker exec -it mysql-container bash
   ```
3. **Set Permissions**:
   ```
   chmod 644 /var/lib/mysql-files/specific_tables_dump.sql
   chown mysql:mysql /var/lib/mysql-files/specific_tables_dump.sql
   ```
4. **Import the Dump**:
   - Log into MySQL:
     ```
     mysql -u root -p
     ```
   - Disable foreign key checks (since `salaries` references `employees`):
     ```
     SET FOREIGN_KEY_CHECKS = 0;
     ```
   - Import:
     ```
     USE employees;
     SOURCE /var/lib/mysql-files/specific_tables_dump.sql;
     ```
   - Or from the host:
     ```
     docker exec -i mysql-container mysql -u root -p employees < ./specific_tables_dump.sql
     ```
   - Re-enable foreign keys:
     ```
     SET FOREIGN_KEY_CHECKS = 1;
     ```
   - Import time: ~1-5 minutes.
5. **Verify Restoration**:
   ```
   SHOW TABLES;
   SELECT COUNT(*) FROM employees;  -- ~300,024
   SELECT COUNT(*) FROM departments;  -- ~9
   SELECT COUNT(*) FROM salaries;  -- ~2,844,047
   SELECT * FROM employees LIMIT 5;
   ```
   - Expected:
     ```
     +--------+------------+------------+-----------+--------+------------+
     | emp_no | birth_date | first_name | last_name | gender | hire_date  |
     +--------+------------+------------+-----------+--------+------------+
     | 10001  | 1953-09-02 | Georgi     | Facello   | M      | 1986-06-26 |
     ...
     ```
6. **Run Integrity Test** (Optional):
   - Copy test script:
     ```
     docker cp ./test_db/test_employees_md5.sql mysql-container:/var/lib/mysql-files/test_employees_md5.sql
     ```
   - Run:
     ```
     mysql -u root -p -t < /var/lib/mysql-files/test_employees_md5.sql
     ```
   - Expected: "OK" for `employees`, `departments`, `salaries`.

#### Step 5: Cleanup and Storage
1. **Exit and Stop (if Testing)**:
   ```
   exit
   docker stop mysql-container  # Optional
   ```
2. **Archive Dump**:
   ```
   mv ./specific_tables_dump.sql ~/backups/
   tar -czf ~/backups/specific_tables_backup.tar.gz ~/backups/specific_tables_dump.sql
   ```

#### Troubleshooting
- **Foreign Key Errors**:
  - If import fails due to dependencies:
    ```
    SET FOREIGN_KEY_CHECKS = 0;
    SOURCE /var/lib/mysql-files/specific_tables_dump.sql;
    SET FOREIGN_KEY_CHECKS = 1;
    ```
- **File Not Found**:
  - Verify paths:
    ```
    ls -al /var/lib/mysql-files/specific_tables_dump.sql
    ```
- **Slow Import**:
  - Increase memory limit:
    ```
    mysql -u root -p
    SET GLOBAL max_allowed_packet = 268435456;
    ```
- **Logs**:
  ```
  tail -n 50 /var/log/mysql/error.log
  ```

#### Notes
- **Dependencies**: The `salaries` table depends on `employees`. Dumping and restoring them together avoids foreign key issues.
- **Portability**: The dump includes `USE employees;` for correct database context.
- **Future Use**: The dump can be restored in any MySQL container by repeating Step 4.

This process successfully deletes the `employees` table, backs up specific tables, and restores them, ensuring data integrity and compatibility with the `employees` database’s schema and relationships. If issues arise, share error outputs for further assistance!


<details>
    <summary>Click to view for the particular table backup and restore</summary>

### Clarification: Why Multiple Tables Were Suggested
The previous response included `employees`, `departments`, and `salaries` in the dump for the following reasons (based on the provided document and database structure):
1. **Foreign Key Dependencies**: The `employees` table is referenced by `salaries`, `titles`, `dept_emp`, and `dept_manager` via foreign keys. Dumping related tables ensures that dependencies are preserved, especially if other tables might be affected or needed in the target environment.
2. **Context from Provided Document**: The document emphasized scenarios for dumping multiple tables (e.g., `mysqldump employees employees departments salaries`), suggesting a broader approach for robustness.
3. **Practical Scenario**: In real-world cases, restoring a single table like `employees` often requires related tables to maintain referential integrity, especially in a database like `employees` with interconnected relationships.

However, since the task specifically involves deleting and restoring only the `employees` table, we can streamline the process to focus solely on this table. We’ll use the `new-mysql-container` (where the full database exists) to dump the `employees` table and restore it in `mysql-container` where it was deleted. We’ll also handle foreign key constraints carefully, as other tables depend on `employees`.

---

### Revised Step-by-Step Guide: Delete, Back Up, and Restore the `employees` Table

#### Objective
Delete the `employees` table in the original container (`mysql-container`), back up only the `employees` table from the new container (`new-mysql-container`), and restore it in the original container, ensuring data integrity and handling foreign key dependencies.

#### Prerequisites
- **Source Container**: `mysql-container` (MySQL 8.0.43, port 3306) with the `employees` database, where the `employees` table will be deleted.
- **New Container**: `new-mysql-container` (port 3307) with the full `employees` database intact.
- **Table**: `employees` (~300,024 rows).
- **Tools**: `mysqldump`, `mysql`, Docker.
- **Disk Space**: ~20 MB for the `employees` table dump.

#### Step 1: Delete the `employees` Table in the Original Container
1. **Access MySQL in `mysql-container`**:
   ```
   docker exec -it mysql-container mysql -u root -p
   ```
   - Enter the root password.
2. **Disable Foreign Key Checks**:
   - Since `employees` is referenced by `salaries`, `titles`, `dept_emp`, and `dept_manager`, disable foreign keys:
     ```
     SET FOREIGN_KEY_CHECKS = 0;
     ```
3. **Delete the `employees` Table**:
   ```
   USE employees;
   DROP TABLE employees;
   ```
4. **Re-enable Foreign Key Checks**:
   ```
   SET FOREIGN_KEY_CHECKS = 1;
   ```
5. **Verify Deletion**:
   ```
   SHOW TABLES;
   SELECT COUNT(*) FROM employees;
   ```
   - Expect: `employees` missing from `SHOW TABLES`; `SELECT` query fails with `Table 'employees.employees' doesn't exist`.
6. **Exit MySQL**:
   ```
   exit;
   ```

#### Step 2: Back Up the `employees` Table from the New Container
1. **Access the New Container Shell**:
   ```
   docker exec -it new-mysql-container bash
   ```
   - The `new-mysql-container` has the full `employees` database, including the `employees` table.
2. **Create the Table Dump**:
   - Dump only the `employees` table:
     ```
     mysqldump -u root -p employees employees --single-transaction > /var/lib/mysql-files/employees_only.sql
     ```
     - **Options**:
       - `-u root`: Root user.
       - `-p`: Prompts for password.
       - `employees employees`: Specifies the database and table.
       - `--single-transaction`: Ensures consistency without locking.
       - Output: `/var/lib/mysql-files/employees_only.sql` (~17-20 MB).
     - Enter the root password.
3. **Verify the Dump File**:
   ```
   ls -al /var/lib/mysql-files/employees_only.sql
   head -n 20 /var/lib/mysql-files/employees_only.sql
   ```
   - Expected: Starts with `-- MySQL dump`, `USE employees;`, `CREATE TABLE employees`, and `INSERT INTO employees` statements.
4. **Set Permissions**:
   ```
   chmod 644 /var/lib/mysql-files/employees_only.sql
   chown mysql:mysql /var/lib/mysql-files/employees_only.sql
   ```

#### Step 3: Transfer the Dump to the Local Machine
1. **Exit the Container**:
   ```
   exit
   ```
2. **Copy to Host**:
   ```
   docker cp new-mysql-container:/var/lib/mysql-files/employees_only.sql ./employees_only.sql
   ```
3. **Verify Locally**:
   ```
   ls -al ./employees_only.sql
   head -n 20 ./employees_only.sql
   ```
4. **Optional: Compress for Storage**:
   ```
   gzip ./employees_only.sql
   ```
   - Creates `employees_only.sql.gz`.

#### Step 4: Restore the `employees` Table in the Original Container
1. **Copy the Dump File**:
   - If compressed, decompress:
     ```
     gunzip ./employees_only.sql.gz
     ```
   - Copy to `mysql-container`:
     ```
     docker cp ./employees_only.sql mysql-container:/var/lib/mysql-files/employees_only.sql
     ```
2. **Access the Original Container**:
   ```
   docker exec -it mysql-container bash
   ```
3. **Set Permissions**:
   ```
   chmod 644 /var/lib/mysql-files/employees_only.sql
   chown mysql:mysql /var/lib/mysql-files/employees_only.sql
   ```
4. **Import the Dump**:
   - Log into MySQL:
     ```
     mysql -u root -p
     ```
   - Disable foreign key checks (to avoid conflicts with dependent tables like `salaries`):
     ```
     SET FOREIGN_KEY_CHECKS = 0;
     ```
   - Import:
     ```
     USE employees;
     SOURCE /var/lib/mysql-files/employees_only.sql;
     ```
   - Or from the host:
     ```
     docker exec -i mysql-container mysql -u root -p employees < ./employees_only.sql
     ```
   - Re-enable foreign keys:
     ```
     SET FOREIGN_KEY_CHECKS = 1;
     ```
   - Import time: ~1-2 minutes.
5. **Verify Restoration**:
   ```
   SHOW TABLES;
   SELECT COUNT(*) FROM employees;  -- ~300,024
   SELECT * FROM employees LIMIT 5;
   ```
   - Expected:
     ```
     +--------+------------+------------+-----------+--------+------------+
     | emp_no | birth_date | first_name | last_name | gender | hire_date  |
     +--------+------------+------------+-----------+--------+------------+
     | 10001  | 1953-09-02 | Georgi     | Facello   | M      | 1986-06-26 |
     ...
     ```
6. **Run Integrity Test** (Optional):
   - Copy test script:
     ```
     docker cp ./test_db/test_employees_md5.sql mysql-container:/var/lib/mysql-files/test_employees_md5.sql
     ```
   - Run:
     ```
     mysql -u root -p -t < /var/lib/mysql-files/test_employees_md5.sql
     ```
   - Expected: "OK" for the `employees` table.

#### Step 5: Cleanup and Storage
1. **Exit and Stop (if Testing)**:
   ```
   exit
   docker stop mysql-container  # Optional
   ```
2. **Archive Dump**:
   ```
   mv ./employees_only.sql ~/backups/
   tar -czf ~/backups/employees_table_backup.tar.gz ~/backups/employees_only.sql
   ```

#### Troubleshooting
- **Foreign Key Errors**:
  - If dependent tables (e.g., `salaries`) cause issues, ensure `SET FOREIGN_KEY_CHECKS = 0` during import.
- **File Not Found**:
  - Verify paths:
    ```
    ls -al /var/lib/mysql-files/employees_only.sql
    ```
- **Slow Import**:
  - Increase memory limit:
    ```
    mysql -u root -p
    SET GLOBAL max_allowed_packet = 268435456;
    ```
- **Logs**:
  ```
  tail -n 50 /var/log/mysql/error.log
  ```

#### Notes
- **Dependencies**: The `employees` table is critical for `salaries`, `titles`, `dept_emp`, and `dept_manager`. Restoring only `employees` is safe as long as foreign key checks are disabled during import.
- **Schema Inclusion**: The dump includes `CREATE TABLE employees` to ensure the table structure is recreated correctly.
- **Future Use**: The `employees_only.sql` file can be restored in any MySQL container with an `employees` database.

This revised process focuses on the `employees` table, addressing the specific task while ensuring compatibility with the database’s dependencies. If you need to back up or restore additional tables or encounter issues, let me know!
   
</details>

</details>

---

## Step by Step guide to delete the rows in the table

To delete specific data (rows) from a table in The MySQL `employees` database (or any table within it, such as `employees`, `departments`, etc.), you can use the `DELETE` statement with a `WHERE` clause to target only the rows you want to remove. Below, I’ll explain how to delete specific data, provide examples tailored to The context, and cover best practices to ensure you don’t accidentally delete unintended data.

<details>
    <summary>Click to view Steps to Delete Specific Data from a Table</summary>

### **Steps to Delete Specific Data from a Table**

1. **Identify the Table and Rows**:
   - Determine the table (e.g., `employees`, `salaries`, etc.) from The `SHOW TABLES` output.
   - Specify the condition in the `WHERE` clause to select the rows you want to delete based on column values.

2. **Use the `DELETE` Statement**:
   - Syntax:
     ```sql
     DELETE FROM table_name WHERE condition;
     ```
   - The `WHERE` clause is critical to avoid deleting all rows in the table.

3. **Verify Before Deleting**:
   - Run a `SELECT` query with the same `WHERE` clause to preview the rows that will be deleted.
   - Example:
     ```sql
     SELECT * FROM table_name WHERE condition;
     ```

4. **Execute the Delete**:
   - Run the `DELETE` statement to remove the targeted rows.
   - Optionally, check the number of affected rows to confirm the operation.

5. **Handle Constraints**:
   - If the table has foreign key relationships (e.g., `employees` linked to `dept_emp` or `salaries`), you may need to address constraints to avoid errors.

### **Examples in The `employees` Database**

Based on The `SHOW TABLES` output (`employees`, `departments`, `dept_emp`, etc.), let’s assume you want to delete specific rows from the `employees` table. Here are some common scenarios:

#### **Example 1: Delete Employees with a Specific `emp_no`**
To delete a single employee with `emp_no = 10001`:
```sql
-- Preview the data to be deleted
SELECT * FROM employees WHERE emp_no = 10001;

-- Delete the row
DELETE FROM employees WHERE emp_no = 10001;
```

- **Check Affected Rows**:
  After running the `DELETE`, MySQL will report the number of rows affected:
  ```
  Query OK, 1 row affected (0.01 sec)
  ```

#### **Example 2: Delete Employees Hired Before a Certain Date**
To delete employees hired before January 1, 1990:
```sql
-- Preview the rows
SELECT emp_no, first_name, last_name, hire_date 
FROM employees 
WHERE hire_date < '1990-01-01';

-- Delete the rows
DELETE FROM employees 
WHERE hire_date < '1990-01-01';
```

#### **Example 3: Delete Employees by Department (Using `dept_emp`)**
If you want to delete employees from a specific department (e.g., department `d001` from the `dept_emp` table):
```sql
-- Preview employees in department d001
SELECT e.emp_no, e.first_name, e.last_name, de.dept_no
FROM employees e
JOIN dept_emp de ON e.emp_no = de.emp_no
WHERE de.dept_no = 'd001';

-- Delete from dept_emp (remove department assignment)
DELETE FROM dept_emp 
WHERE dept_no = 'd001';

-- Optionally, delete the employees entirely if they have no other department assignments
DELETE FROM employees 
WHERE emp_no IN (
    SELECT emp_no 
    FROM dept_emp 
    WHERE dept_no = 'd001'
);
```

- **Note**: Be cautious with foreign key constraints. Deleting from `employees` may fail if `emp_no` is referenced in `dept_emp`, `salaries`, or other tables unless foreign key checks are disabled or cascading deletes are configured.

#### **Example 4: Delete a Limited Number of Rows**
To delete only a specific number of rows (e.g., the first 10 employees with a certain condition):
```sql
-- Preview the rows
SELECT * FROM employees 
WHERE gender = 'M' 
LIMIT 10;

-- Delete the rows
DELETE FROM employees 
WHERE gender = 'M' 
LIMIT 10;
```

- **Note**: The `LIMIT` clause in `DELETE` is supported in MySQL but not in all SQL databases.

### **Handling Foreign Key Constraints**
The `employees` database has tables like `dept_emp`, `salaries`, and `titles` that likely reference the `employees` table via foreign keys (e.g., `emp_no`). Deleting rows from `employees` may cause errors if referenced data exists. Here’s how to handle this:

1. **Check Foreign Key Constraints**:
   - View the table’s structure to identify foreign keys:
     ```sql
     SHOW CREATE TABLE dept_emp;
     ```
     Look for `FOREIGN KEY` definitions, e.g., `FOREIGN KEY (emp_no) REFERENCES employees(emp_no)`.

2. **Option 1: Delete Dependent Rows First**:
   - Delete rows from dependent tables (e.g., `dept_emp`, `salaries`) before deleting from `employees`:
     ```sql
     -- Delete related data first
     DELETE FROM dept_emp WHERE emp_no = 10001;
     DELETE FROM salaries WHERE emp_no = 10001;
     DELETE FROM titles WHERE emp_no = 10001;

     -- Then delete from employees
     DELETE FROM employees WHERE emp_no = 10001;
     ```

3. **Option 2: Temporarily Disable Foreign Key Checks**:
   - Disable foreign key checks to allow deletion, then re-enable:
     ```sql
     SET FOREIGN_KEY_CHECKS = 0;
     DELETE FROM employees WHERE emp_no = 10001;
     SET FOREIGN_KEY_CHECKS = 1;
     ```
   - **Caution**: This can leave orphaned data in dependent tables (e.g., `dept_emp` rows with no corresponding `employees` row), so use carefully.

4. **Option 3: Use Cascading Deletes**:
   - If the foreign keys are defined with `ON DELETE CASCADE`, deleting from `employees` will automatically delete related rows in `dept_emp`, `salaries`, etc. Check the schema:
     ```sql
     SHOW CREATE TABLE dept_emp;
     ```
     If `ON DELETE CASCADE` is not set, you’d need to modify the schema (not recommended for production databases without careful planning).

### **Best Practices for Deleting Data**
1. **Always Preview with `SELECT`**:
   - Run a `SELECT` query with the same `WHERE` clause to confirm which rows will be deleted:
     ```sql
     SELECT * FROM employees WHERE condition;
     ```

2. **Use Transactions for Safety**:
   - Wrap the `DELETE` in a transaction to allow rollback if something goes wrong:
     ```sql
     START TRANSACTION;
     DELETE FROM employees WHERE emp_no = 10001;
     -- Verify the result
     SELECT * FROM employees WHERE emp_no = 10001;
     -- Commit or rollback
     COMMIT; -- or ROLLBACK;
     ```

3. **Backup Before Deleting**:
   - Create a backup of the table or database before deleting data:
     ```bash
     mysqldump -u username -p employees employees > employees_backup.sql
     ```
   - This ensures you can restore data if you accidentally delete the wrong rows.

4. **Test in a Non-Production Environment**:
   - Try the `DELETE` operation on a copy of the database first to avoid irreversible mistakes.

5. **Use Precise Conditions**:
   - Ensure the `WHERE` clause is specific enough to target only the intended rows. Without a `WHERE` clause, `DELETE FROM table_name;` will delete **all rows** in the table.

6. **Check Affected Rows**:
   - After running `DELETE`, MySQL reports the number of rows affected. Verify this matches The expectations:
     ```
     Query OK, 5 rows affected (0.02 sec)
     ```

### **Common Pitfalls to Avoid**
- **Missing `WHERE` Clause**:
  - Running `DELETE FROM employees;` without a `WHERE` clause deletes **all rows** in the table. Always double-check The query.
- **Foreign Key Errors**:
  - Deleting from `employees` may fail if `emp_no` is referenced elsewhere. Address dependent tables or disable foreign key checks as needed.
- **Incorrect Conditions**:
  - A poorly written `WHERE` clause (e.g., `WHERE hire_date > '1990-01-01'` when you meant `<=`) can delete unintended rows. Always preview with `SELECT`.
- **Performance with Large Tables**:
  - Deleting many rows from a large table (e.g., millions of rows) can be slow due to indexes and foreign key checks. Consider deleting in smaller batches using `LIMIT` or disabling indexes temporarily.

### **Example: Deleting from Multiple Tables**
If you need to delete data across related tables (e.g., remove an employee and their salary records):
```sql
-- Preview the data
SELECT e.emp_no, e.first_name, e.last_name, s.salary
FROM employees e
JOIN salaries s ON e.emp_no = s.emp_no
WHERE e.emp_no = 10001;

-- Delete from dependent table first
DELETE FROM salaries WHERE emp_no = 10001;

-- Then delete from employees
DELETE FROM employees WHERE emp_no = 10001;
```

### **Conclusion**
To delete specific data from a table in The `employees` database:
- Use `DELETE FROM table_name WHERE condition;` to target specific rows.
- Preview with `SELECT` to verify the rows to be deleted.
- Handle foreign key constraints by deleting dependent rows first, disabling checks, or ensuring cascading deletes are configured.
- Use transactions and backups for safety.
- Example for The `employees` table:
  ```sql
  START TRANSACTION;
  SELECT * FROM employees WHERE emp_no = 10001; -- Preview
  DELETE FROM dept_emp WHERE emp_no = 10001;   -- Delete dependent data
  DELETE FROM salaries WHERE emp_no = 10001;   -- Delete dependent data
  DELETE FROM employees WHERE emp_no = 10001;  -- Delete target row
  COMMIT;
  ```

If you have a specific table or condition in mind (e.g., delete employees with certain names or from a specific department), let me know, and I can provide a tailored query!

</details>

---

## Restoring only the missing rows using a Particular Table Backup and Restore

### **Key Considerations**
- **Do You Have a Backup?**: You need a recent `.sql` dump file (e.g., `employees_table.sql` or a full database dump) that contains the table’s data before the deletion occurred.
- **Can You Identify Missing Rows?**: If you don’t know which rows were deleted, you’ll need to compare the current table with the backup to find the differences.
- **Foreign Key Constraints**: The `employees` database has related tables (e.g., `dept_emp`, `salaries`). Restoring rows in one table may require restoring related rows in others to maintain data integrity.
- **Alternative Recovery Options**: If you don’t have a dump file, other methods like binary logs or point-in-time recovery may be available, depending on The MySQL configuration.

<details>
    <summary>Click to view the options and steps to restore</summary>

### **Option 1: Don’t Delete All Rows, Restore Only Missing Rows**
You can compare the current table with a dump file to identify and restore only the missing rows. This is the preferred approach to avoid overwriting existing data. Here’s how to do it:

#### **Step 1: Verify You Have a Dump File**
- Ensure you have a `.sql` dump file (e.g., `employees_table.sql`) created with `mysqldump` before the deletion. This file should contain the `CREATE TABLE` statement (optional) and `INSERT` statements for the table’s data.
- Example command that created the dump:
  ```bash
  mysqldump -u username -p employees employees > employees_table.sql
  ```

#### **Step 2: Create a Temporary Database for Comparison**
- Import the dump file into a temporary database to compare its contents with the current table.
- Create a new database (e.g., `temp_restore`):
  ```sql
  CREATE DATABASE temp_restore;
  ```
- Restore the dump file into the temporary database:
  ```bash
  mysql -u username -p temp_restore < employees_table.sql
  ```
- This creates a copy of the `employees` table (and any other dumped tables) in `temp_restore` with the data as it was at the time of the backup.

#### **Step 3: Identify Missing Rows**
- Compare the current table (e.g., `employees.employees`) with the restored table (e.g., `temp_restore.employees`) to find rows that exist in the backup but not in the current table.
- Assuming `emp_no` is the primary key in the `employees` table, you can use a query like:
  ```sql
  SELECT t.emp_no, t.first_name, t.last_name, t.birth_date, t.gender, t.hire_date
  FROM temp_restore.employees t
  LEFT JOIN employees.employees e ON t.emp_no = e.emp_no
  WHERE e.emp_no IS NULL;
  ```
  - This query finds rows in `temp_restore.employees` that don’t exist in `employees.employees` (i.e., the deleted rows).
  - The `LEFT JOIN` with `WHERE e.emp_no IS NULL` identifies rows present in the backup but missing in the current table.

- **Save the Missing Rows**:
  Export the missing rows to a new `.sql` file for restoration:
  ```bash
  mysqldump -u username -p --no-create-info --skip-lock-tables --where="emp_no IN (
    SELECT t.emp_no
    FROM temp_restore.employees t
    LEFT JOIN employees.employees e ON t.emp_no = e.emp_no
    WHERE e.emp_no IS NULL
  )" temp_restore employees > missing_employees.sql
  ```
  - The `--where` option filters the dump to include only the missing rows.
  - The resulting `missing_employees.sql` contains `INSERT` statements for the deleted rows.

#### **Step 4: Restore Only the Missing Rows**
- Import the `missing_employees.sql` file into the original database:
  ```bash
  mysql -u username -p employees < missing_employees.sql
  ```
- This inserts only the missing rows back into the `employees` table, preserving any existing data.

#### **Step 5: Handle Related Tables**
- If the deleted rows in `employees` had related data in tables like `dept_emp`, `salaries`, or `titles`, repeat the process for those tables:
  - Identify missing rows in `dept_emp`:
    ```sql
    SELECT t.emp_no, t.dept_no, t.from_date, t.to_date
    FROM temp_restore.dept_emp t
    LEFT JOIN employees.dept_emp e ON t.emp_no = e.emp_no AND t.dept_no = e.dept_no
    WHERE e.emp_no IS NULL;
    ```
  - Export and restore missing rows for `dept_emp`, `salaries`, etc., as needed.

#### **Step 6: Verify the Restore**
- Check the restored data:
  ```sql
  SELECT * FROM employees WHERE emp_no IN (
      SELECT emp_no FROM temp_restore.employees
  );
  ```
- Compare row counts before and after:
  ```sql
  SELECT COUNT(*) FROM employees.employees;
  SELECT COUNT(*) FROM temp_restore.employees;
  ```

#### **Step 7: Clean Up**
- Drop the temporary database:
  ```sql
  DROP DATABASE temp_restore;
  ```

### **Option 2: Delete All Rows and Restore Entire Table**
If identifying and restoring only missing rows is too complex or you’re confident the backup contains all necessary data, you can delete all rows and restore the entire table from the dump. However, this is less desirable because it overwrites existing data.

#### **Steps**:
1. **Backup Current Data** (for safety):
   ```bash
   mysqldump -u username -p employees employees > employees_current.sql
   ```

2. **Truncate the Table**:
   ```sql
   TRUNCATE TABLE employees;
   ```
   - Or use `DELETE`:
     ```sql
     DELETE FROM employees;
     ```
   - **Note**: `TRUNCATE` resets auto-increment counters, while `DELETE` does not. Use `DELETE` if you need to preserve the counter.

3. **Handle Foreign Keys**:
   - If `employees` is referenced by `dept_emp`, `salaries`, etc., you may need to truncate or delete related tables first or disable foreign key checks:
     ```sql
     SET FOREIGN_KEY_CHECKS = 0;
     TRUNCATE TABLE employees;
     SET FOREIGN_KEY_CHECKS = 1;
     ```

4. **Restore the Full Table**:
   ```bash
   mysql -u username -p employees < employees_table.sql
   ```

5. **Verify**:
   ```sql
   SELECT COUNT(*) FROM employees;
   ```

**Downsides**:
- Overwrites any new or modified rows added since the backup.
- Requires restoring related tables (e.g., `dept_emp`, `salaries`) to maintain consistency, which can be disruptive.
- More destructive than restoring only missing rows.

### **Option 3: Use Binary Logs (If Enabled)**
If you don’t have a dump file or want to recover without a full restore, you can use MySQL’s binary logs (if enabled) to identify and replay transactions before the deletion.

#### **Steps**:
1. **Check Binary Logging**:
   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```
   - If `log_bin` is `ON`, binary logs are enabled.

2. **Identify the Deletion**:
   - Use `mysqlbinlog` to inspect binary logs and find the `DELETE` statement(s):
     ```bash
     mysqlbinlog /path/to/binary-log.000001 > binlog.txt
     ```
   - Look for the `DELETE` query or use timestamps to narrow down the event.

3. **Replay Transactions**:
   - Extract and replay `INSERT` statements from before the deletion using `mysqlbinlog` with a time range:
     ```bash
     mysqlbinlog --start-datetime="2025-10-23 00:00:00" --stop-datetime="2025-10-24 09:00:00" /path/to/binary-log.000001 | mysql -u username -p employees
     ```
   - Adjust the time range to before the deletion occurred.

4. **Limitations**:
   - Requires binary logging to be enabled before the deletion.
   - Complex to pinpoint exact transactions without knowing the deletion time.
   - Not practical if you don’t have binary logs or if they’ve been purged.

### **Why Restoring Only Missing Rows is Better**
- **Preserves Existing Data**: New or modified rows since the backup are retained.
- **Less Disruptive**: Avoids wiping out the entire table or related tables.
- **Targeted Recovery**: Only restores what was lost, maintaining database consistency.
- **Feasible with Dump**: A `.sql` dump file makes it easy to compare and extract missing rows.

### **Challenges with Restoring Only Missing Rows**
- **Requires a Backup**: You need a `.sql` dump file from before the deletion.
- **Primary Key Needed**: Comparing tables relies on a unique identifier (e.g., `emp_no` in `employees`).
- **Foreign Key Complexity**: Restoring rows in `employees` may require restoring related rows in `dept_emp`, `salaries`, etc., to avoid constraint violations.
- **Time-Consuming**: Comparing large tables can be slow, especially for millions of rows.

### **Best Practices**
1. **Always Have Backups**:
   - Regularly create dump files:
     ```bash
     mysqldump -u username -p employees > employees_backup_$(date +%F).sql
     ```

2. **Enable Binary Logging**:
   - Enable binary logging for point-in-time recovery:
     ```sql
     SET GLOBAL log_bin = 'ON';
     ```
   - Configure in `my.cnf`:
     ```ini
     [mysqld]
     log_bin = /var/log/mysql/mysql-bin.log
     ```

3. **Use Transactions for Deletes**:
   - Wrap `DELETE` operations in transactions to allow rollback:
     ```sql
     START TRANSACTION;
     DELETE FROM employees WHERE condition;
     -- Verify
     ROLLBACK; -- or COMMIT;
     ```

4. **Test Restores**:
   - Test the restore process in a temporary database to ensure it works as expected.

5. **Check Foreign Keys**:
   - Before restoring, verify foreign key relationships:
     ```sql
     SHOW CREATE TABLE dept_emp;
     ```

### **Example in The Context**
Assume you accidentally deleted rows from the `employees` table and have a dump file `employees_table.sql`.

1. **Create Temporary Database**:
   ```sql
   CREATE DATABASE temp_restore;
   ```
   ```bash
   mysql -u username -p temp_restore < employees_table.sql
   ```

2. **Find Missing Rows**:
   ```sql
   SELECT t.emp_no, t.first_name, t.last_name
   FROM temp_restore.employees t
   LEFT JOIN employees.employees e ON t.emp_no = e.emp_no
   WHERE e.emp_no IS NULL;
   ```

3. **Export Missing Rows**:
   ```bash
   mysqldump -u username -p --no-create-info --where="emp_no IN (
       SELECT t.emp_no
       FROM temp_restore.employees t
       LEFT JOIN employees.employees e ON t.emp_no = e.emp_no
       WHERE e.emp_no IS NULL
   )" temp_restore employees > missing_employees.sql
   ```

4. **Restore Missing Rows**:
   ```bash
   mysql -u username -p employees < missing_employees.sql
   ```

5. **Handle Related Tables**:
   - Repeat for `dept_emp`, `salaries`, etc., if needed:
     ```bash
     mysqldump -u username -p --no-create-info --where="emp_no IN (
         SELECT t.emp_no
         FROM temp_restore.dept_emp t
         LEFT JOIN employees.dept_emp e ON t.emp_no = e.emp_no AND t.dept_no = e.dept_no
         WHERE e.emp_no IS NULL
     )" temp_restore dept_emp > missing_dept_emp.sql
     mysql -u username -p employees < missing_dept_emp.sql
     ```

6. **Verify**:
   ```sql
   SELECT COUNT(*) FROM employees.employees;
   ```

### **Conclusion**
You don’t need to delete all rows and restore the entire table. Instead, you can use a `.sql` dump file to:
- Compare the current table with the backup in a temporary database.
- Identify missing rows using a `LEFT JOIN` query.
- Export and restore only the missing rows with `mysqldump --where`.
This approach preserves existing data and is more precise. If binary logs are enabled, you can also explore point-in-time recovery, but a dump file is usually easier to work with. Always maintain regular backups and test The restore process to ensure data integrity.

</details>

---

## Restore the Missing Rows using the Entire DB Backup

The best approach depends on whether you can identify the affected table and the nature of the missing data (e.g., missing `emp_no` rows in `employees` or missing department assignments in `dept_emp`). Since you’re unsure which department’s data is missing, we’ll assume the `employees` table or `dept_emp` (department assignments) is affected, as these are central to department-related data. The strategy will:
1. Identify missing rows by comparing the current table with the backup.
2. Extract relevant rows from the full dump (`employees_full_dump.sql`).
3. Restore only those rows, handling foreign key constraints.

Given the complexity of pinpointing deleted rows without specific details, we’ll use a practical method to restore missing data by leveraging the backup and MySQL tools, minimizing disruption to the existing database. Below is a step-by-step guide, tailored to The containerized setup, with alternatives if needed.

### Best Approach: Restore Missing Rows Using the Full Database Dump

#### Why This Approach?
- **Leverages Existing Backup**: The `employees_full_dump.sql` contains all data (~300,024 rows in `employees`, ~2,844,047 in `salaries`, etc.), making it a reliable source to recover missing rows.
- **Preserves Existing Data**: Instead of dropping the database, we selectively insert missing rows to avoid overwriting valid data.
- **Handles Dependencies**: Disables foreign key checks to manage relationships (e.g., `dept_emp` references `employees`).
- **Scalable**: Works for any table (e.g., `employees` or `dept_emp`) once the affected table is identified.

<details>
    <summary>Click to view Prerequisites and steps</summary>

#### Prerequisites
- **Source Container**: `mysql-container` (MySQL 8.0.43, port 3306) with the `employees` database, where data is missing.
- **Backup File**: `employees_full_dump.sql` (~167-200 MB) in `~/backups/` on the host.
- **Reference Container**: `new-mysql-container` (port 3307) with the full database (optional, for comparison).
- **Tools**: `mysqldump`, `mysql`, Docker, `grep`/`sed` for processing dumps.
- **Disk Space**: ~20 MB for temporary table dumps.

#### Step 1: Identify the Affected Table and Missing Data
1. **Access MySQL in `mysql-container`**:
   ```
   docker exec -it mysql-container mysql -u root -p
   ```
   - Enter the root password.
2. **Check Table Row Counts**:
   - Compare current counts with expected counts (from the backup or schema documentation):
     ```
     USE employees;
     SELECT table_name, table_rows
     FROM information_schema.tables
     WHERE table_schema = 'employees';
     ```
     - Expected counts (approximate):
       - `employees`: ~300,024
       - `dept_emp`: ~331,603
       - `salaries`: ~2,844,047
       - `departments`: ~9
       - `dept_manager`: ~24
       - `titles`: ~443,308
   - If `employees` or `dept_emp` shows significantly fewer rows (e.g., `employees` has 200,000 rows), it’s likely affected.
3. **Check Department Data** (since you mentioned departments):
   ```
   SELECT dept_no, dept_name, (SELECT COUNT(*) FROM dept_emp WHERE dept_no = d.dept_no) AS employee_count
   FROM departments d;
   ```
   - Look for departments with unexpectedly low `employee_count` (e.g., `d001` should have ~20,000+).
4. **Assume `employees` is Affected**:
   - If unsure, focus on `employees` (primary table) or `dept_emp` (department assignments), as these are critical for department data.
5. **Exit MySQL**:
   ```
   exit
   ```

#### Step 2: Extract the `employees` Table from the Full Dump
Since the full dump (`employees_full_dump.sql`) contains all tables, we’ll extract the `employees` table’s data to restore missing rows.

1. **Copy the Full Dump to Host** (if not already there):
   ```
   ls -al ~/backups/employees_full_dump.sql
   ```
   - If compressed, decompress:
     ```
     gunzip ~/backups/employees_full_dump.sql.gz
     ```
2. **Extract `employees` Table Data**:
   - On the host, use `grep` and `sed` to isolate the `employees` table’s `INSERT` statements:
     ```
     grep -A 1000000 "Table structure for table \`employees\`" ~/backups/employees_full_dump.sql | sed -n '/INSERT INTO `employees`/p' > employees_data_only.sql
     ```
     - This creates `employees_data_only.sql` with only `INSERT INTO employees` statements (no `CREATE TABLE`).
   - Verify:
     ```
     head -n 20 employees_data_only.sql
     ```
     - Expect: Lines starting with `INSERT INTO `employees` VALUES ...`.

#### Step 3: Identify Missing Rows
1. **Copy Data to Container**:
   ```
   docker cp ./employees_data_only.sql mysql-container:/var/lib/mysql-files/employees_data_only.sql
   ```
2. **Access MySQL**:
   ```
   docker exec -it mysql-container mysql -u root -p
   ```
3. **Create a Temporary Table**:
   - Load the backup data into a temporary table to compare:
     ```
     USE employees;
     CREATE TABLE temp_employees LIKE employees;
     SET FOREIGN_KEY_CHECKS = 0;
     SOURCE /var/lib/mysql-files/employees_data_only.sql;
     SET FOREIGN_KEY_CHECKS = 1;
     ```
   - This loads all ~300,024 rows into `temp_employees`.
4. **Find Missing Rows**:
   - Identify `emp_no` values missing in `employees` but present in `temp_employees`:
     ```
     SELECT t.emp_no
     FROM temp_employees t
     LEFT JOIN employees e ON t.emp_no = e.emp_no
     WHERE e.emp_no IS NULL;
     ```
   - Save missing `emp_no` values (e.g., to a file or note count):
     ```
     SELECT t.emp_no INTO OUTFILE '/var/lib/mysql-files/missing_employees.txt'
     FROM temp_employees t
     LEFT JOIN employees e ON t.emp_no = e.emp_no
     WHERE e.emp_no IS NULL;
     ```

#### Step 4: Restore Missing Rows
1. **Extract Missing Rows from Backup**:
   - On the host, filter `employees_data_only.sql` for missing `emp_no` values (requires scripting if many rows):
     - Copy `missing_employees.txt` to host:
       ```
       docker cp mysql-container:/var/lib/mysql-files/missing_employees.txt ./missing_employees.txt
       ```
     - Use a script (e.g., Python) to filter `INSERT` statements:
       ```python
       #!/usr/bin/python3
       missing_ids = set()
       with open('missing_employees.txt', 'r') as f:
           missing_ids = {line.strip() for line in f}
       with open('employees_data_only.sql', 'r') as f, open('employees_missing_rows.sql', 'w') as out:
           for line in f:
               if any(f"('{id}'," in line for id in missing_ids):
                   out.write(line)
       ```
     - Run:
       ```
       python3 filter_missing_rows.py
       ```
     - Creates `employees_missing_rows.sql` with `INSERT` statements for missing rows.
2. **Copy to Container**:
   ```
   docker cp ./employees_missing_rows.sql mysql-container:/var/lib/mysql-files/employees_missing_rows.sql
   ```
3. **Import Missing Rows**:
   ```
   docker exec -it mysql-container mysql -u root -p
   ```
   ```
   USE employees;
   SET FOREIGN_KEY_CHECKS = 0;
   SOURCE /var/lib/mysql-files/employees_missing_rows.sql;
   SET FOREIGN_KEY_CHECKS = 1;
   ```
   - Or from host:
     ```
     docker exec -i mysql-container mysql -u root -p employees < ./employees_missing_rows.sql
     ```

#### Step 5: Verify Restoration
1. **Check Row Counts**:
   ```
   SELECT COUNT(*) FROM employees;  -- Should be ~300,024
   ```
2. **Verify Department Data**:
   ```
   SELECT d.dept_name, COUNT(de.emp_no) AS employee_count
   FROM departments d
   LEFT JOIN dept_emp de ON d.dept_no = de.dept_no
   GROUP BY d.dept_name;
   ```
   - Compare with expected counts (e.g., `Development` ~85,000).
3. **Run Integrity Test**:
   ```
   docker cp ~/test_db/test_employees_md5.sql mysql-container:/var/lib/mysql-files/test_employees_md5.sql
   mysql -u root -p -t < /var/lib/mysql-files/test_employees_md5.sql
   ```
   - Expect: "OK" for `employees`.
4. **Drop Temporary Table**:
   ```
   DROP TABLE temp_employees;
   ```

#### Step 6: Cleanup and Prevention
1. **Exit MySQL**:
   ```
   exit
   ```
2. **Archive Files**:
   ```
   mv ./employees_missing_rows.sql ~/backups/
   tar -czf ~/backups/employees_recovery.tar.gz ~/backups/employees_missing_rows.sql
   ```
3. **Prevent Future Loss**:
   - Enable binary logging:
     - Edit `/etc/mysql/my.cnf` in the container:
       ```
       docker cp mysql-container:/etc/mysql/my.cnf ./my.cnf
       # Add: log_bin = /var/log/mysql/mysql-bin.log
       docker cp ./my.cnf mysql-container:/etc/mysql/my.cnf
       docker restart mysql-container
       ```
   - Schedule daily backups:
     ```
     0 2 * * * docker exec mysql-container mysqldump -u root -pThe_PASSWORD --databases employees > /var/lib/mysql-files/backup_$(date +%Y%m%d).sql
     ```

#### Troubleshooting
- **Foreign Key Errors**:
  - Ensure `SET FOREIGN_KEY_CHECKS = 0` during import.
- **Duplicate Rows**:
  - Use `INSERT IGNORE` or check for duplicates:
    ```
    INSERT IGNORE INTO employees SELECT * FROM temp_employees;
    ```
- **File Not Found**:
  - Verify:
    ```
    ls -al /var/lib/mysql-files/employees_missing_rows.sql
    ```
- **Slow Import**:
  - Increase memory:
    ```
    SET GLOBAL max_allowed_packet = 268435456;
    ```
- **Logs**:
  - Check:
    ```
    tail -n 50 /var/log/mysql/error.log
    ```

#### Alternative: If `dept_emp` is Affected
If the missing data is in `dept_emp` (department assignments):
1. Extract `dept_emp` from the full dump:
   ```
   grep -A 1000000 "Table structure for table \`dept_emp\`" ~/backups/employees_full_dump.sql | sed -n '/INSERT INTO `dept_emp`/p' > dept_emp_data_only.sql
   ```
2. Compare and restore as in Steps 3-4, using `temp_dept_emp` table.

#### Why This is Best
- **Targeted Restoration**: Restores only missing rows, preserving existing data.
- **Uses Existing Backup**: Leverages `employees_full_dump.sql` without needing binlogs.
- **Handles Dependencies**: Disables foreign keys to avoid conflicts.
- **Verifiable**: Integrity tests ensure correctness.


</details>

---

## Monitoring MySQL DB

To monitor the number of connections (open, closed, and active) in a MySQL instance running inside a container, you can use MySQL commands, variables, and tools available within the container. Below are the steps to achieve this:

<details>
    <summary>Click to view the Steps to Achieve the Result</summary>


### 1. **Access the MySQL Container**
First, you need to access the MySQL instance running inside the container. Assuming you’re using Docker, you can enter the container’s shell:

```bash
docker exec -it <container_name> bash
```

Replace `<container_name>` with the name or ID of The MySQL container. Once inside, you can connect to the MySQL server:

```bash
mysql -u root -p
```

Enter the root password when prompted, or use another user with sufficient privileges.

### 2. **Monitor Connections Using MySQL Commands**

MySQL provides several ways to monitor connections through its system variables and status commands. Here’s how you can check open, closed, and active connections:

#### a) **Check Open Connections**
Open connections are the currently active or idle connections to the MySQL server. Use the `SHOW STATUS` command to view connection-related metrics:

```sql
SHOW STATUS LIKE 'Threads_%';
```

Key variables to focus on:
- `Threads_connected`: Number of currently open connections.
- `Threads_running`: Number of connections actively running queries (active connections).
- `Threads_cached`: Number of cached threads for quick reuse.
- `Threads_created`: Number of threads created to handle connections.

Example output:
```
+-------------------+-------+
| Variable_name     | Value |
+-------------------+-------+
| Threads_cached    | 2     |
| Threads_connected | 10    |
| Threads_created   | 15    |
| Threads_running   | 3     |
+-------------------+-------+
```

Here:
- `Threads_connected` (10) indicates open connections.
- `Threads_running` (3) indicates active connections executing queries.

#### b) **Check Total Connections (Including Closed)**
To see the total number of connections (including those that have been closed), use:

```sql
SHOW STATUS LIKE 'Connections';
```

This shows the total number of connection attempts (successful or not) since the server started.

Example output:
```
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| Connections   | 150   |
+---------------+-------+
```

The `Connections` value represents all connection attempts, including those that are now closed.

To estimate closed connections, you can calculate:
```
Closed Connections = Connections - Threads_connected
```
So, if `Connections = 150` and `Threads_connected = 10`, then closed connections ≈ 140.

#### c) **List All Current Connections**
To see details of all open connections, including user, host, and status:

```sql
SHOW PROCESSLIST;
```

Example output:
```
+----+------+-----------+------+---------+------+-------+------------------+
| Id | User | Host      | db   | Command | Time | State | Info             |
+----+------+-----------+------+---------+------+-------+------------------+
| 5  | root | localhost | test | Query   | 0    | init  | SHOW PROCESSLIST |
| 6  | user | 172.17.0.1| app  | Sleep   | 120  |       | NULL             |
+----+------+-----------+------+---------+------+-------+------------------+
```

- `Command = Query`: Indicates an active connection running a query.
- `Command = Sleep`: Indicates an idle connection.
- The number of rows in the output reflects the total open connections (`Threads_connected`).

#### d) **Check Maximum Allowed Connections**
To ensure you’re not hitting connection limits, check the maximum allowed connections:

```sql
SHOW VARIABLES LIKE 'max_connections';
```

Example output:
```
+-----------------+-------+
| Variable_name   | Value |
+-----------------+-------+
| max_connections | 151   |
+-----------------+-------+
```

If `Threads_connected` approaches `max_connections`, you may need to increase this limit or optimize The application.

### 3. **Monitor Connections in Real-Time**
To continuously monitor connections, you can run the above commands in a loop inside the container. For example:

```bash
watch -n 1 'mysql -u root -p<The_password> -e "SHOW STATUS LIKE \"Threads_%\"; SHOW STATUS LIKE \"Connections\"; SHOW PROCESSLIST;"'
```

This updates the output every second. Replace `<The_password>` with The MySQL root password.

### 4. **Use MySQL Tools for Advanced Monitoring**
If you need more detailed monitoring, consider these tools available in the MySQL container:

#### a) **MySQL Admin**
Use `mysqladmin` to check status:

```bash
mysqladmin -u root -p status
```

Example output:
```
Uptime: 3600  Threads: 10  Questions: 500  Slow queries: 0  Opens: 100  Flush tables: 1  Open tables: 50  Queries per second avg: 0.138
```

- `Threads`: Number of open connections.
- `Questions`: Total queries executed, which can help infer connection activity.

#### b) **Performance Schema**
If the Performance Schema is enabled (check with `SHOW VARIABLES LIKE 'performance_schema';`), you can query detailed connection metrics:

```sql
SELECT * FROM performance_schema.threads WHERE PROCESSLIST_ID IS NOT NULL;
```

This provides detailed thread information, including connection states.

### 5. **External Monitoring Tools**
For a more robust solution, you can use external tools to monitor MySQL connections from outside the container:

- **Prometheus + MySQL Exporter**: Deploy a MySQL exporter in The containerized environment to scrape metrics like `mysql_global_status_threads_connected` and visualize them in Prometheus or Grafana.
- **MySQL Workbench**: Connect to the MySQL instance running in the container and use its performance dashboard to monitor connections.
- **Zabbix or Nagios**: Configure these tools to monitor MySQL metrics via the container’s exposed port (default: 3306).

To expose MySQL metrics, ensure the container’s MySQL port is mapped to the host (e.g., `-p 3306:3306` in The `docker run` command).

### 6. **Automating Connection Monitoring**
To automate monitoring, you can write a script inside the container. For example, create a script to log connection stats:

```bash
#!/bin/bash
while true; do
  mysql -u root -p<The_password> -e "SHOW STATUS LIKE 'Threads_%'; SHOW STATUS LIKE 'Connections';" >> /var/log/mysql_connections.log
  sleep 10
done
```

Save this as `monitor_connections.sh`, make it executable (`chmod +x monitor_connections.sh`), and run it in the background.

### Summary of Key Metrics
- **Open Connections**: `Threads_connected` from `SHOW STATUS LIKE 'Threads_%';`
- **Active Connections**: `Threads_running` from `SHOW STATUS LIKE 'Threads_%';`
- **Closed Connections**: Derived as `Connections - Threads_connected` from `SHOW STATUS LIKE 'Connections';`
- **Detailed Connection Info**: `SHOW PROCESSLIST;`

</details>

---

## Enabling the Logs and Monitoring

<details>
    <summary>Click to view steps and guide</summary>

### Combined Guide: Verify and Enable MySQL Logs

#### Step 1: Verify Log Status
Connect to MySQL:
```bash
mysql -u root -p
```

1. **General Query Log**:
   ```sql
   SHOW VARIABLES LIKE 'general_log%';
   ```
   - Expected if disabled: `general_log = OFF`, file: `/var/lib/mysql/88e7e755eccf.log`.

2. **Slow Query Log**:
   ```sql
   SHOW VARIABLES LIKE 'slow_query_log%';
   ```
   - Expected if disabled: `slow_query_log = OFF`, file: `/var/lib/mysql/88e7e755eccf-slow.log`.

3. **Log Output**:
   ```sql
   SHOW VARIABLES LIKE 'log_output%';
   ```
   - Expected: `log_output = FILE`.

4. **Error Log**:
   ```sql
   SHOW VARIABLES LIKE 'log_error%';
   ```
   - Expected: `log_error = stderr`, `log_error_verbosity = 2`.

5. **Test Activity** (No entries if disabled):
   - General: `SELECT 1;` then `tail -f /var/lib/mysql/88e7e755eccf.log`.
   - Slow: `SELECT SLEEP(2);` then `tail -f /var/lib/mysql/88e7e755eccf-slow.log`.
   - Error: `journalctl -u mysql -f`.

#### Solution 1: Enable Logs Dynamically
```sql
SET GLOBAL general_log = 'ON';
SET GLOBAL slow_query_log = 'ON';
SET GLOBAL long_query_time = 1;
SET GLOBAL log_queries_not_using_indexes = 'ON';
SET GLOBAL log_error = '/var/lib/mysql/error.log';  -- Redirect error log
```
- Permissions:
  ```bash
  sudo chown mysql:mysql /var/lib/mysql/88e7e755eccf*.log /var/lib/mysql/error.log
  sudo chmod 660 /var/lib/mysql/88e7e755eccf*.log /var/lib/mysql/error.log
  ```

#### Step 2: Verify After Dynamic Enable
Re-run queries from Step 1:
- `general_log = ON`, `slow_query_log = ON`.
- Test: `SELECT 1;` → Check `tail -f /var/lib/mysql/88e7e755eccf.log` for entry.
- Slow: `SELECT SLEEP(2);` → Check slow log.
- Error: `tail -f /var/lib/mysql/error.log`.

#### Solution 2: Enable Persistently
Edit `/etc/mysql/my.cnf` under `[mysqld]`:
```ini
[mysqld]
general_log = 1
general_log_file = /var/lib/mysql/88e7e755eccf.log
slow_query_log = 1
slow_query_log_file = /var/lib/mysql/88e7e755eccf-slow.log
long_query_time = 1
log_queries_not_using_indexes = 1
log_output = FILE
log_error = /var/lib/mysql/error.log
log_error_verbosity = 3
max_connections = 500  -- For heavy load
```
Restart:
```bash
sudo systemctl restart mysql
```

#### Step 3: Verify After Persistent Enable
- Re-run `SHOW VARIABLES` from Step 1: All should show `ON` (except error to file).
- Test activity as in Step 1 (entries should appear in logs).

<details>
    <summary>Click to view other logs to enable and monitor</summary>

To optimize MySQL performance monitoring, I'll focus on reconfiguring the logging setup (moving relevant logs to `/var/log/mysql` for better organization and accessibility), enabling/enhancing monitoring for dropped connections, memory utilization, active connections, slow queries, and query execution times. I'll explain the rationale, suggest specific variable changes (some can be set dynamically via SQL, others require editing `my.cnf` and restarting MySQL), and provide queries to monitor these metrics ongoing.

### Step 1: Reconfigure Log File Locations
The current logs are scattered in `/var/lib/mysql/` with container-like prefixes (e.g., `88e7e755eccf.log`). We'll move the general log, slow query log, and error log to `/var/log/mysql/` for centralized management. Binlogs and relay logs are typically kept in data directories for replication/recovery, so I'll leave them unless you insist on moving (not recommended as it can break replication).

- **Dynamic Changes (via SQL, no restart needed):**
  Run these in The MySQL shell:
  ```
  SET GLOBAL general_log_file = '/var/log/mysql/general.log';
  SET GLOBAL slow_query_log_file = '/var/log/mysql/slow.log';
  ```
  - This relocates the files immediately. Ensure the directory `/var/log/mysql` exists and MySQL has write permissions (e.g., `chown mysql:mysql /var/log/mysql` and `chmod 755 /var/log/mysql` on the host).

- **Persistent Changes (edit `my.cnf` or `my.ini` under `[mysqld]` section, then restart MySQL):**
  Add/update these:
  ```
  general_log_file = /var/log/mysql/general.log
  slow_query_log_file = /var/log/mysql/slow.log
  log_error = /var/log/mysql/error.log  # Changes from stderr to a file for easier tailing/monitoring
  SHOW VARIABLES LIKE '%log%';
  ```
  - Restart: `sudo systemctl restart mysqld` (or equivalent).
  - Rationale: Centralized logs make it easier to use tools like `tail -f`, logrotate, or external monitors (e.g., ELK stack, Prometheus).

The `general_log` and `slow_query_log` are already ON, which is good. If general log becomes too verbose (it logs *all* queries), consider toggling it OFF for production and relying on slow query log + performance schema.

### Step 2: Monitor Dropped Connections
Dropped connections (aborted connects/clients) often indicate network issues, auth failures, or resource exhaustion.

- **Enable/Enhance Logging:**
  - The `log_error_verbosity = 2` is moderate; set to 3 for more details on errors (including connection issues):
    ```
    SET GLOBAL log_error_verbosity = 3;
    ```
    Add to `my.cnf` for persistence: `log_error_verbosity = 3`.
  - Ensure `log_warnings` (deprecated in newer MySQL, but if available) or use performance schema for connection events.

- **Monitoring Queries:**
  Run these periodically (e.g., via cron or monitoring tool):
  ```
  SHOW GLOBAL STATUS LIKE 'Aborted_connects';  -- Counts failed connection attempts (e.g., bad credentials, timeouts)
  SHOW GLOBAL STATUS LIKE 'Aborted_clients';   -- Counts connections closed abnormally (e.g., timeouts, max_packet_size exceeded)
  SHOW GLOBAL STATUS LIKE 'Connection_errors%';  -- Breakdown of error types (e.g., Connection_errors_max_connections)
  ```
  - To reset counters: `FLUSH STATUS;`.
  - Threshold: If Aborted_connects > 1% of Connections, investigate (e.g., increase `max_connections` if hitting limits).

- **Optimization Tips:**
  - Check `max_connections` (not in The log vars; run `SHOW VARIABLES LIKE 'max_connections';`). Default is 151; increase if needed: `SET GLOBAL max_connections = 200;` (and in `my.cnf`).
  - For timeouts: Adjust `wait_timeout` and `interactive_timeout` (defaults 28800s; lower if idle connections are an issue).

### Step 3: Monitor Current Memory Utilization
MySQL memory is dominated by InnoDB buffer pool, query cache (if enabled), and thread stacks.

- **Key Variables to Check/Set:**
  - The `innodb_log_buffer_size = 16MB` and `innodb_log_file_size = 48MB` are reasonable, but monitor usage.
  - Run `SHOW VARIABLES LIKE 'innodb_buffer_pool_size';` (not in The list; default ~128MB on small servers). To optimize, set to 50-70% of total RAM: e.g., `innodb_buffer_pool_size = 1G` in `my.cnf` (restart required).

- **Monitoring Queries:**
  ```
  SHOW GLOBAL STATUS LIKE 'Innodb_buffer_pool_pages%';  -- Utilization: (total_pages - free_pages) / total_pages * 100%
  SHOW GLOBAL STATUS LIKE 'Innodb_os_log_written';     -- Log write activity; high values may need larger log buffer
  SHOW ENGINE INNODB STATUS;                           -- Detailed InnoDB stats, including memory breakdown
  SELECT * FROM information_schema.MEMORY_GLOBAL_TOTAL;  -- Total allocated memory (MySQL 8+)
  ```
  - External: Use `top`, `htop`, or `free -h` on the host for OS-level MySQL process memory. Tools like MySQLTuner or Percona Monitoring can automate this.
  - Optimization: If utilization >80%, increase buffer pool. Watch for swapping (bad for perf).

### Step 4: Monitor Number of Active Connections
- **Monitoring Queries:**
  ```
  SHOW GLOBAL STATUS LIKE 'Threads_connected';  -- Current active connections
  SHOW GLOBAL STATUS LIKE 'Threads_running';     -- Actively running threads (non-sleeping)
  SHOW GLOBAL STATUS LIKE 'Max_used_connections';-- Peak connections since last restart
  SHOW PROCESSLIST;                              -- Detailed list of connections and what they're doing
  ```
  - If Threads_connected approaches max_connections, scale up or optimize queries.

- **Optimization:**
  - Enable performance_schema if not (run `SHOW VARIABLES LIKE 'performance_schema';`). Set in `my.cnf`: `performance_schema = ON` (restart).
  - Query: `SELECT * FROM performance_schema.threads;` for thread details.

### Step 5: Monitor Slow Queries, Query Execution Times, and Optimization
- **Enhance Slow Query Logging:**
  - The `slow_query_log = ON` is good. Set `long_query_time = 1` (seconds) to catch more:
    ```
    SET GLOBAL long_query_time = 1;
    ```
    Add to `my.cnf`: `long_query_time = 1`.
  - Enable `log_slow_extra = ON` (if MySQL 8.0.14+): Logs extra info like rows examined.
  - The `log_queries_not_using_indexes = ON` is enabled; good for spotting inefficient queries.

- **Monitor Query Times:**
  - General log (already ON) includes execution times for *all* queries, but disable in prod if overhead is high: `SET GLOBAL general_log = OFF;`.
  - Use slow query log for targeted monitoring. Parse the log with `pt-query-digest` (Percona Toolkit) for summaries.
  - For real-time: 
    ```
    SHOW GLOBAL STATUS LIKE 'Slow_queries';  -- Count of slow queries
    SELECT * FROM performance_schema.events_statements_summary_global_by_event_name WHERE EVENT_NAME LIKE 'statement/sql%' ORDER BY AVG_TIMER_WAIT DESC LIMIT 10;  -- Top slow query types (requires performance_schema)
    ```

- **General Performance Optimization and Monitoring:**
  - Enable query logging details: Set `log_slow_admin_statements = ON` and `log_slow_replica_statements = ON` in `my.cnf` if relevant.
  - Use EXPLAIN on suspect queries to check execution plans.
  - Key status queries:
    ```
    SHOW GLOBAL STATUS LIKE 'Handler_read%';  -- Index usage; high Handler_read_rnd_next means full table scans
    SHOW GLOBAL STATUS LIKE 'Created_tmp%';    -- Temp tables; high on disk means increase tmp_table_size
    SHOW GLOBAL STATUS LIKE 'Select%';         -- Query types; optimize full joins/scans
    ```
  - Tools: Install MySQL Workbench, phpMyAdmin, or Prometheus + Grafana for dashboards. Run MySQLTuner script for automated suggestions.
  - Binlog/InnoDB Logs: The settings look fine (e.g., `sync_binlog=1` for durability). If high write load, consider `innodb_flush_log_at_trx_commit=2` for perf (less safe).

Apply changes gradually, monitor with `SHOW GLOBAL STATUS;` before/after, and test in a non-prod environment.
   
</details>
   
</details>

---

## Step-by-Step Guide to Setting Up MySQL Proxy

<details>
    <summary>Click to view Step-by-Step Guide to Setting Up MySQL Proxy (Using ProxySQL) with Your Running MySQL Container</summary>

### Step-by-Step Guide to Setting Up MySQL Proxy (Using ProxySQL) with Your Running MySQL Container

I'll explain this in detail as a beginner-friendly guide, assuming you're using ProxySQL (a popular MySQL proxy for load balancing, query routing, and monitoring) with your existing MySQL 8.0 container (`mysql-container`). We'll follow your specified approach: pulling the ProxySQL image, creating a configuration file locally (`proxysql.cnf`), running the ProxySQL container, and then copying the configuration file into the running container using `docker cp`. After copying, we'll reload the configuration inside ProxySQL without restarting the container.

This setup places ProxySQL as an intermediary between clients (e.g., your `sysbench` container or applications) and your MySQL container. Clients will connect to ProxySQL on port 6033, and ProxySQL will forward requests to MySQL on port 3306.

**Assumptions:**
- Your MySQL container (`mysql-container`) is running on the `mysql-sysbench-net` Docker network.
- MySQL root user/password: `root`/`root` (adjust if different).
- You're working on a host with Docker installed (e.g., your local machine).
- We'll use the official `proxysql/proxysql` image.
- Commands are run in your host terminal (e.g., Bash on macOS/Linux).

If any step fails, check `docker logs <container_name>` for errors.

#### Step 1: Pull the ProxySQL Docker Image
First, download the official ProxySQL image from Docker Hub. This ensures you have the latest version compatible with MySQL 8.0.

1. Open your terminal.
2. Run the following command:
   ```
   docker pull proxysql/proxysql:latest
   ```
   - This pulls the image (size ~200MB). Output will show progress and confirm "latest" tag.
   - Verify: Run `docker images | grep proxysql` to see the image listed.

#### Step 2: Create the ProxySQL Configuration File Locally
ProxySQL needs a configuration file (`proxysql.cnf`) to define how it connects to your MySQL backend, handles users, and listens for connections.

1. Create a directory for the config (optional but organized):
   ```
   mkdir proxysql-config
   cd proxysql-config
   ```

2. Use a text editor (e.g., Vim, Nano, or VS Code) to create `proxysql.cnf`. For example, with Vim:
   ```
   vim proxysql.cnf
   ```
   - Press `i` to insert mode, paste the following configuration, then `Esc` + `:wq` to save and exit.

   Configuration content (copy-paste this):
   ```
   # proxysql.cnf - Basic setup for single MySQL backend
   datadir="/var/lib/proxysql"

   admin_variables=
   {
       admin_credentials="admin:admin"  # ProxySQL admin login (change for security)
       mysql_ifaces="0.0.0.0:6032"      # Admin interface port
       refresh_interval=2000
   }

   mysql_variables=
   {
       threads=4                        # Number of worker threads (adjust based on your CPU cores, e.g., run 'nproc' in container)
       max_connections=2048             # Max client connections (higher than MySQL's 151)
       default_query_delay=0
       default_query_timeout=36000000
       have_compress=true
       poll_timeout=2000
       interfaces="0.0.0.0:6033"        # Proxy listener port (clients connect here)
       default_schema="employees"       # Default database (your employees DB)
       stacksize=1048576
       server_version="8.0.43"          # Match your MySQL version
       connect_timeout_server=10000
       monitor_history=60000
       monitor_connect_interval=200000
       monitor_ping_interval=200000
       ping_interval_server_msec=10000
       commands_stats=true
       sessions_sort=true
   }

   mysql_servers=
   (
       { address="mysql-container", port=3306, hostgroup=10 }  # Your MySQL container as backend
   )

   mysql_users=
   (
       { username="root", password="root", default_hostgroup=10 }  # MySQL user for ProxySQL to connect
   )

   mysql_query_rules=
   (
       # Optional: Route queries to employees DB tables
       { match_pattern="^SELECT .* FROM employees\\..*", destination_hostgroup=10, apply=1 }
   )
   ```

   - **Key explanations**:
     - `admin_credentials`: Login for ProxySQL's admin interface.
     - `interfaces`: ProxySQL listens on port 6033 for MySQL clients.
     - `mysql_servers`: Points to your MySQL container by name (resolved via Docker network).
     - `mysql_users`: ProxySQL uses this to authenticate and connect to MySQL.
     - Customize: Change passwords, threads, or add rules for specific queries.

3. Verify the file exists:
   ```
   ls -l proxysql.cnf
   ```

#### Step 3: Run the ProxySQL Container
Now, start the ProxySQL container without the configuration (we'll copy it in later). We'll connect it to the same network as your MySQL container.

1. Run the container:
   ```
   docker run -d \
     --name proxysql \
     --network mysql-sysbench-net \
     -p 6033:6033 \
     -p 6032:6032 \  # Expose admin port to host (optional for monitoring)
     proxysql/proxysql:latest
   ```
   - `-d`: Run in background.
   - `--network mysql-sysbench-net`: Allows communication with `mysql-container`.
   - `-p 6033:6033`: Expose proxy port to host (clients connect to `localhost:6033` or container name).
   - `-p 6032:6032`: Expose admin port (for Step 5).

2. Verify it's running:
   ```
   docker ps | grep proxysql
   ```
   - Should show status "Up".
   - Check logs: `docker logs proxysql` (it may warn about missing config, but that's okay for now).

#### Step 4: Copy the Configuration File into the Running Container
Use `docker cp` to copy your local `proxysql.cnf` into the container's `/etc/proxysql/` directory (ProxySQL's default config path).

1. Ensure you're in the directory with `proxysql.cnf` (e.g., `./proxysql-config`).
2. Run the copy command:
   ```
   docker cp proxysql.cnf proxysql:/etc/proxysql/proxysql.cnf
   ```

3. Verify the copy inside the container:
   ```
   docker exec -it proxysql bash
   ls -l /etc/proxysql/proxysql.cnf  # Should show the file
   cat /etc/proxysql/proxysql.cnf    # Optional: Check content
   exit
   ```

#### Step 5: Reload the Configuration in ProxySQL
ProxySQL doesn't auto-reload configs; we need to use its admin interface to load the new config at runtime (no restart needed).

1. Connect to ProxySQL's admin interface:
   ```
   docker exec -it proxysql mysql -u admin -padmin -h127.0.0.1 -P6032 --prompt='Admin> '
   ```
   - User/password: `admin`/`admin` (from config; change in production).
   - If on host port 6032: `mysql -u admin -padmin -h localhost -P6032`.

2. Inside the admin console (prompt: `Admin>`), load the config:
   ```
   Admin> LOAD MYSQL SERVERS FROM CONFIG;
   Admin> LOAD MYSQL USERS FROM CONFIG;
   Admin> LOAD MYSQL QUERY RULES FROM CONFIG;
   Admin> LOAD MYSQL VARIABLES FROM CONFIG;
   Admin> LOAD ADMIN VARIABLES FROM CONFIG;
   Admin> SAVE MYSQL SERVERS TO DISK;
   Admin> SAVE MYSQL USERS TO DISK;
   Admin> SAVE MYSQL QUERY RULES TO DISK;
   Admin> SAVE MYSQL VARIABLES TO DISK;
   Admin> SAVE ADMIN VARIABLES TO DISK;
   Admin> LOAD MYSQL SERVERS TO RUNTIME;
   Admin> LOAD MYSQL USERS TO RUNTIME;
   Admin> LOAD MYSQL QUERY RULES TO RUNTIME;
   Admin> LOAD MYSQL VARIABLES TO RUNTIME;
   Admin> LOAD ADMIN VARIABLES TO RUNTIME;
   ```

3. Verify the backend (your MySQL container):
   ```
   Admin> SELECT * FROM mysql_servers;
   ```
   - Should show `mysql-container` as a hostgroup=10 entry.

4. Exit: `exit;`

#### Step 6: Test the ProxySQL Setup
Now, test by connecting through the proxy.

1. From your host:
   ```
   mysql -h localhost -P6033 -u root -proot -e "SHOW DATABASES;"
   ```
   - Should list databases like `employees` (proxied via ProxySQL).

2. From your `sysbench` container (enter it with `docker exec -it ee89c8bb871e bash`):
   ```
   mysql -h proxysql -P6033 -u root -proot -e "SELECT 1 FROM employees.employees LIMIT 1;"
   ```

3. Update sysbench commands to use the proxy:
   ```
   sysbench oltp_read_write --mysql-host=proxysql --mysql-port=6033 --mysql-user=root --mysql-password=root --mysql-db=employees --threads=128 --time=60 run
   ```

#### Step 7: Monitoring and Cleanup
- Monitor ProxySQL: `docker logs proxysql` or use the admin console (`SELECT * FROM stats_mysql_connection_pool;`).
- Stop/Remove: `docker stop proxysql; docker rm proxysql`.
- Security: Change admin/root passwords in `proxysql.cnf` for production.
- Troubleshooting: If ProxySQL can't connect to MySQL, check MySQL permissions (`GRANT ALL ON *.* TO 'root'@'%';`) and network (`docker network inspect mysql-sysbench-net`).

This completes the setup. Now, all traffic goes through ProxySQL, and you can add advanced features like query caching by editing `proxysql.cnf` and reloading. If you encounter errors (e.g., during copy or reload), share the output for debugging!
   
</details>

---

## MySQL Performance Benchmarking with Sysbench (Docker Setup)

This guide describes how to benchmark a MySQL database using **Sysbench** inside Docker containers connected through a custom Docker network.

<details>
    <summary>Click to view the guide</summary>

## **1. Create a Docker Network**

Create an isolated Docker network to allow the MySQL and Sysbench containers to communicate directly.

```bash
docker network create mysql-sysbench-net
```

Verify the network:

```bash
docker network ls
```

---

## **2. Connect MySQL Container to the Network**

Assuming you already have a running MySQL container named `mysql-container`:

```bash
docker network connect mysql-sysbench-net mysql-container
```

This ensures the Sysbench container can access MySQL using the container name `mysql-container` as hostname.

---

## **3. Create a Sysbench Dockerfile**

Create a `Dockerfile` for a lightweight Sysbench image.

```dockerfile
# Use Debian Bullseye as the base image (ARM-compatible)
FROM debian:bullseye

# Install Sysbench and MySQL client
RUN apt-get update && \
    apt-get install -y sysbench default-mysql-client && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Keep container alive
CMD ["tail", "-f", "/dev/null"]
```

Build the image:

```bash
docker build -t sysbench:debian .
```

---

## **4. Run the Sysbench Container**

Start a container from the built image and attach it to the same Docker network.

```bash
docker run -it -d --name sysbench-debian --network mysql-sysbench-net sysbench:debian
```

Verify container is running:

```bash
docker ps
```

---

## **5. Access the Sysbench Container**

Enter the running container:

```bash
docker exec -it sysbench-debian bash
```

Check installed versions:

```bash
sysbench --version
mysql --version
```

Check CPU thread count (for parallel test threads):

```bash
nproc
```

---

## **6. Create Sysbench Lua Script**

Create a Lua script for random read operations on the `employees` table.

```bash
cat > /employees_read.lua << 'EOF'
function thread_init()
   drv = sysbench.sql.driver()
   con = drv:connect()
end

function event()
   con:query("SELECT * FROM employees.employees WHERE emp_no = " .. sysbench.rand.uniform(10001, 500000))
end

function thread_done()
   con:disconnect()
end
EOF
```

---

## **7. Run the Sysbench Benchmark**

Run the read-only benchmark for 60 seconds, using all available CPU threads.

```bash
sysbench /employees_read.lua \
  --mysql-host=mysql-container \
  --mysql-user=root \
  --mysql-password=rootpass \
  --mysql-db=employees \
  --threads=$(nproc) \
  --time=60 \
  --report-interval=10 \
  run
```

### Key parameters explained:

| Parameter           | Description                                       |
| ------------------- | ------------------------------------------------- |
| `--mysql-host`      | Hostname of MySQL container (from Docker network) |
| `--mysql-user`      | MySQL username                                    |
| `--mysql-password`  | MySQL password                                    |
| `--mysql-db`        | Target database name                              |
| `--threads`         | Number of concurrent threads (based on CPU cores) |
| `--time`            | Duration of the benchmark in seconds              |
| `--report-interval` | Interval (in seconds) for progress reports        |

---

## **8. Monitor MySQL Connection Stats**

After the test, connect to your MySQL instance and run:

```sql
SHOW GLOBAL STATUS LIKE 'Aborted_%';
SHOW STATUS LIKE 'Connections';
SHOW VARIABLES LIKE 'max_connections';
```

### Interpretation:

| Metric             | Meaning                                                             |
| ------------------ | ------------------------------------------------------------------- |
| `Aborted_connects` | Failed connection attempts (authentication errors, resource limits) |
| `Aborted_clients`  | Connections terminated unexpectedly by clients                      |
| `Connections`      | Total number of connection attempts (successful + failed)           |
| `max_connections`  | The configured upper limit for concurrent connections               |

---

## **Summary**

| Step | Description                       |
| ---- | --------------------------------- |
| 1    | Create Docker network             |
| 2    | Connect MySQL to network          |
| 3    | Build Sysbench image              |
| 4    | Run Sysbench container            |
| 5    | Verify setup                      |
| 6    | Create benchmark Lua script       |
| 7    | Run benchmark                     |
| 8    | Analyze MySQL performance metrics |

</details>

---

## ProxySQL Setup with MySQL and Sysbench (Docker)

<details>
    <summary>Click to view the guide and setup</summary>

## **1. Prerequisites**

Ensure you already have:

* A **Docker network** (`mysql-sysbench-net`)
* A running **MySQL container** named `mysql-container`
* The **Sysbench** container configured (from the previous guide)

Check network:

```bash
docker network ls
```

---

## **2. Pull the ProxySQL Docker Image**

```bash
docker pull proxysql/proxysql:latest
```

---

## **3. Create ProxySQL Configuration**

Create a configuration file named **`proxysql.cnf`**:

```bash
vim proxysql.cnf
```

Paste the following configuration:

```ini
# proxysql.cnf - Basic setup for single MySQL backend

datadir="/var/lib/proxysql"

admin_variables=
{
    admin_credentials="admin:admin"           # ProxySQL admin login (change for security)
    mysql_ifaces="0.0.0.0:6032"               # Admin interface port
    refresh_interval=2000
}

mysql_variables=
{
    threads=4                                 # Worker threads (adjust using 'nproc')
    max_connections=2048                      # Max client connections
    default_query_delay=0
    default_query_timeout=36000000
    have_compress=true
    poll_timeout=2000
    interfaces="0.0.0.0:6033"                 # Proxy listener port (Sysbench connects here)
    default_schema="employees"                # Default DB
    stacksize=1048576
    server_version="8.0.43"                   # Match MySQL version
    connect_timeout_server=10000
    monitor_history=60000
    monitor_connect_interval=200000
    monitor_ping_interval=200000
    ping_interval_server_msec=10000
    commands_stats=true
    sessions_sort=true
}

mysql_servers=
(
    { address="mysql-container", port=3306, hostgroup=10 }  # MySQL backend
)

mysql_users=
(
    { username="root", password="root", default_hostgroup=10 }  # ProxySQL connects using this user
)

mysql_query_rules=
(
    # Optional routing rule for employees DB
    { match_pattern="^SELECT .* FROM employees\\..*", destination_hostgroup=10, apply=1 }
)
```

---

## **4. Run ProxySQL Container**

Start ProxySQL and attach it to the same network as MySQL and Sysbench:

```bash
docker run -d --name proxysql \
  --network mysql-sysbench-net \
  -p 6033:6033 -p 6032:6032 \
  proxysql/proxysql:latest
```

Check container status:

```bash
docker ps
```

View logs:

```bash
docker logs proxysql
```

---

## **5. Copy Configuration File into Container**

```bash
docker cp proxysql.cnf proxysql:/etc/proxysql.cnf
```

(Optional) verify:

```bash
docker exec -it proxysql ls -l /etc/proxysql.cnf
docker exec -it proxysql cat /etc/proxysql.cnf
```

---

## **6. Load Configuration into ProxySQL Runtime**

Access ProxySQL’s **admin interface**:

```bash
docker exec -it proxysql mysql -u admin -padmin -h 127.0.0.1 -P6032 --prompt='Admin> '
```

Execute the following commands:

```sql
Admin> LOAD MYSQL SERVERS FROM CONFIG;
Admin> LOAD MYSQL USERS FROM CONFIG;
Admin> LOAD MYSQL QUERY RULES FROM CONFIG;
Admin> LOAD MYSQL VARIABLES FROM CONFIG;
Admin> LOAD ADMIN VARIABLES FROM CONFIG;

Admin> SAVE MYSQL SERVERS TO DISK;
Admin> SAVE MYSQL USERS TO DISK;
Admin> SAVE MYSQL QUERY RULES TO DISK;
Admin> SAVE MYSQL VARIABLES TO DISK;
Admin> SAVE ADMIN VARIABLES TO DISK;

Admin> LOAD MYSQL SERVERS TO RUNTIME;
Admin> LOAD MYSQL USERS TO RUNTIME;
Admin> LOAD MYSQL QUERY RULES TO RUNTIME;
Admin> LOAD MYSQL VARIABLES TO RUNTIME;
Admin> LOAD ADMIN VARIABLES TO RUNTIME;
```

Validate loaded configuration:

```sql
Admin> SELECT * FROM mysql_servers;
Admin> SELECT * FROM runtime_mysql_servers;
```

---

## **7. Configure MySQL to Allow ProxySQL Access**

Enter your MySQL container:

```bash
docker exec -it mysql-container bash
```

Grant access to the `root` user from all hosts (or just ProxySQL):

```bash
mysql -u root -p -e "GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION; FLUSH PRIVILEGES;"
```

Verify connectivity:

```bash
mysql -h localhost -P3306 -u root -p -e "SHOW DATABASES;"
```

---

## **8. Test ProxySQL Connection from Sysbench**

In your Sysbench container (`sysbench-debian`):

```bash
docker exec -it sysbench-debian bash
```

Test connection via ProxySQL:

```bash
mysql -h proxysql -P6033 -u root -p
```

Then run a Sysbench benchmark **through ProxySQL**:

```bash
sysbench /employees_read.lua \
  --mysql-host=proxysql \
  --mysql-port=6033 \
  --mysql-user=root \
  --mysql-password=root \
  --mysql-db=employees \
  --threads=$(nproc) \
  --time=60 \
  --report-interval=10 \
  run
```

---

## **9. Monitor ProxySQL Metrics**

You can monitor connection and query stats via the **admin interface**:

```sql
Admin> SELECT * FROM stats_mysql_connection_pool;
Admin> SELECT * FROM stats_mysql_query_digest;
Admin> SELECT * FROM stats_mysql_commands_counters;
```

---

## **Summary**

| Component               | Purpose                   | Port |
| ----------------------- | ------------------------- | ---- |
| **MySQL**               | Backend database          | 3306 |
| **ProxySQL (Admin)**    | Management interface      | 6032 |
| **ProxySQL (Listener)** | Client-facing MySQL proxy | 6033 |
| **Sysbench**            | Load-testing client       | —    |

**Workflow:**

```
Sysbench (client)
      ↓
 ProxySQL (port 6033)
      ↓
 MySQL (port 3306)
```

   
</details>

---

Perfect 👍 — here’s the **extended, production-ready documentation** with a clear **architecture diagram** and **connection-pool tuning guidance** for ProxySQL.

---

## ProxySQL Setup with MySQL and Sysbench (Docker)

<details>
    <summary>Click to view the Architecture Overview, Steps and Guide</summary>

### 🧩 **Architecture Overview**

```
                 +---------------------------+
                 |     Sysbench Container    |
                 |  (Load Generator Client)  |
                 +-------------+-------------+
                               |
                               |  TCP 6033 (ProxySQL listener)
                               v
                 +---------------------------+
                 |       ProxySQL Layer      |
                 |  Port 6033 -> MySQL Proxy |
                 |  Port 6032 -> Admin Panel |
                 +-------------+-------------+
                               |
                               |  TCP 3306 (MySQL port)
                               v
                 +---------------------------+
                 |       MySQL Container     |
                 |  Backend DB: employees    |
                 +---------------------------+
```

**Docker Network:** `mysql-sysbench-net`
All three containers communicate internally through this bridge network.

---

### **Setup Steps**

#### **1. Create Docker Network**

```bash
docker network create mysql-sysbench-net
```

#### **2. Connect MySQL Container**

```bash
docker network connect mysql-sysbench-net mysql-container
```

#### **3. ProxySQL Configuration File**

Create `proxysql.cnf`:

```ini
datadir="/var/lib/proxysql"

admin_variables=
{
    admin_credentials="admin:admin"
    mysql_ifaces="0.0.0.0:6032"
    refresh_interval=2000
}

mysql_variables=
{
    threads=4
    max_connections=2048
    interfaces="0.0.0.0:6033"
    default_schema="employees"
    server_version="8.0.43"
    commands_stats=true
    sessions_sort=true
}

mysql_servers=
(
    { address="mysql-container", port=3306, hostgroup=10 }
)

mysql_users=
(
    { username="root", password="root", default_hostgroup=10 }
)

mysql_query_rules=
(
    { match_pattern="^SELECT .* FROM employees\\..*", destination_hostgroup=10, apply=1 }
)
```

#### **4. Run ProxySQL Container**

```bash
docker run -d --name proxysql \
  --network mysql-sysbench-net \
  -p 6033:6033 -p 6032:6032 \
  proxysql/proxysql:latest
```

Copy config into container:

```bash
docker cp proxysql.cnf proxysql:/etc/proxysql.cnf
```

---

### **Load ProxySQL Configuration**

Access admin console:

```bash
docker exec -it proxysql mysql -u admin -padmin -h 127.0.0.1 -P6032 --prompt='Admin> '
```

Run:

```sql
Admin> LOAD MYSQL SERVERS FROM CONFIG;
Admin> LOAD MYSQL USERS FROM CONFIG;
Admin> LOAD MYSQL QUERY RULES FROM CONFIG;
Admin> LOAD MYSQL VARIABLES FROM CONFIG;
Admin> LOAD ADMIN VARIABLES FROM CONFIG;

Admin> SAVE MYSQL SERVERS TO DISK;
Admin> SAVE MYSQL USERS TO DISK;
Admin> SAVE MYSQL QUERY RULES TO DISK;
Admin> SAVE MYSQL VARIABLES TO DISK;
Admin> SAVE ADMIN VARIABLES TO DISK;

Admin> LOAD MYSQL SERVERS TO RUNTIME;
Admin> LOAD MYSQL USERS TO RUNTIME;
Admin> LOAD MYSQL QUERY RULES TO RUNTIME;
Admin> LOAD MYSQL VARIABLES TO RUNTIME;
Admin> LOAD ADMIN VARIABLES TO RUNTIME;

Admin> SELECT * FROM runtime_mysql_servers;
```

---

### **MySQL Configuration**

Grant root user remote access from ProxySQL:

```bash
docker exec -it mysql-container bash
mysql -u root -p -e "GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION; FLUSH PRIVILEGES;"
```

---

### **Run Sysbench Through ProxySQL**

Access Sysbench container:

```bash
docker exec -it sysbench-debian bash
```

Run a benchmark (reads via ProxySQL):

```bash
sysbench /employees_read.lua \
  --mysql-host=proxysql \
  --mysql-port=6033 \
  --mysql-user=root \
  --mysql-password=root \
  --mysql-db=employees \
  --threads=$(nproc) \
  --time=60 \
  --report-interval=10 \
  run
```

---

### **Monitoring Metrics**

Inside ProxySQL admin interface (`port 6032`):

```sql
Admin> SELECT * FROM stats_mysql_connection_pool;
Admin> SELECT * FROM stats_mysql_query_digest;
Admin> SELECT * FROM stats_mysql_commands_counters;
Admin> SELECT * FROM mysql_server_connect_log ORDER BY time_start_us DESC LIMIT 5;
```

---

### ⚡ **Tuning Connection Pool Parameters**

ProxySQL can maintain persistent connections to MySQL and reuse them for new clients, improving throughput and latency.

Below are recommended tuning variables for heavy load testing:

| Parameter                               | Description                                  | Recommended                    |
| --------------------------------------- | -------------------------------------------- | ------------------------------ |
| `mysql-threads`                         | Number of worker threads                     | Set to `nproc` (e.g. 4–8)      |
| `mysql-max_connections`                 | Max client connections                       | 2–4× MySQL’s `max_connections` |
| `mysql-default_query_timeout`           | Query timeout in ms                          | `36000000` (10h)               |
| `mysql-ping_interval_server_msec`       | Health check frequency                       | 10000 (10s)                    |
| `mysql-monitor_connect_interval`        | Backend connectivity test interval           | 200000 (200s)                  |
| `mysql-monitor_ping_interval`           | Backend ping interval                        | 200000 (200s)                  |
| `mysql-monitor_history`                 | Monitor history retention                    | 60000                          |
| `mysql-connection_max_age_ms`           | Max age of pooled connections before refresh | 1800000 (30m)                  |
| `mysql-max_transactions_per_connection` | Reuse limit per connection                   | 10000                          |
| `mysql-session_idle_timeout`            | Idle session close timeout                   | 300000 (5m)                    |

To modify at runtime:

```sql
Admin> UPDATE global_variables SET variable_value='1800000' WHERE variable_name='mysql-connection_max_age_ms';
Admin> LOAD MYSQL VARIABLES TO RUNTIME;
Admin> SAVE MYSQL VARIABLES TO DISK;
```

---

### **Verification Flow**

| Action                      | Command                                                                          |
| --------------------------- | -------------------------------------------------------------------------------- |
| Check backend status        | `Admin> SELECT * FROM mysql_servers;`                                            |
| Check connection pool stats | `Admin> SELECT * FROM stats_mysql_connection_pool;`                              |
| Check live traffic          | `Admin> SELECT * FROM stats_mysql_query_digest ORDER BY sum_time DESC LIMIT 10;` |
| Validate MySQL privileges   | `mysql -h mysql-container -u root -p`                                            |

---

### 🧠 **Performance Notes**

* **ProxySQL** helps handle **burst connections** by pooling MySQL sessions.
* **Sysbench** results may show higher TPS and lower connection latency compared to direct MySQL connection.
* It is ideal for production-like testing where connection reuse and load balancing behavior must be simulated.

</details>

---

## 📊 **Performance Analysis and Interpretation**

After running your Sysbench benchmark through **ProxySQL**, you will get **reports of TPS, latency, and connection metrics**. Understanding these is crucial to identify bottlenecks and tune the system.

<details>
    <summary>Click to view Steps and Guide</summary>

### **1. Key Metrics from Sysbench**

#### Example Sysbench Output Snippet:

```
Threads started: 8
Time limit: 60s
...
transactions: 12000  (200.00 per sec)
queries: 24000       (400.00 per sec)
ignored errors: 0
reconnects: 0
```

| Metric                   | Meaning                                          | Interpretation                                                |
| ------------------------ | ------------------------------------------------ | ------------------------------------------------------------- |
| `transactions/sec` (TPS) | Completed transactions per second                | Higher is better; indicates throughput                        |
| `queries/sec`            | Queries per second                               | Sum of all SQL statements executed; proxy may reduce latency  |
| `avg latency`            | Average time per query (ms)                      | Lower latency indicates efficient pooling & connection reuse  |
| `min/max latency`        | Min/max response time                            | Spikes in max latency may indicate contention or slow queries |
| `reconnects`             | Number of new connections due to pool exhaustion | Should ideally be 0 with ProxySQL pooling                     |
| `ignored errors`         | Failed queries                                   | Non-zero indicates issues in SQL, permissions, or load        |

---

### **2. MySQL Status Variables to Monitor**

```sql
SHOW GLOBAL STATUS LIKE 'Aborted_%';
SHOW STATUS LIKE 'Connections';
SHOW VARIABLES LIKE 'max_connections';
```

| Variable           | Meaning                          | What to Watch                                                      |
| ------------------ | -------------------------------- | ------------------------------------------------------------------ |
| `Aborted_clients`  | Clients disconnected improperly  | High values indicate client timeouts or crashes                    |
| `Aborted_connects` | Failed connection attempts       | Should be minimal; ProxySQL helps reduce these                     |
| `Connections`      | Total connection attempts        | Compare with `max_connections` to see if limits are reached        |
| `max_connections`  | MySQL max concurrent connections | Ensure ProxySQL `max_connections` does not exceed this by too much |

---

### **3. ProxySQL Stats to Analyze**

Inside ProxySQL admin interface:

```sql
Admin> SELECT * FROM stats_mysql_connection_pool;
Admin> SELECT * FROM stats_mysql_query_digest ORDER BY sum_time DESC LIMIT 10;
Admin> SELECT * FROM stats_mysql_commands_counters;
```

| Table                           | Key Metrics                                                | Interpretation                                                      |
| ------------------------------- | ---------------------------------------------------------- | ------------------------------------------------------------------- |
| `stats_mysql_connection_pool`   | `srv_hostgroup`, `hostgroup`, `status`, `used_connections` | High `used_connections` vs `max_connections` → pool may need tuning |
| `stats_mysql_query_digest`      | Query `sum_time`, `count_star`, `avg_time`                 | Shows which queries are slow; identify bottlenecks                  |
| `stats_mysql_commands_counters` | Counters for SELECT, INSERT, UPDATE                        | Helps analyze read/write load distribution                          |

---

### **4. Common Performance Bottlenecks**

| Bottleneck          | Symptom                                 | Recommended Fix                                       |
| ------------------- | --------------------------------------- | ----------------------------------------------------- |
| Connection limits   | High `reconnects` or `Aborted_connects` | Increase ProxySQL pool, check MySQL `max_connections` |
| Slow queries        | High `avg_time` or `max_time`           | Optimize SQL, add indexes, adjust ProxySQL rules      |
| CPU saturation      | Low TPS despite low latency             | Reduce threads or increase CPU resources              |
| ProxySQL saturation | High `used_connections`                 | Increase `threads` or tune connection pool parameters |

---

### **5. Suggested Tuning Steps**

1. **ProxySQL Thread Count**

   ```sql
   Admin> SET mysql-threads = 8;
   Admin> LOAD MYSQL VARIABLES TO RUNTIME;
   ```

2. **Connection Pool Size**

   ```sql
   Admin> UPDATE global_variables SET variable_value=4096 WHERE variable_name='mysql-max_connections';
   Admin> LOAD MYSQL VARIABLES TO RUNTIME;
   ```

3. **Connection Lifetime and Idle Timeout**

   ```sql
   Admin> UPDATE global_variables SET variable_value=1800000 WHERE variable_name='mysql-connection_max_age_ms';
   Admin> UPDATE global_variables SET variable_value=300000 WHERE variable_name='mysql-session_idle_timeout';
   Admin> LOAD MYSQL VARIABLES TO RUNTIME;
   ```

4. **Query Routing / Rules**
   Optimize query rules for heavy-read tables (e.g., route read-only queries to dedicated hostgroup).

---

### **6. Practical Example Analysis**

If a 60-second read benchmark shows:

```
TPS: 2000
Avg Latency: 25 ms
Reconnections: 0
Aborted_connects: 5
```

**Interpretation:**

* TPS is decent → ProxySQL pooling is effective
* Latency is acceptable → queries served quickly
* 0 reconnects → pool sizing is correct
* 5 `Aborted_connects` → minor transient failures; can monitor and check logs

This workflow allows you to **benchmark, monitor, and tune** your MySQL + ProxySQL setup in a **repeatable and controlled Docker environment**.

</details>

---
