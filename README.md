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
## SQL AND Operator
- The `WHERE` clause can contain one or many `AND` operators.
- The `AND` operator is used to filter records based on more than one condition.
- Syntax
  ```sql
  SELECT column1, column2, ...
  FROM table_name
  WHERE condition1 AND condition2 AND condition3 ...;
  ```
- Example
- Note: All the conditions must be TRUE in AND Operator
- Select all fields from Customers where Country is "Germany" AND City is "Berlin" AND PostalCode is higher than 12000:
  ```sql
  SELECT * FROM Customers WHERE Country = 'Germany' AND City = 'Berlin' AND PostalCode > 12000;
  ```
- Select all customers from Spain that starts with the letter 'G':
  ```sql
  SELECT * FROM Customers WHERE Country = 'Spain' AND CustomerName LIKE 'G%';
  ```
## SQL OR Operator
- The `WHERE` clause can contain one or more `OR` operators.
- The `OR` operator is used to filter records based on more than one condition.
- Syntax
  ```sql
  SELECT column1, column2, ...
  FROM table_name
  WHERE condition1 OR condition2 OR condition3 ...;
  ```
- Example
- Select all customers from Germany or Spain:
  ```sql
  SELECT * FROM Customers WHERE Country = 'Germany' OR Country = 'Spain';
  ```
  ### OR vs AND
  - The `OR` operator displays a record if any of the conditions are TRUE.
  - The `AND` operator displays a record if all the conditions are TRUE.
- Example
- Select all fields from Customers where either City is "Berlin", CustomerName starts with the letter "G" or Country is "Norway":
  ```sql
  SELECT * FROM Customers WHERE City = 'Berlin' OR CustomerName LIKE 'G%' OR Country = 'Norway';
  ```
  ### Combining AND and OR
  - Select all customers from Spain that starts with a "G" or an "R".
    ```sql
    SELECT * FROM Customers WHERE Country = 'Spain' AND (CustomerName LIKE 'G%' OR CustomerName LIKE 'R%');
    ```
    - Note:- Without parenthesis, the select statement will return all customers from Spain that starts with a "G", plus all customers that starts with an "R", regardless of the country value.

## SQL ORDER BY Keyword
- The `ORDER BY` keyword is used to sort the result-set in ascending or descending order.
- Syntax
  ```sql
  SELECT column1, column2, ...
  FROM table_name
  ORDER BY column1, column2, ... ASC|DESC;
  ```
- Example
- Sort the products from highest to lowest price
  ```sql
  SELECT * FROM Products ORDER BY Price DESC;
  ```
- Sort the products by ProductName in reverse order
  ```sql
  SELECT * FROM Products ORDER BY ProductName DESC;
  ```
- ORDER BY Several Columns Using Both ASC and DESC
  ```sql
  SELECT * FROM Customers ORDER BY Country ASC, CustomerName DESC;
  ```
## SQL SELECT DISTINCT Statement
- The `SELECT DISTINCT` statement is used to return only distinct (different) values.
- Inside a table, a column often contains many duplicate values; and sometimes you only want to list the different (distinct) values.
- Syntax
  ```sql
  SELECT DISTINCT column1, column2, ...
  FROM table_name;
  ```
- Example:
- Select all the different countries from the "Customers" table:
  ```sql
  SELECT DISTINCT Country FROM Customers;
  ```

## SQL INSERT INTO Statement
- The `INSERT INTO` statement is used to insert new records in a table.
- It is possible to write the INSERT INTO statement in two ways:
  - 1. Specify both the column names and the values to be inserted:
  - Syntax
    ```sql
    INSERT INTO table_name (column1, column2, column3, ...)
    VALUES (value1, value2, value3, ...);
    ```
  - Example
  - Insert a new record, but only insert data in the "CustomerName", "City", and "Country" columns:
    ```sql
    INSERT INTO Customers (CustomerName, City, Country)
    VALUES ('Cardinal', 'Stavanger', 'Norway');
    ```
  - 2. If you are adding values for all the columns of the table, you do not need to specify the column names in the SQL query. However, make sure the order of the values is in the same order as the columns in the table.
  - Syntax
    ```sql
    INSERT INTO table_name
    VALUES (value1, value2, value3, ...);
    ```
  - Example
    ```sql
    INSERT INTO Customers
    VALUES ('Cardinal', 'Tom B. Erichsen', 'Skagen 21', 'Stavanger', '4006', 'Norway');
    ```
- **Insert Multiple Rows**
  - To insert multiple rows of data, we use the same INSERT INTO statement, but with multiple values:
  - Make sure you separate each set of values with a comma `,`.
  ```sql
  INSERT INTO Customers (CustomerName, ContactName, Address, City, PostalCode, Country)
  VALUES
  ('Cardinal', 'Tom B. Erichsen', 'Skagen 21', 'Stavanger', '4006', 'Norway'),
  ('Greasy Burger', 'Per Olsen', 'Gateveien 15', 'Sandnes', '4306', 'Norway'),
  ('Tasty Tee', 'Finn Egan', 'Streetroad 19B', 'Liverpool', 'L1 0AA', 'UK');
  ```
    
  
