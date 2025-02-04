# SQL
DBMS &amp; RDBMS
SQL is a standard language for storing, manipulating and retrieving data in databases.
### What is SQL?
- SQL stands for Structured Query Language.
- SQL lets you access and manipulate databases.
### What can SQL do?
- SQL can execute queries against a database.
- SQL can retrieve data from a database.
- SQL can insert, update and delete records in a database.
- SQL can create new databases.
- SQL can create new tables in a database.
- SQL can create stored procedures and can create views in a database.
- SQL can set permissions on tables, procedures, and views.
# RDBMS
- RDBMS is the basis for SQL, RDBMS stands for Relational Database Management System.
- The data in RDBMS is stored in database objects called tables. A table is a collection of related data entries and it consists of columns and rows.
  - Tables are also knows Objects or Entity.
  - Columns are also knows as Attributes or Fileds.(A column is a vertical entity in a table.)
  - Rows are also knows as Records or Tuples.(A record is a horizontal entity in a table.)
# SQL Syntax
- SELECT - extracts data from a database
- UPDATE - updates data in a database
- DELETE - deletes data from a database
- INSERT INTO - inserts new data into a database
- CREATE DATABASE - creates a new database
- ALTER DATABASE - modifies a database
- CREATE TABLE - creates a new table
- ALTER TABLE - modifies a table
- DROP TABLE - deletes a table
- CREATE INDEX - creates an index (search key)
- DROP INDEX - deletes an index
  - Note:- SQL keywords are NOT case sensitive: select is the same as SELECT.

## SQL SELECT Statement
- The `SELECT` statement is used to select data from a database.
- Syntax
  ```sql
  SELECT column1, column2, ...
  FROM table_name;
  ```
  - Example
  - Select CustomerName & City Columns from Customers Table.
    ```sql
    SELECT CustomerName, City FROM Customers;
    ```
  - Select All columns
    - If you want to return all columns, without specifying every column name, you can use the `SELECT *` syntax:
      ```sql
      SELECT * FROM Customers;
      ```
## SQL WHERE Clause
- The `WHERE` clause is used to filter records.
- It is used to extract only those records that fulfill a specified condition.
- Syntax
  ```sql
  SELECT column1, column2, ...
  FROM table_name
  WHERE condition;
  ```
  - Note:- The `WHERE` clause is not only used in `SELECT` statements, it is also used in `UPDATE`, `DELETE`, etc.!
  - Example
  - Select all customers from Mexico:
    ```sql
    SELECT * FROM Customers WHERE Country='Mexico';
    ```
  - Pick a customer whose customerID is 1:
    ```sql
    SELECT * FROM Customers WHERE CustomerID=1;
    ```
    <br>
  **Operators in the WHERE Clause** <br>
  - = , > , < , >= , <= , <> , BETWEEN , LIKE , IN
