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
