# SQL Aggregate Functions Notes

## 1. Aggregate Functions

### Overview
- **Aggregate Functions**: SQL functions that perform calculations on a set of values in a column and return a single summarized value.
- Used in **Data Manipulation Language (DML)** queries, typically with the `SELECT` statement, to analyze or summarize data.
- Operate on groups of rows, often used with the `GROUP BY` clause to group data before applying the function.
- Common in reporting, analytics, and data summarization tasks.

### Purpose
- Summarize data (e.g., total sales, average salary, count of records).
- Provide insights from large datasets by reducing multiple rows into a single result.
- Ensure data integrity by working within the constraints of the database schema (e.g., keys, normalization).

### Common Aggregate Functions
1. **COUNT()**: Counts the number of rows or non-NULL values in a column.
   - Syntax: `COUNT(column_name | * | expression)`
2. **SUM()**: Calculates the sum of numeric values in a column.
   - Syntax: `SUM(column_name)`
3. **AVG()**: Computes the average of numeric values in a column.
   - Syntax: `AVG(column_name)`
4. **MIN()**: Finds the smallest value in a column (numeric, string, or date).
   - Syntax: `MIN(column_name)`
5. **MAX()**: Finds the largest value in a column (numeric, string, or date).
   - Syntax: `MAX(column_name)`

### Key Characteristics
- **Input**: Operate on a set of rows (or a group of rows when used with `GROUP BY`).
- **Output**: Return a single value per group or query.
- **NULL Handling**: Most aggregate functions (except `COUNT(*)`) ignore NULL values.
- **Data Types**:
  - `COUNT`: Works with any data type; returns an integer.
  - `SUM`, `AVG`: Work with numeric data types (e.g., INT, DECIMAL).
  - `MIN`, `MAX`: Work with numeric, string, or date data types.
- **Usage**: Often combined with `GROUP BY` to summarize data by categories or with `HAVING` to filter aggregated results.

### Example: Sample Database
- **Tables** (from a normalized database, as per previous topics):
  ```
  Table: Customers
  | CustomerID | CustomerName | CustomerCity |
  |------------|--------------|--------------|
  | 101        | John         | New York     |
  | 102        | Alice        | Chicago      |
  | 103        | Bob          | New York     |

  Table: Orders
  | OrderID | CustomerID | ItemID | OrderAmount | OrderDate  |
  |---------|------------|--------|-------------|------------|
  | 1       | 101        | 1      | 20.00       | 2025-06-01 |
  | 2       | 101        | 2      | 50.00       | 2025-06-02 |
  | 3       | 102        | 1      | 30.00       | 2025-06-03 |
  | 4       | NULL       | 3      | NULL        | 2025-06-04 |
  ```

### Basic Syntax and Examples
1. **COUNT**:
   - **Purpose**: Count rows or non-NULL values.
   - **Example**: Count total orders and non-NULL `CustomerID` values.
     ```sql
     SELECT COUNT(*) AS TotalOrders, COUNT(CustomerID) AS CustomerOrders
     FROM Orders;
     ```
     **Output**:
     ```
     | TotalOrders | CustomerOrders |
     |-------------|----------------|
     | 4           | 3              |
     ```
     - Note: `COUNT(*)` counts all rows; `COUNT(CustomerID)` ignores NULLs.

2. **SUM**:
   - **Purpose**: Sum numeric values.
   - **Example**: Calculate total order amount.
     ```sql
     SELECT SUM(OrderAmount) AS TotalSales
     FROM Orders;
     ```
     **Output**:
     ```
     | TotalSales |
     |------------|
     | 100.00     |
     ```
     - Note: Ignores NULL values (e.g., OrderID 4).

3. **AVG**:
   - **Purpose**: Compute average of numeric values.
   - **Example**: Calculate average order amount.
     ```sql
     SELECT AVG(OrderAmount) AS AvgOrderAmount
     FROM Orders;
     ```
     **Output**:
     ```
     | AvgOrderAmount |
     |----------------|
     | 33.3333        |
     ```
     - Note: Calculated as `100.00 / 3` (ignores NULL).

4. **MIN**:
   - **Purpose**: Find minimum value.
   - **Example**: Find earliest order date.
     ```sql
     SELECT MIN(OrderDate) AS EarliestOrder
     FROM Orders;
     ```
     **Output**:
     ```
     | EarliestOrder |
     |---------------|
     | 2025-06-01    |
     ```

5. **MAX**:
   - **Purpose**: Find maximum value.
   - **Example**: Find highest order amount.
     ```sql
     SELECT MAX(OrderAmount) AS HighestOrder
     FROM Orders;
     ```
     **Output**:
     ```
     | HighestOrder |
     |--------------|
     | 50.00        |
     ```

### Using Aggregate Functions with GROUP BY
- **Purpose**: Group rows by one or more columns and apply aggregate functions to each group.
- **Syntax**:
  ```sql
  SELECT column_to_group_by, AGGREGATE_FUNCTION(column_name)
  FROM table_name
  GROUP BY column_to_group_by;
  ```
- **Example**: Calculate total order amount per customer.
  ```sql
  SELECT c.CustomerName, SUM(o.OrderAmount) AS TotalSpent
  FROM Customers c
  JOIN Orders o ON c.CustomerID = o.CustomerID
  GROUP BY c.CustomerName;
  ```
  **Output**:
  ```
  | CustomerName | TotalSpent |
  |--------------|------------|
  | John         | 70.00      |
  | Alice        | 30.00      |
  ```
  - Note: Groups rows by `CustomerName` and sums `OrderAmount` for each group.

### Using Aggregate Functions with HAVING
- **Purpose**: Filter grouped results based on aggregate function results (unlike `WHERE`, which filters individual rows).
- **Syntax**:
  ```sql
  SELECT column_to_group_by, AGGREGATE_FUNCTION(column_name)
  FROM table_name
  GROUP BY column_to_group_by
  HAVING condition;
  ```
- **Example**: Find customers with total orders exceeding $50.
  ```sql
  SELECT c.CustomerName, SUM(o.OrderAmount) AS TotalSpent
  FROM Customers c
  JOIN Orders o ON c.CustomerID = o.CustomerID
  GROUP BY c.CustomerName
  HAVING SUM(o.OrderAmount) > 50;
  ```
  **Output**:
  ```
  | CustomerName | TotalSpent |
  |--------------|------------|
  | John         | 70.00      |
  ```

### Practical Example: Comprehensive Query
- **Scenario**: Analyze orders by city, including count, total, and average order amounts.
- **SQL Query**:
  ```sql
  SELECT c.CustomerCity, 
         COUNT(o.OrderID) AS OrderCount, 
         SUM(o.OrderAmount) AS TotalSales, 
         AVG(o.OrderAmount) AS AvgSale
  FROM Customers c
  JOIN Orders o ON c.CustomerID = o.CustomerID
  GROUP BY c.CustomerCity
  HAVING COUNT(o.OrderID) > 1
  ORDER BY TotalSales DESC;
  ```
  **Output** (based on sample data):
  ```
  | CustomerCity | OrderCount | TotalSales | AvgSale |
  |--------------|------------|------------|---------|
  | New York     | 2          | 70.00      | 35.00   |
  ```
  - Note: Only cities with more than one order are shown, sorted by total sales.

### Key Considerations
- **NULL Handling**: Aggregate functions (except `COUNT(*)`) skip NULL values, ensuring accurate calculations.
- **Performance**: Aggregations on large datasets may require indexing on grouped columns (e.g., `CustomerID`) for efficiency.
- **Data Types**: Ensure the column used in `SUM` or `AVG` is numeric; `MIN` and `MAX` work with strings and dates but require consistent data types.
- **Grouping**: Without `GROUP BY`, aggregate functions process all rows in the result set, returning a single value.
- **HAVING vs. WHERE**:
  - `WHERE`: Filters individual rows before aggregation.
  - `HAVING`: Filters groups after aggregation.
  - Example:
    ```sql
    SELECT CustomerID, SUM(OrderAmount) AS Total
    FROM Orders
    WHERE OrderDate >= '2025-06-02'
    GROUP BY CustomerID
    HAVING SUM(OrderAmount) > 30;
    ```
    - `WHERE` filters orders after June 2, 2025; `HAVING` filters groups with total > $30.

### Common Pitfalls
- **Mixing Aggregates and Non-Aggregates**: Non-aggregated columns in `SELECT` must appear in `GROUP BY`.
  - Incorrect: `SELECT CustomerName, OrderDate, SUM(OrderAmount) FROM Orders GROUP BY CustomerName;`
  - Correct: `SELECT CustomerName, SUM(OrderAmount) FROM Orders GROUP BY CustomerName;`
- **NULL Values**: Be aware that `COUNT(column_name)` excludes NULLs, which may affect results.
- **Empty Groups**: If no rows match a group, it won’t appear in the result unless using `LEFT JOIN`.

### Practical Tips
- Use `COUNT(*)` for total row counts, `COUNT(column_name)` for non-NULL counts.
- Combine with `ROUND` for readable `AVG` results:
  ```sql
  SELECT ROUND(AVG(OrderAmount), 2) AS AvgOrder FROM Orders;
  ```
- Use indexes on columns frequently used in `GROUP BY` or `WHERE` to improve performance.
- Test aggregate queries on small datasets to verify results before running on large tables.
- Combine with `JOIN` to aggregate data across normalized tables (e.g., `Customers` and `Orders`).