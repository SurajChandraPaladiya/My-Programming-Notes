# SQL Operators Notes

## SQL Operators

### Overview
- **Operators**: Symbols or keywords in SQL used to perform operations on data, such as comparisons, arithmetic calculations, logical evaluations, pattern matching, or set operations.
- **Purpose**: Enable filtering, manipulation, and comparison of data within SQL queries, facilitating precise data retrieval and modification.
- **Context**: Used in clauses like `WHERE`, `HAVING`, `ON`, and expressions in `SELECT`, `UPDATE`, or `INSERT` statements.
- **Categories**: Arithmetic, Comparison, Logical, String, Set, and Specialized Comparison Operators.

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

### Types of SQL Operators

#### 1. Arithmetic Operators
- **Definition**: Perform mathematical operations on numeric values.
- **Operators**:
  - `+` (Addition)
  - `-` (Subtraction)
  - `*` (Multiplication)
  - `/` (Division)
  - `%` (Modulus, remainder of division)
- **Use Case**: Calculate derived values in queries or expressions.
- **Syntax**:
  ```sql
  SELECT column1 + column2 AS result
  FROM table_name;
  ```
- **Example**: Calculate total cost per order item.
  ```sql
  SELECT oi.OrderID, oi.Quantity * i.ItemPrice AS TotalCost
  FROM OrderItems oi
  JOIN Items i ON oi.ItemID = i.ItemID;
  ```
  **Output**:
  ```
  | OrderID | TotalCost |
  |---------|-----------|
  | 1       | 4.00      |
  | 2       | 10.00     |
  | 3       | 6.00      |
  ```
- **Notes**:
  - Ensure operands are numeric (use `CAST` if needed).
  - Division by zero causes errors in most DBMS.

#### 2. Comparison Operators
- **Definition**: Compare values to filter rows or define conditions.
- **Operators**:
  - `=` (Equal to)
  - `<>` or `!=` (Not equal to)
  - `<` (Less than)
  - `>` (Greater than)
  - `<=` (Less than or equal to)
  - `>=` (Greater than or equal to)
  - `!<` (Not less than, equivalent to `>=`, non-standard, rare)
  - `!>` (Not greater than, equivalent to `<=`, non-standard, rare)
- **Use Case**: Filter rows in `WHERE`, `HAVING`, or `ON` clauses.
- **Syntax**:
  ```sql
  SELECT column1, ...
  FROM table_name
  WHERE column_name operator value;
  ```
- **Example**: Find orders with amount not less than 150 (using standard `>=`).
  ```sql
  SELECT OrderID, Amount
  FROM Orders
  WHERE Amount >= 150;
  ```
  **Output**:
  ```
  | OrderID | Amount |
  |---------|--------|
  | 2       | 150.00 |
  | 3       | 200.00 |
  ```
- **Notes**:
  - Compatible with numeric, string, and date data types.
  - `!<` and `!>` are non-standard and not supported in major DBMS (e.g., MySQL, PostgreSQL, SQL Server, Oracle). Use `>=` and `<=` instead.
  - Use `<>` or `!=` depending on DBMS (e.g., SQL Server prefers `!=`).

#### 3. Logical Operators
- **Definition**: Combine or negate conditions to produce a boolean result (TRUE, FALSE, or UNKNOWN).
- **Operators**:
  - `AND` (Both conditions must be true)
  - `OR` (At least one condition must be true)
  - `NOT` (Negates a condition)
- **Use Case**: Combine multiple conditions in `WHERE`, `HAVING`, or `ON` clauses.
- **Syntax**:
  ```sql
  SELECT column1, ...
  FROM table_name
  WHERE condition1 AND|OR|NOT condition2;
  ```
- **Example**: Find orders from specific customers with high amounts.
  ```sql
  SELECT OrderID, CustomerID, Amount
  FROM Orders
  WHERE CustomerID = 101 AND Amount >= 150;
  ```
  **Output**:
  ```
  | OrderID | CustomerID | Amount |
  |---------|------------|--------|
  | 2       | 101        | 150.00 |
  ```
- **Notes**:
  - Use parentheses to control evaluation order (e.g., `(condition1 AND condition2) OR condition3`).
  - Logical operators combine conditions, while other operators (`IN`, `EXISTS`, etc.) perform specific tests.

#### 4. String Operators
- **Definition**: Manipulate or compare string data.
- **Operators**:
  - `LIKE` (Pattern matching with wildcards: `%` for any characters, `_` for a single character)
  - `||` (String concatenation, supported in PostgreSQL, Oracle; MySQL uses `CONCAT()`)
- **Use Case**: Filter or combine string data in queries.
- **Syntax**:
  ```sql
  SELECT column1
  FROM table_name
  WHERE column_name LIKE 'pattern';
  ```
- **Example 1 (LIKE)**: Find customers with names starting with 'J'.
  ```sql
  SELECT CustomerName
  FROM Customers
  WHERE CustomerName LIKE 'J%';
  ```
  **Output**:
  ```
  | CustomerName |
  |--------------|
  | John         |
  ```
- **Example 2 (Concatenation)**: Combine name and city (PostgreSQL syntax).
  ```sql
  SELECT CustomerName || ', ' || CustomerCity AS FullInfo
  FROM Customers;
  ```
  **Output**:
  ```
  | FullInfo          |
  |-------------------|
  | John, New York    |
  | Alice, Chicago    |
  | Bob, Boston       |
  ```
- **Notes**:
  - MySQL uses `CONCAT(CustomerName, ', ', CustomerCity)` instead of `||`.
  - `LIKE` is case-sensitive in some DBMS (e.g., PostgreSQL).

#### 5. Set Operators
- **Definition**: Perform set operations on query results (covered in Topic 9: Set Operations).
- **Operators**:
  - `UNION` (Combine results, remove duplicates)
  - `UNION ALL` (Combine results, keep duplicates)
  - `INTERSECT` (Return common rows)
  - `EXCEPT`/`MINUS` (Return rows in first query not in second)
- **Use Case**: Combine or compare result sets from multiple `SELECT` queries.
- **Syntax**:
  ```sql
  SELECT column1, ...
  FROM table1
  UNION | UNION ALL | INTERSECT | EXCEPT
  SELECT column1, ...
  FROM table2;
  ```
- **Example**: Combine customer lists from two tables.
  ```sql
  SELECT CustomerName FROM Customers
  UNION
  SELECT CustomerName FROM HighValueCustomers;
  ```
- **Notes**: See Set Operations (Topic 9) for detailed examples.

#### 6. Specialized Comparison Operators
- **Definition**: Perform specific comparisons or tests involving lists, ranges, subqueries, or NULL values, often used within logical expressions.
- **Operators**:
  - **IN**: Checks if a value is in a list or subquery.
  - **BETWEEN**: Checks if a value lies within a range (inclusive).
  - **IS NULL / IS NOT NULL**: Tests for NULL or non-NULL values.
  - **EXISTS / NOT EXISTS**: Tests for the existence of rows in a subquery.
  - **ANY / SOME**: Compares a value to any value in a subquery.
  - **ALL**: Compares a value to all values in a subquery.
- **Use Case**: Filter rows based on specific conditions, often involving sets or subqueries.
- **Syntax and Examples**:
  - **IN**:
    ```sql
    SELECT CustomerName
    FROM Customers
    WHERE CustomerCity IN ('New York', 'Chicago');
    ```
    **Output**:
    ```
    | CustomerName |
    |--------------|
    | John         |
    | Alice        |
    ```
  - **BETWEEN**:
    ```sql
    SELECT OrderID, Amount
    FROM Orders
    WHERE Amount BETWEEN 100 AND 150;
    ```
    **Output**:
    ```
    | OrderID | Amount |
    |---------|--------|
    | 1       | 100.00 |
    | 2       | 150.00 |
    ```
  - **IS NULL / IS NOT NULL**:
    ```sql
    SELECT CustomerName
    FROM Customers
    WHERE CustomerCity IS NOT NULL;
    ```
    **Output**: All customers (all have cities in this case).
  - **EXISTS**:
    ```sql
    SELECT CustomerName
    FROM Customers c
    WHERE EXISTS (
        SELECT 1
        FROM Orders o
        WHERE o.CustomerID = c.CustomerID
    );
    ```
    **Output**:
    ```
    | CustomerName |
    |--------------|
    | John         |
    | Alice        |
    ```
  - **ANY / SOME**:
    ```sql
    SELECT OrderID, Amount
    FROM Orders
    WHERE Amount > ANY (SELECT ItemPrice FROM Items);
    ```
    **Output**: All orders (all amounts exceed some item prices).
  - **ALL**:
    ```sql
    SELECT OrderID, Amount
    FROM Orders
    WHERE Amount > ALL (SELECT ItemPrice FROM Items);
    ```
    **Output**:
    ```
    | OrderID | Amount |
    |---------|--------|
    | 1       | 100.00 |
    | 2       | 150.00 |
    | 3       | 200.00 |
    ```
- **Notes**:
  - These operators are distinct from logical operators (`AND`, `OR`, `NOT`) because they perform specific tests (e.g., set membership, range checks, subquery existence) rather than combining boolean conditions.
  - They are often used within logical expressions (e.g., `WHERE CustomerID IN (101, 102) AND Amount > 100`).

### Practical Tips
- **Operator Precedence**:
  - Arithmetic: `*`, `/`, `%` before `+`, `-`.
  - Comparison: `=`, `<`, `>`, etc., evaluated after arithmetic.
  - Logical: `NOT` before `AND`, `AND` before `OR`. Use parentheses for clarity.
- **Performance**:
  - Index columns used with comparison (`=`, `<`, etc.) and `IN` operators.
  - Use `EXISTS` instead of `IN` for subqueries with large datasets for better performance in some DBMS (e.g., PostgreSQL).
- **DBMS Variations**:
  - Concatenation: `||` (PostgreSQL, Oracle) vs. `CONCAT()` (MySQL).
  - `EXCEPT` (SQL Server, PostgreSQL) vs. `MINUS` (Oracle).
  - `!<` and `!>` are not supported in standard SQL; use `>=` and `<=`.
- **Debugging**:
  - Test conditions individually in `WHERE` clauses.
  - Check for NULL handling with `IS NULL`/`IS NOT NULL`.
  - Verify DBMS support for non-standard operators like `!<` or `!>` if encountered.
- **Clarity**:
  - Use parentheses to clarify complex conditions (e.g., `WHERE (A = 1 OR A = 2) AND B = 3`).
  - Avoid `ANY`/`ALL` for simple queries; prefer joins or `IN` when possible.

### Common Pitfalls
- **NULL Handling**: Comparison operators (`=`, `<`, etc.) with NULL return UNKNOWN; use `IS NULL`/`IS NOT NULL`.
- **Division by Zero**: Avoid in arithmetic operations (use `CASE` or `NULLIF`).
- **Case Sensitivity**: `LIKE` and string comparisons may be case-sensitive in some DBMS.
- **Non-Standard Operators**: Avoid `!<` and `!>`; use `>=` and `<=` for portability.
- **Subquery Performance**: `ANY`, `ALL`, and `EXISTS` can be slow with large subqueries; optimize with joins.