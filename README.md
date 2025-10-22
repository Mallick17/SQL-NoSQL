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

***

## Project Overview

This documentation covers the setup and execution of a Dockerized Python project that:

- Reads an existing CSV file (`MOCK_DATA.csv`).
- Adds 10,000 dummy data rows with unique modifications.
- Outputs a combined CSV (`MOCK_DATA_10000_more.csv`) for local use.

The containerized approach ensures reproducibility and isolation from local environment issues.

***

## Folder Structure

Your working directory `/Users/gyanaranjan.mallick/Downloads/docker_local` should contain:

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

Mount your folder so the output file is saved locally:

```bash
docker run --rm -v /Users/gyanaranjan.mallick/Downloads/docker_local:/app csv_dummy_data
```

Details:

- `--rm` cleans up container after exit.
- `-v /local/path:/app` mounts folder from Mac into container's `/app`.
- The script writes output `MOCK_DATA_10000_more.csv` into `/app`, which syncs to your Mac.

### 3. Verify Output

Check your local folder `/Users/gyanaranjan.mallick/Downloads/docker_local` for the file

`MOCK_DATA_10000_more.csv` containing the combined data.

***

## Summary

This setup provides a reproducible way to:

- Run data augmentation inside an isolated container.
- Avoid local environment dependency issues.
- Easily share or automate data preparation.

If your original CSV or script changes, simply rebuild the image and rerun the container.

***

[1](https://stackoverflow.com/questions/61262638/how-should-i-containerize-a-python-script-which-reads-a-csv-file)
[2](https://forums.docker.com/t/how-to-create-a-docker-container-when-i-have-two-python-scripts-which-are-dependent-to-each-other/128530)
[3](https://towardsdatascience.com/build-and-run-a-docker-container-for-your-machine-learning-model-60209c2d7a7f/)
[4](https://realpython.com/python-csv/)
[5](https://dev.to/cloudforce/containerizing-python-data-processing-scripts-with-docker-a-step-by-step-guide-166)
[6](https://www.kdnuggets.com/build-your-own-simple-data-pipeline-with-python-and-docker)
[7](https://www.dataquest.io/blog/intro-to-docker-compose/)

---

# Step-by-Step Guide to Create the `Ola_cab` Table in MySQL

<details>
    <summary>Click to view the Quick Setup</summary>

# Step-by-Step Commands you can run from your Container Shell to:

1. create a database `ola_db`
2. create lookup tables (`City`, `Vendor`, `Owner`)
3. create the `Ola_cab` table with **foreign key** constraints (so MySQL enforces referential integrity)
4. seed the lookup tables with valid IDs
5. load your CSV file `/var/lib/mysql-files/MOCK_DATA_10000_more.csv` into `Ola_cab` (the CSV header is ignored)

---

## 1) Create a SQL script (copy & paste this into your container)

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

-- Create main Ola_cab table (types follow your provided schema).
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
-- IMPORTANT: adjust the FIELDS/ENCLOSED/TERMINATED rules if your CSV format differs
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

   * I used `FIELDS TERMINATED BY ',' OPTIONALLY ENCLOSED BY '"'` and `LINES TERMINATED BY '\n'`. If your CSV uses `\r\n` line endings, replace `LINES TERMINATED BY '\r\n'`.
   * The `IGNORE 1 LINES` tells MySQL to skip the header row.

3. **Foreign key failures**

   * If a CSV row has a `city_id`, `vendor_id` or `owner_id` that is not present in the seeded lookup tables, MySQL will **reject** that row (foreign key constraint).
   * We seeded City 1..100, Vendor 1..50, Owner 1..200. If your CSV contains IDs outside these ranges, change the seeding ranges or pre-process the CSV to fix IDs. If you prefer MySQL to accept the rows without enforcement, I can give a variant that creates the table **without** foreign keys.

4. **If LOAD DATA fails with data conversion issues**

   * Date/time or boolean formats in the CSV can cause errors. If you hit such an error paste the error message and I’ll adapt the LOAD statement (or provide a Python loader that parses/validates).
   
</details>

You have a CSV with many columns. To import it properly, let's create a MySQL table that matches its structure.

***

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

Based on your column list, here is a sample `CREATE TABLE` statement with suitable data types. You should adjust data types based on actual data characteristics (length, numeric or text, etc.):

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
> - Adjust column sizes or types based on your data for best results.

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

2. Copy your CSV into that directory inside container, e.g.:

```bash
docker cp /Users/gyanaranjan.mallick/Downloads/docker_local/MOCK_DATA_10000_more.csv local-mysql:/var/lib/mysql-files/
```

3. Then run your import using full path inside that directory, e.g.:

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
| **Automated backup scripts** | Check `/var/backups/mysql/`, `/backups/`, `/opt/mysql_backups/`, etc. | Depends on your server setup or cron jobs                                                        |
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

You’ll be prompted for your password, and then the dump file will be created in the location you specify.

---

# Migrate the dump to the mysql server in local
### Step-by-Step Guide to Set Up the Employees Database

The Employees Database is a sample MySQL database for HR/employee management, containing tables like `employees`, `departments`, `dept_emp`, `dept_manager`, `salaries`, and `titles`. It includes constraints (e.g., primary keys, foreign keys), relationships, and a large dataset (~300,024 employee records and ~2.8 million salary entries) for testing queries, optimization, and data analysis. The data is about 167 MB when exported.

This guide assumes you have basic familiarity with command-line tools. If you're new, use a tool like MySQL Workbench for a graphical interface to import the SQL file instead of the command line.

<details>
    <summary>Click to view the Prerequisites and Steps to Execute</summary>

#### Prerequisites
- **MySQL Installed**: You need MySQL Server version 5.0 or higher. Download and install from the official site (https://dev.mysql.com/downloads/) if you don't have it. For Windows/Mac/Linux, follow the OS-specific instructions.
- **User Privileges**: Log in as a MySQL user with privileges like SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, and CREATE VIEW. The root user typically has these.
- **Disk Space and Resources**: Ensure at least 500 MB free disk space (for the data and indexes). The import may take 5-30 minutes depending on your hardware, as it's a large dataset.
- **Enable Local Infile (if needed)**: The script uses `LOAD DATA LOCAL INFILE` to load data from accompanying .dump files. If you encounter errors, add `--local-infile=1` to your mysql command, and ensure the server allows it (check with `SHOW GLOBAL VARIABLES LIKE 'local_infile';`—set to ON via `SET GLOBAL local_infile = 1;` if needed, but restart may be required).
- **Git (Optional)**: For cloning the repo. If not installed, download the ZIP instead.

#### Step 1: Download the Repository
- Go to https://github.com/datacharmer/test_db.
- **Option A (Recommended - Clone with Git)**:
  - Open a terminal or command prompt.
  - Run: `git clone https://github.com/datacharmer/test_db.git`
  - This downloads the entire repo, including the `employees.sql` script and necessary .dump files (e.g., employees.dump).
- **Option B (Download ZIP)**:
  - Click the green "Code" button on the GitHub page and select "Download ZIP".
  - Extract the ZIP file to a folder on your computer (e.g., `C:\test_db` on Windows or `~/test_db` on macOS/Linux).

#### Step 2: Navigate to the Repository Folder
- In your terminal/command prompt, change directory to the downloaded folder:
  - `cd test_db` (if cloned) or `cd path/to/extracted/folder` (if ZIP).
- Verify contents: You should see files like `employees.sql`, `employees_partitioned.sql`, `test_employees_md5.sql`, and several .dump.gz files (these contain the actual data).

#### Step 3: Start MySQL Server (If Not Running)
- Ensure your MySQL server is running:
  - On Windows: Use Services app or run `net start mysql`.
  - On macOS/Linux: Run `sudo service mysql start` or `brew services start mysql` (if using Homebrew).
- Log in to MySQL to test: `mysql -u root -p` (enter password when prompted). Exit with `exit;`.

#### Step 4: Import the Database
- From the repository folder, run the import command:
  - Standard version (non-partitioned tables):
    ```
    mysql -u root -p < employees.sql
    ```
  - Partitioned version (for better performance with large tables; recommended if your MySQL supports partitioning):
    ```
    mysql -u root -p < employees_partitioned.sql
    ```
- Replace `root` with your username if different. You'll be prompted for the password.
- The script will:
  - Create a database named `employees`.
  - Create tables with constraints (e.g., PRIMARY KEY on employee IDs, FOREIGN KEY links between tables like `dept_emp` referencing `employees` and `departments`).
  - Load data using `LOAD DATA LOCAL INFILE` from the .dump files.
- Wait for completion. If it fails (e.g., due to file paths), ensure you're in the correct directory, or use absolute paths in the SQL file (edit if needed).
- Common Errors and Fixes:
  - "The used command is not allowed with this MySQL version": Enable local_infile as mentioned in prerequisites.
  - Timeout or memory issues: Increase MySQL's `max_allowed_packet` (e.g., add `--max_allowed_packet=256M` to the command) or run on a machine with more RAM.
  - If using a hosted MySQL (e.g., RDS), upload files via SFTP and adjust import method.

#### Step 5: Verify the Installation
- Log in to MySQL: `mysql -u root -p`
- Switch to the database: `USE employees;`
- Check table counts: `SHOW TABLES;` (Should list 6 tables: departments, dept_emp, dept_manager, employees, salaries, titles).
- Check row counts: e.g., `SELECT COUNT(*) FROM employees;` (Should be ~300,024).
- Run the built-in test script from the repo folder:
  - `mysql -u root -p -t < test_employees_md5.sql` (or `test_employees_sha.sql` for SHA checksums).
  - Output should show "OK" for all tables, confirming data integrity (e.g., expected vs. found records and checksums match).

#### Step 6: Explore and Use the Database
- Run sample queries: e.g., `SELECT * FROM employees LIMIT 10;`
- For optimization practice: Try joins like `SELECT e.first_name, s.salary FROM employees e JOIN salaries s ON e.emp_no = s.emp_no WHERE s.salary > 100000;`
- Documentation: Refer to https://dev.mysql.com/doc/employee/en/ for schema details and examples.
- If you need to drop and reload: `DROP DATABASE employees;` then repeat Step 4.

If you encounter issues, check MySQL error logs (e.g., `SHOW ERRORS;`) or search for specific errors online. This setup is great for practicing with real-world-scale data! If you're using a different DBMS (e.g., PostgreSQL), conversions exist but aren't covered here.

</details>


Great, you're running MySQL in a container, have downloaded the Employees Database repository, verified the files (e.g., `employees.sql`, `.dump.gz` files), and are in the correct directory. I'll provide a step-by-step guide tailored for executing the `employees.sql` import within a container environment (assuming Docker with a MySQL image). This will include creating the database, loading the data, and verifying the setup, accounting for container-specific nuances like file paths and permissions.

### Step-by-Step Guide to Migrate the Dump, the Employees Database in a MySQL Container

<details>
    <summary>Click to view the step by step guide</summary>

#### Prerequisites
- **Docker and MySQL Container**: You have a running MySQL container (e.g., `mysql:8.0` or similar). If not, start one with:
  ```
  docker run --name mysql-container -e MYSQL_ROOT_PASSWORD=your_password -d -p 3306:3306 mysql:8.0
  ```
  Replace `your_password` with a secure password.
- **Repository Files**: You're in the `test_db` directory with files like `employees.sql` and `.dump.gz` files.
- **Container Access**: You can execute commands inside the container or from the host.
- **Local Infile Enabled**: The Employees Database uses `LOAD DATA LOCAL INFILE`. Ensure `local_infile` is enabled in your MySQL container (we'll verify and set this).

#### Step 1: Verify MySQL Container Status
1. Check if the MySQL container is running:
   ```
   docker ps
   ```
   Look for your container (e.g., `mysql-container`). If not running, start it:
   ```
   docker start mysql-container
   ```
2. Get the container's name or ID for commands:
   ```
   docker ps -a
   ```

#### Step 2: Copy Repository Files to the Container
Since you're running MySQL in a container, the `.sql` and `.dump.gz` files need to be accessible inside the container for `LOAD DATA LOCAL INFILE` to work.
1. Copy the entire `test_db` folder to a directory in the container (e.g., `/tmp/test_db`):
   ```
   docker cp ./test_db mysql-container:/tmp/test_db
   ```
   This assumes your current host directory is `test_db`. Adjust the path if needed (e.g., `/path/to/test_db`).
2. Verify files in the container:
   ```
   docker exec mysql-container ls /tmp/test_db
   ```
   You should see `employees.sql`, `employees_partitioned.sql`, `test_employees_md5.sql`, and `.dump.gz` files (e.g., `employees.dump`, `departments.dump`).

#### Step 3: Log in to MySQL and Enable Local Infile
1. Access the MySQL container's shell:
   ```
   docker exec -it mysql-container bash
   ```
2. Log in to MySQL inside the container:
   ```
   mysql -u root -p
   ```
   Enter the `MYSQL_ROOT_PASSWORD` you set when starting the container.
3. Check if `local_infile` is enabled:
   ```
   SHOW GLOBAL VARIABLES LIKE 'local_infile';
   ```
   If `Value` is `ON`, you're good. If `OFF`, enable it:
   ```
   SET GLOBAL local_infile = 1;
   ```
4. Exit MySQL (`exit;`) but stay in the container shell for now.

#### Step 4: Adjust File Permissions (If Needed)
The `.dump.gz` files are loaded by `LOAD DATA LOCAL INFILE`, and MySQL needs read access.
1. Inside the container, ensure the MySQL user can read the files:
   ```
   chmod -R 644 /tmp/test_db/*.*
   chown -R mysql:mysql /tmp/test_db
   ```
2. If you encounter permission errors later, you may need to adjust the MySQL configuration to allow reading from `/tmp/test_db`. Alternatively, move files to a MySQL-accessible directory like `/var/lib/mysql-files`:
   ```
   mv /tmp/test_db/* /var/lib/mysql-files/
   chmod -R 644 /var/lib/mysql-files/*.*
   chown -R mysql:mysql /var/lib/mysql-files
   ```

#### Step 5: Import the Employees Database
1. From the container shell, run the import:
   ```
   mysql -u root -p --local-infile=1 < /tmp/test_db/employees.sql
   ```
   - Use `--local-infile=1` to ensure `LOAD DATA LOCAL INFILE` works.
   - Enter the root password when prompted.
   - If you moved files to `/var/lib/mysql-files`, update the path in `employees.sql` (edit with `nano` or `vi` inside the container to point to `/var/lib/mysql-files`).
2. Alternatively, run from the host (if you prefer not to work inside the container):
   ```
   docker exec -i mysql-container mysql -u root -p --local-infile=1 < ./test_db/employees.sql
   ```
   This pipes the SQL file into the container's MySQL.
3. Wait for the import to complete (5-30 minutes, depending on hardware). The script:
   - Creates the `employees` database.
   - Sets up 6 tables (`employees`, `departments`, `dept_emp`, `dept_manager`, `salaries`, `titles`) with primary and foreign key constraints.
   - Loads data from the `.dump.gz` files using `LOAD DATA LOCAL INFILE`.

#### Step 6: Verify the Import
1. Log back into MySQL (inside container or via host):
   ```
   docker exec -it mysql-container mysql -u root -p
   ```
2. Switch to the database:
   ```
   USE employees;
   ```
3. List tables:
   ```
   SHOW TABLES;
   ```
   Expected output: `departments`, `dept_emp`, `dept_manager`, `employees`, `salaries`, `titles`.
4. Check row counts:
   ```
   SELECT COUNT(*) FROM employees;
   ```
   Should return ~300,024 rows.
   ```
   SELECT COUNT(*) FROM salaries;
   ```
   Should return ~2,844,047 rows.
5. Run the integrity test:
   - From the container shell:
     ```
     mysql -u root -p -t < /tmp/test_db/test_employees_md5.sql
     ```
   - Or from the host:
     ```
     docker exec -i mysql-container mysql -u root -p -t < ./test_db/test_employees_md5.sql
     ```
   - Look for "OK" for each table, confirming data integrity (matching row counts and checksums).

#### Step 7: Explore the Database
- Run sample queries to test:
  ```
  SELECT * FROM employees LIMIT 10;
  SELECT e.first_name, d.dept_name FROM employees e JOIN dept_emp de ON e.emp_no = de.emp_no JOIN departments d ON de.dept_no = d.dept_no LIMIT 10;
  ```
- Schema details: See https://dev.mysql.com/doc/employee/en/employees-structure.html for table relationships (e.g., `dept_emp` links `employees` and `departments` via foreign keys).
- If you want to use the partitioned version for better performance:
  - Repeat Step 5 with `employees_partitioned.sql` instead (drop the database first if needed: `DROP DATABASE employees;`).

#### Troubleshooting Common Issues
- **"ERROR 1148: The used command is not allowed"**: Ensure `--local-infile=1` is used and `local_infile` is `ON` in MySQL (Step 3). If persistent, add `local_infile=1` to the MySQL config file (`/etc/mysql/my.cnf` in the container) and restart the container:
  ```
  docker restart mysql-container
  ```
- **File Not Found**: Verify the `.dump.gz` files are in `/tmp/test_db` or `/var/lib/mysql-files`. Check paths in `employees.sql` (edit with `nano` if needed).
- **Permission Denied**: Ensure files are readable (`chmod 644`) and owned by `mysql:mysql` (Step 4).
- **Slow Import**: Large data takes time. If it fails, increase `max_allowed_packet`:
  ```
  SET GLOBAL max_allowed_packet = 268435456; -- 256MB
  ```
- **Connection Issues**: Ensure port 3306 is accessible (`docker ps` shows `0.0.0.0:3306->3306/tcp`).

#### Optional: Access via GUI
If you prefer a GUI, install MySQL Workbench on your host, connect to `localhost:3306` (or your container's IP/port), and import `employees.sql` via the "Server > Data Import" menu, pointing to the host path of `test_db`. Copy `.dump.gz` files to a container directory first, as above.

#### Next Steps
- Use this database to practice queries, joins, or optimization.
- For additional databases, repeat the process with other dumps (e.g., Sakila, World from https://dev.mysql.com/doc/index-other.html) or create your own using your Ola_cab script as a template.
- If you want to adapt your `ola_create_and_load.sql` for the container, copy it to the container and run similarly, ensuring the CSV path (`/var/lib/mysql-files/MOCK_DATA_10000_more.csv`) is accessible.

</details>
