# SQL Clauses Notes

## SQL Clauses

### Overview
- **Clauses**: Components of an SQL statement that specify conditions, groupings, sorting, or other operations to manipulate or retrieve data from a database.
- **Purpose**: Clauses refine and control the behavior of SQL queries, enabling precise data retrieval, manipulation, and organization.
- **Context**: Clauses are used within SQL statements (e.g., `SELECT`, `INSERT`, `UPDATE`, `DELETE`) to define how data is processed, filtered, or presented.

### SQL Clauses
Below is a comprehensive list of SQL clauses, categorized by their use in **Data Manipulation Language (DML)**, **Data Definition Language (DDL)**, **Data Control Language (DCL)**, and **Transaction Control Language (TCL)** statements. Each clause is explained with its purpose, syntax, and examples.

### Example Database
The examples use the following normalized retail database tables, consistent with previous notes:
```
Table: Customers
| CustomerID (PK) | CustomerName | CustomerCity |
|-----------------|--------------|--------------|
| 101             | John         | New York     |
| 102             | Alice        | Chicago      |
| 103             | Bob          | Boston       |

Table: Orders
| OrderID (PK) | CustomerID (FK) | OrderDate  | Amount |
|--------------|-----------------|------------|--------|
| 1            | 101             | 2025-07-01 | 100.00 |
| 2            | 101             | 2025-07-02 | 150.00 |
| 3            | 102             | 2025-07-03 | 200.00 |

Table: OrderItems
| OrderID (PK, FK) | ItemID (PK, FK) | Quantity |
|------------------|-----------------|----------|
| 1                | 201             | 2        |
| 2                | 202             | 1        |
| 3                | 201             | 3        |

Table: Items
| ItemID (PK) | ItemName | ItemPrice |
|-------------|----------|-----------|
| 201         | Pen      | 2.00      |
| 202         | Book     | 10.00     |
```

### Clauses in SQL

#### 1. SELECT Clause
- **Definition**: Specifies the columns to retrieve from a table or joined tables in a `SELECT` query.
- **Use Case**: Define the data to display in the query result.
- **Syntax**:
  ```sql
  SELECT column1, column2, ... | SELECT * | SELECT expression
  FROM table_name;
  ```
- **Example**: Retrieve customer names and cities.
  ```sql
  SELECT CustomerName, CustomerCity
  FROM Customers;
  ```
  **Output**:
  ```
  | CustomerName | CustomerCity |
  |--------------|--------------|
  | John         | New York     |
  | Alice        | Chicago      |
  | Bob          | Boston       |
  ```
- **Notes**:
  - Use `*` to select all columns (e.g., `SELECT * FROM Customers`).
  - Can include expressions (e.g., `SELECT ItemPrice * Quantity AS Total`).
- **Connection**: Relates to Joins (Topic 7) for selecting columns from multiple tables.

#### 2. FROM Clause
- **Definition**: Specifies the table(s) from which data is retrieved in a `SELECT` query.
- **Use Case**: Identify the source table(s) or views for the query.
- **Syntax**:
  ```sql
  SELECT column1, ...
  FROM table_name [alias] | FROM table1 JOIN table2 ...;
  ```
- **Example**: Retrieve data from `Orders` with an alias.
  ```sql
  SELECT o.OrderID, o.Amount
  FROM Orders o;
  ```
  **Output**:
  ```
  | OrderID | Amount |
  |---------|--------|
  | 1       | 100.00 |
  | 2       | 150.00 |
  | 3       | 200.00 |
  ```
- **Notes**: Used with Joins (e.g., `FROM Customers JOIN Orders`) to combine tables.
- **Connection**: Essential for Joins (Topic 7) and Normalization (Topic 4), as it references tables created during database design.

#### 3. WHERE Clause
- **Definition**: Filters rows based on a specified condition.
- **Use Case**: Retrieve or manipulate specific rows (e.g., orders above a certain amount).
- **Syntax**:
  ```sql
  SELECT column1, ...
  FROM table_name
  WHERE condition;
  ```
- **Example**: Retrieve orders with amount greater than 120.
  ```sql
  SELECT OrderID, Amount
  FROM Orders
  WHERE Amount > 120;
  ```
  **Output**:
  ```
  | OrderID | Amount |
  |---------|--------|
  | 2       | 150.00 |
  | 3       | 200.00 |
  ```
- **Notes**:
  - Supports operators: `=`, `<`, `>`, `<>`, `LIKE`, `IN`, `BETWEEN`, `AND`, `OR`, `NOT`.
  - Used in `SELECT`, `UPDATE`, `DELETE`.
- **Connection**: Relates to Data Integrity (Topic 6) for filtering valid data and Joins (Topic 7) for conditions in `ON` clauses.

#### 4. GROUP BY Clause
- **Definition**: Groups rows with the same values in specified columns for aggregate calculations (e.g., `SUM`, `COUNT`).
- **Use Case**: Summarize data (e.g., total orders per customer).
- **Syntax**:
  ```sql
  SELECT column1, AGGREGATE_FUNCTION(column2)
  FROM table_name
  [WHERE condition]
  GROUP BY column1;
  ```
- **Example**: Calculate total order amount per customer.
  ```sql
  SELECT c.CustomerName, SUM(o.Amount) AS TotalSpent
  FROM Customers c
  LEFT JOIN Orders o ON c.CustomerID = o.CustomerID
  GROUP BY c.CustomerName;
  ```
  **Output**:
  ```
  | CustomerName | TotalSpent |
  |--------------|------------|
  | John         | 250.00     |
  | Alice        | 200.00     |
  | Bob          | NULL       |
  ```
- **Notes**: Must include non-aggregated columns in `GROUP BY` (except in some DBMS like MySQL).
- **Connection**: Used with Aggregate Functions (Topic 7) and Joins (Topic 7) for summarizing joined data.

#### 5. HAVING Clause
- **Definition**: Filters grouped results based on a condition, used with `GROUP BY`.
- **Use Case**: Restrict groups (e.g., customers with total orders above a threshold).
- **Syntax**:
  ```sql
  SELECT column1, AGGREGATE_FUNCTION(column2)
  FROM table_name
  GROUP BY column1
  HAVING condition;
  ```
- **Example**: Find customers with total orders exceeding 200.
  ```sql
  SELECT c.CustomerName, SUM(o.Amount) AS TotalSpent
  FROM Customers c
  LEFT JOIN Orders o ON c.CustomerID = o.CustomerID
  GROUP BY c.CustomerName
  HAVING SUM(o.Amount) > 200;
  ```
  **Output**:
  ```
  | CustomerName | TotalSpent |
  |--------------|------------|
  | John         | 250.00     |
  ```
- **Notes**: `HAVING` filters groups, while `WHERE` filters individual rows.
- **Connection**: Relates to Aggregate Functions (Topic 7) and GROUP BY for filtering summarized data.

#### 6. ORDER BY Clause
- **Definition**: Sorts the result set by one or more columns in ascending (`ASC`) or descending (`DESC`) order.
- **Use Case**: Present results in a specific匆

System: **ORDER BY Clause** (continued)

- **Definition**: Sorts the result set by one or more columns in ascending (`ASC`) or descending (`DESC`) order.
- **Use Case**: Display query results in a specific order (e.g., alphabetical, numerical).
- **Syntax**:
  ```sql
  SELECT column1, column2, ...
  FROM table_name
  ORDER BY column1 [ASC | DESC], column2 [ASC | DESC], ...;
  ```
- **Example**: Sort customers by name in ascending order.
  ```sql
  SELECT CustomerName, CustomerCity
  FROM Customers
  ORDER BY CustomerName ASC;
  ```
  **Output**:
  ```
  | CustomerName | CustomerCity |
  |--------------|--------------|
  | Alice        | Chicago      |
  | Bob          | Boston       |
  | John         | New York     |
  ```
- **Notes**:
  - Default is `ASC` if not specified.
  - Can sort by multiple columns (e.g., `ORDER BY CustomerCity, CustomerName`).
- **Connection**: Enhances readability of query results, often used with `SELECT` and Joins (Topic 7).

#### 7. JOIN ... ON Clause
- **Definition**: Combines rows from two or more tables based on a condition (part of the `JOIN` operation).
- **Use Case**: Retrieve related data from multiple tables (e.g., customers and their orders).
- **Syntax**:
  ```sql
  SELECT column1, column2, ...
  FROM table1
  JOIN table2 ON table1.column = table2.column;
  ```
- **Example**: Join customers with their orders.
  ```sql
  SELECT c.CustomerName, o.OrderID, o.OrderDate
  FROM Customers c
  INNER JOIN Orders o ON c.CustomerID = o.CustomerID;
  ```
  **Output**:
  ```
  | CustomerName | OrderID | OrderDate  |
  |--------------|---------|------------|
  | John         | 1       | 2025-07-01 |
  | John         | 2       | 2025-07-02 |
  | Alice        | 3       | 2025-07-03 |
  ```
- **Notes**: Covered extensively in Joins (Topic 7); includes `INNER`, `LEFT`, `RIGHT`, `FULL`, `CROSS`, `NATURAL`, etc.
- **Connection**: Relates to Keys (Topic 5) and Normalization (Topic 4) for establishing table relationships.

#### 8. USING Clause
- **Definition**: Simplifies `JOIN` syntax by specifying a single column name that exists in both tables, instead of a full `ON` condition.
- **Use Case**: Join tables with identically named columns.
- **Syntax**:
  ```sql
  SELECT column1, column2, ...
  FROM table1
  JOIN table2 USING (column_name);
  ```
- **Example**: Join customers and orders using `CustomerID`.
  ```sql
  SELECT CustomerName, OrderID
  FROM Customers
  INNER JOIN Orders USING (CustomerID);
  ```
  **Output**: Same as INNER JOIN with `ON c.CustomerID = o.CustomerID`.
- **Notes**:
  - Alternative to `ON` when column names match exactly.
  - Less common than `ON` due to explicitness of `ON`.
- **Connection**: Relates to Natural Join (Topic 7) and Keys (Topic 5).

#### 9. INTO Clause
- **Definition**: Specifies a target table or variable for storing query results or inserted data.
- **Use Case**: Save query results into a new table or temporary table, or insert data.
- **Syntax**:
  - For `SELECT ... INTO` (new table, SQL Server):
    ```sql
    SELECT column1, column2, ...
    INTO new_table
    FROM table_name;
    ```
  - For `INSERT ... INTO`:
    ```sql
    INSERT INTO table_name (column1, column2, ...)
    VALUES (value1, value2, ...);
    ```
- **Example**: Create a new table with high-value orders.
  ```sql
  SELECT OrderID, Amount
  INTO HighValueOrders
  FROM Orders
  WHERE Amount > 150;
  ```
  **Result**: Creates `HighValueOrders` table with:
  ```
  | OrderID | Amount |
  |---------|--------|
  | 2       | 150.00 |
  | 3       | 200.00 |
  ```
- **Notes**:
  - `SELECT ... INTO` is not supported in all DBMS (e.g., MySQL uses `CREATE TABLE ... SELECT`).
  - Common in `INSERT` statements.
- **Connection**: Relates to DML operations (Topic 2) and Data Manipulation.

#### 10. VALUES Clause
- **Definition**: Specifies the data values to be inserted into a table.
- **Use Case**: Insert new rows into a table.
- **Syntax**:
  ```sql
  INSERT INTO table_name (column1, column2, ...)
  VALUES (value1, value2, ...);
  ```
- **Example**: Insert a new customer.
  ```sql
  INSERT INTO Customers (CustomerID, CustomerName, CustomerCity)
  VALUES (104, 'Carol', 'Miami');
  ```
  **Result**: Adds a row to `Customers`.
- **Notes**: Can insert multiple rows using `VALUES (row1), (row2), ...`.
- **Connection**: Relates to Data Integrity (Topic 6) for ensuring valid data insertion.

#### 11. SET Clause
- **Definition**: Specifies columns and their new values in an `UPDATE` statement.
- **Use Case**: Modify existing rows in a table.
- **Syntax**:
  ```sql
  UPDATE table_name
  SET column1 = value1, column2 = value2, ...
  [WHERE condition];
  ```
- **Example**: Update a customer’s city.
  ```sql
  UPDATE Customers
  SET CustomerCity = 'Los Angeles'
  WHERE CustomerID = 101;
  ```
  **Result**: Changes John’s city to Los Angeles.
- **Notes**: Use `WHERE` to avoid updating all rows.
- **Connection**: Relates to Data Integrity (Topic 6) and DML operations (Topic 2).

#### 12. ON Clause
- **Definition**: Specifies the condition for joining tables in a `JOIN` operation.
- **Use Case**: Define how tables are related in a join.
- **Syntax**: See `JOIN ... ON` above.
- **Notes**: Covered in Joins (Topic 7); essential for all joins except `CROSS JOIN` and `NATURAL JOIN`.
- **Connection**: Relates to Keys (Topic 5) and Normalization (Topic 4).

#### 13. WITH Clause
- **Definition**: Defines a Common Table Expression (CTE) or temporary result set for use within a query.
- **Use Case**: Simplify complex queries or enable recursive queries.
- **Syntax**:
  ```sql
  WITH cte_name AS (
      SELECT column1, ...
      FROM table_name
      [WHERE condition]
  )
  SELECT column1, ...
  FROM cte_name;
  ```
- **Example**: Calculate total orders per customer using a CTE.
  ```sql
  WITH CustomerOrders AS (
      SELECT c.CustomerID, c.CustomerName, COUNT(o.OrderID) AS OrderCount
      FROM Customers c
      LEFT JOIN Orders o ON c.CustomerID = o.CustomerID
      GROUP BY c.CustomerID, c.CustomerName
  )
  SELECT CustomerName, OrderCount
  FROM CustomerOrders
  WHERE OrderCount > 0;
  ```
  **Output**:
  ```
  | CustomerName | OrderCount |
  |--------------|------------|
  | John         | 2          |
  | Alice        | 1          |
  ```
- **Notes**:
  - CTEs improve readability and allow reuse of subqueries.
  - Supports recursive queries (e.g., for hierarchical data).
- **Connection**: Relates to Joins (Topic 7) and Aggregate Functions (Topic 7).

#### 14. AS Clause
- **Definition**: Assigns an alias to a column, table, or expression for readability or reference.
- **Use Case**: Simplify column names, table names, or computed values in query results.
- **Syntax**:
  ```sql
  SELECT column_name AS alias_name
  FROM table_name [AS table_alias];
  ```
- **Example**: Alias a computed column and table.
  ```sql
  SELECT c.CustomerName AS Name, SUM(o.Amount) AS TotalSpent
  FROM Customers AS c
  LEFT JOIN Orders AS o ON c.CustomerID = o.CustomerID
  GROUP BY c.CustomerName;
  ```
  **Output**:
  ```
  | Name  | TotalSpent |
  |-------|------------|
  | John  | 250.00     |
  | Alice | 200.00     |
  | Bob   | NULL       |
  ```
- **Notes**: Aliases are optional but improve clarity.
- **Connection**: Used extensively in Joins (Topic 7) and complex queries.

#### 15. DISTINCT Clause
- **Definition**: Removes duplicate rows from the result set.
- **Use Case**: Retrieve unique values or rows.
- **Syntax**:
  ```sql
  SELECT DISTINCT column1, column2, ...
  FROM table_name;
  ```
- **Example**: Retrieve unique customer cities.
  ```sql
  SELECT DISTINCT CustomerCity
  FROM Customers;
  ```
  **Output**:
  ```
  | CustomerCity |
  |--------------|
  | New York     |
  | Chicago      |
  | Boston       |
  ```
- **Notes**: Applies to the entire row or specified columns.
- **Connection**: Relates to Data Integrity (Topic 6) for ensuring unique results.

#### 16. LIMIT Clause (or TOP in SQL Server)
- **Definition**: Restricts the number of rows returned by a query.
- **Use Case**: Retrieve a specific number of rows (e.g., top 5 results).
- **Syntax**:
  ```sql
  SELECT column1, ...
  FROM table_name
  LIMIT number [OFFSET number];
  ```
- **Example**: Retrieve the top 2 orders by amount.
  ```sql
  SELECT OrderID, Amount
  FROM Orders
  ORDER BY Amount DESC
  LIMIT 2;
  ```
  **Output**:
  ```
  | OrderID | Amount |
  |---------|--------|
  | 3       | 200.00 |
  | 2       | 150.00 |
  ```
- **Notes**:
  - MySQL/PostgreSQL use `LIMIT`; SQL Server uses `TOP`.
  - `OFFSET` skips a specified number of rows.
- **Connection**: Enhances query efficiency and usability with ORDER BY.

#### 17. CASE Clause
- **Definition**: Provides conditional logic within a `SELECT` query to transform data based on conditions.
- **Use Case**: Create custom output based on column values.
- **Syntax**:
  ```sql
  SELECT column1,
         CASE
             WHEN condition1 THEN result1
             WHEN condition2 THEN result2
             ELSE result_default
         END AS alias_name
  FROM table_name;
  ```
- **Example**: Categorize orders by amount.
  ```sql
  SELECT OrderID, Amount,
         CASE
             WHEN Amount > 150 THEN 'High'
             WHEN Amount > 100 THEN 'Medium'
             ELSE 'Low'
         END AS OrderCategory
  FROM Orders;
  ```
  **Output**:
  ```
  | OrderID | Amount | OrderCategory |
  |---------|--------|--------------|
  | 1       | 100.00 | Low          |
  | 2       | 150.00 | Medium       |
  | 3       | 200.00 | High         |
  ```
- **Notes**: Can be used in `SELECT`, `WHERE`, or `ORDER BY`.
- **Connection**: Enhances data presentation, often used with Joins (Topic 7).

#### 18. TRANSACTION Clauses (COMMIT, ROLLBACK, SAVEPOINT)
- **Definition**: Manage database transactions to ensure data integrity.
- **Use Case**: Control the outcome of a series of DML operations.
- **Syntax**:
  ```sql
  BEGIN TRANSACTION;
  -- DML statements (INSERT, UPDATE, DELETE)
  COMMIT; -- or ROLLBACK;
  SAVEPOINT savepoint_name;
  ```
- **Example**: Update customer city with transaction control.
  ```sql
  BEGIN TRANSACTION;
  UPDATE Customers
  SET CustomerCity = 'Miami'
  WHERE CustomerID = 101;
  SAVEPOINT customer_update;
  -- If error occurs:
  ROLLBACK TO customer_update;
  -- If successful:
  COMMIT;
  ```
- **Notes**:
  - Ensures ACID properties (Topic 3: RDBMS).
  - `SAVEPOINT` allows partial rollbacks.
- **Connection**: Relates to Data Integrity (Topic 6) and TCL (Topic 2).

#### 19. GRANT and REVOKE Clauses (DCL)
- **Definition**: Manage database permissions.
- **Use Case**: Assign or remove access rights for users or roles.
- **Syntax**:
  ```sql
  GRANT privilege ON object TO user;
  REVOKE privilege ON object FROM user;
  ```
- **Example**: Grant and revoke select permission.
  ```sql
  GRANT SELECT ON Customers TO User1;
  REVOKE SELECT ON Customers FROM User1;
  ```
- **Notes**: Privileges include `SELECT`, `INSERT`, `UPDATE`, `DELETE`, etc.
- **Connection**: Relates to DCL (Topic 2) and Data Integrity (Topic 6).

### Practical Tips
- **Query Structure**: Follow the logical order of clauses in a `SELECT` query:
  ```sql
  SELECT [DISTINCT] column1, ...
  FROM table_name
  [JOIN table2 ON condition]
  [WHERE condition]
  [GROUP BY column]
  [HAVING condition]
  [ORDER BY column [ASC | DESC]]
  [LIMIT number];
  ```
- **Performance**:
  - Use `WHERE` to filter early and reduce rows before joins or grouping.
  - Index columns used in `WHERE`, `JOIN`, `GROUP BY`, and `ORDER BY`.
- **Debugging**:
  - Check result sets for unexpected NULLs or duplicates.
  - Test clauses individually in complex queries.
- **Clarity**:
  - Use aliases (`AS`) for readability.
  - Break complex queries into CTEs (`WITH`) for modularity.

### Common Pitfalls
- **Misplaced Clauses**: Clauses must follow the correct order (e.g., `WHERE` before `GROUP BY`).
- **Ambiguous Columns**: Use table aliases in multi-table queries.
- **Overusing `*`**: Specify columns explicitly in `SELECT` for clarity and performance.
- **HAVING vs. WHERE**: Use `WHERE` for row-level filtering, `HAVING` for group-level filtering.