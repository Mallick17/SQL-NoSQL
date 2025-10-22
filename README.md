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

</details>

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

</details>

---

# Migrate the dump to the mysql server in local
### Step-by-Step Guide to Set Up the Employees Database

The Employees Database is a sample MySQL database for HR/employee management, containing tables like `employees`, `departments`, `dept_emp`, `dept_manager`, `salaries`, and `titles`. It includes constraints (e.g., primary keys, foreign keys), relationships, and a large dataset (~300,024 employee records and ~2.8 million salary entries) for testing queries, optimization, and data analysis. The data is about 167 MB when exported.

This guide assumes you have basic familiarity with command-line tools. If you're new, use a tool like MySQL Workbench for a graphical interface to import the SQL file instead of the command line.

### Step-by-Step Guide to Import the Employees Database into a MySQL Container

This guide provides a clean and organized process to import the Employees Database into a MySQL container (e.g., `mysql:8.0`), addressing the issue with the `employees.sql` script where `source` commands incorrectly reference `.dump` files (raw tab-separated data) instead of using `LOAD DATA LOCAL INFILE`. The steps account for container-specific nuances like file paths and permissions, ensuring the database (with ~300,024 employee records and ~2.8M salary entries, ~167 MB) is set up with proper constraints (primary keys, foreign keys). The import may take 5-30 minutes. The modification of `employees.sql` is moved to immediately follow cloning the repository for clarity.

#### Prerequisites
- **Docker and MySQL Container**: A running MySQL container. Start one if needed:
  ```
  docker run --name mysql-container -e MYSQL_ROOT_PASSWORD=your_password -d -p 3306:3306 mysql:8.0
  ```
  Replace `your_password` with a secure password. Use your container name (e.g., `mysql-container`).
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
