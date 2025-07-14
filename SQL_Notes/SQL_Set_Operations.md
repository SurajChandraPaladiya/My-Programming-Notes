# SQL Set Operations Notes

## SQL Set Operations

### Overview
- **Set Operations**: SQL operations that combine the results of two or more `SELECT` queries into a single result set, based on set theory principles (union, intersection, difference).
- **Purpose**: Combine, compare, or find differences between query results from the same or different tables, producing a unified output.
- **Key Concept**: Set operations treat query results as sets of rows, requiring compatible structures (same number of columns and compatible data types).
- **Context**: Used in `SELECT` queries to perform operations like combining data, finding common rows, or identifying unique rows.

### Characteristics of Set Operations
- **Column Compatibility**: All queries involved must return the same number of columns, with corresponding columns having compatible data types.
- **Column Names**: The result set uses column names from the first `SELECT` query.
- **Order of Rows**: The order of rows in the result is not guaranteed unless an `ORDER BY` clause is used.
- **Distinctness**: Most set operations (`UNION`, `INTERSECT`, `EXCEPT`) remove duplicates by default, unless specified otherwise (e.g., `UNION ALL`).

### Types of Set Operations
SQL supports four main set operations: **UNION**, **UNION ALL**, **INTERSECT**, and **EXCEPT** (or `MINUS` in some DBMS like Oracle). Each is detailed below.

1. **UNION**
   - **Definition**: Combines the results of two or more `SELECT` queries, removing duplicate rows to produce a single result set containing all unique rows.
   - **Use Case**: Merge data from multiple tables or queries with similar structures (e.g., combining customer lists from different regions).
   - **Characteristics**:
     - Removes duplicates (performs an implicit `DISTINCT`).
     - Computationally expensive due to duplicate removal.
   - **Syntax**:
     ```sql
     SELECT column1, column2, ...
     FROM table1
     [WHERE condition]
     UNION
     SELECT column1, column2, ...
     FROM table2
     [WHERE condition];
     ```

2. **UNION ALL**
   - **Definition**: Combines the results of two or more `SELECT` queries, including all rows (no duplicate removal).
   - **Use Case**: Merge data when duplicates are acceptable or desired (e.g., combining sales data from multiple stores).
   - **Characteristics**:
     - Faster than `UNION` because it does not remove duplicates.
     - Returns all rows, including duplicates.
   - **Syntax**: Same as `UNION`, replacing `UNION` with `UNION ALL`.

3. **INTERSECT**
   - **Definition**: Returns only the rows that appear in the result sets of both `SELECT` queries (common rows).
   - **Use Case**: Find records present in multiple datasets (e.g., customers who placed orders in two different time periods).
   - **Characteristics**:
     - Removes duplicates.
     - Not supported by all DBMS (e.g., MySQL does not natively support `INTERSECT`).
   - **Syntax**:
     ```sql
     SELECT column1, column2, ...
     FROM table1
     [WHERE condition]
     INTERSECT
     SELECT column1, column2, ...
     FROM table2
     [WHERE condition];
     ```

4. **EXCEPT (or MINUS)**
   - **Definition**: Returns rows from the first `SELECT` query that do not appear in the second query’s result set.
   - **Use Case**: Identify records unique to one dataset (e.g., customers who ordered in one period but not another).
   - **Characteristics**:
     - Removes duplicates.
     - Called `EXCEPT` in SQL Server, PostgreSQL; `MINUS` in Oracle.
     - Not supported by all DBMS (e.g., MySQL does not natively support `EXCEPT`).
   - **Syntax**:
     ```sql
     SELECT column1, column2, ...
     FROM table1
     [WHERE condition]
     EXCEPT
     SELECT column1, column2, ...
     FROM table2
     [WHERE condition];
     ```

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

Table: HighValueCustomers
| CustomerID (PK) | CustomerName | CustomerCity |
|-----------------|--------------|--------------|
| 101             | John         | New York     |
| 104             | Carol        | Miami        |
```

### Examples of Set Operations

1. **UNION**
   - **Scenario**: Combine customer lists from `Customers` and `HighValueCustomers`, removing duplicates.
   - **Query**:
     ```sql
     SELECT CustomerID, CustomerName, CustomerCity
     FROM Customers
     UNION
     SELECT CustomerID, CustomerName, CustomerCity
     FROM HighValueCustomers;
     ```
   - **Output**:
     ```
     | CustomerID | CustomerName | CustomerCity |
     |------------|--------------|--------------|
     | 101        | John         | New York     |
     | 102        | Alice        | Chicago      |
     | 103        | Bob          | Boston       |
     | 104        | Carol        | Miami        |
     ```
   - **Note**: John (CustomerID 101) appears only once due to duplicate removal.

2. **UNION ALL**
   - **Scenario**: Combine customer lists, keeping duplicates.
   - **Query**:
     ```sql
     SELECT CustomerID, CustomerName, CustomerCity
     FROM Customers
     UNION ALL
     SELECT CustomerID, CustomerName, CustomerCity
     FROM HighValueCustomers;
     ```
   - **Output**:
     ```
     | CustomerID | CustomerName | CustomerCity |
     |------------|--------------|--------------|
     | 101        | John         | New York     |
     | 102        | Alice        | Chicago      |
     | 103        | Bob          | Boston       |
     | 101        | John         | New York     |
     | 104        | Carol        | Miami        |
     ```
   - **Note**: John appears twice because `UNION ALL` retains duplicates.

3. **INTERSECT**
   - **Scenario**: Find customers present in both `Customers` and `HighValueCustomers`.
   - **Query**:
     ```sql
     SELECT CustomerID, CustomerName, CustomerCity
     FROM Customers
     INTERSECT
     SELECT CustomerID, CustomerName, CustomerCity
     FROM HighValueCustomers;
     ```
   - **Output**:
     ```
     | CustomerID | CustomerName | CustomerCity |
     |------------|--------------|--------------|
     | 101        | John         | New York     |
     ```
   - **Note**: Only John is common to both tables.
   - **Alternative for MySQL** (which lacks `INTERSECT`):
     ```sql
     SELECT DISTINCT c.CustomerID, c.CustomerName, c.CustomerCity
     FROM Customers c
     INNER JOIN HighValueCustomers h
     ON c.CustomerID = h.CustomerID
     AND c.CustomerName = h.CustomerName
     AND c.CustomerCity = h.CustomerCity;
     ```

4. **EXCEPT (or MINUS)**
   - **Scenario**: Find customers in `Customers` but not in `HighValueCustomers`.
   - **Query**:
     ```sql
     SELECT CustomerID, CustomerName, CustomerCity
     FROM Customers
     EXCEPT
     SELECT CustomerID, CustomerName, CustomerCity
     FROM HighValueCustomers;
     ```
   - **Output**:
     ```
     | CustomerID | CustomerName | CustomerCity |
     |------------|--------------|--------------|
     | 102        | Alice        | Chicago      |
     | 103        | Bob          | Boston       |
     ```
   - **Note**: Excludes John (CustomerID 101) and Carol (CustomerID 104).
   - **Alternative for MySQL** (which lacks `EXCEPT`):
     ```sql
     SELECT c.CustomerID, c.CustomerName, c.CustomerCity
     FROM Customers c
     LEFT JOIN HighValueCustomers h
     ON c.CustomerID = h.CustomerID
     WHERE h.CustomerID IS NULL;
     ```

### Using Set Operations with Other Clauses
Set operations can be combined with clauses like `WHERE`, `ORDER BY`, and `GROUP BY` to refine results.

- **Example**: Combine orders from two periods and sort by amount.
  ```sql
  SELECT CustomerID, Amount
  FROM Orders
  WHERE OrderDate = '2025-07-01'
  UNION
  SELECT CustomerID, Amount
  FROM Orders
  WHERE OrderDate = '2025-07-02'
  ORDER BY Amount DESC;
  ```
  **Output**:
  ```
  | CustomerID | Amount |
  |------------|--------|
  | 101        | 150.00 |
  | 101        | 100.00 |
  ```

- **Example with GROUP BY**: Total amount per customer across two queries.
  ```sql
  SELECT CustomerID, SUM(Amount) AS Total
  FROM (
      SELECT CustomerID, Amount
      FROM Orders
      WHERE OrderDate = '2025-07-01'
      UNION ALL
      SELECT CustomerID, Amount
      FROM Orders
      WHERE OrderDate = '2025-07-02'
  ) AS CombinedOrders
  GROUP BY CustomerID;
  ```
  **Output**:
  ```
  | CustomerID | Total  |
  |------------|--------|
  | 101        | 250.00 |
  ```

### Practical Tips
- **Choosing Set Operations**:
  - Use `UNION` when duplicates must be removed.
  - Use `UNION ALL` for better performance when duplicates are acceptable.
  - Use `INTERSECT` to find common records (or use `INNER JOIN` in MySQL).
  - Use `EXCEPT`/`MINUS` to find differences (or use `LEFT JOIN ... IS NULL` in MySQL).
- **Performance**:
  - `UNION ALL` is faster than `UNION` due to no duplicate removal.
  - Index columns used in `WHERE` conditions within queries.
  - Avoid combining large result sets unnecessarily.
- **Compatibility**:
  - Check DBMS support: MySQL lacks `INTERSECT` and `EXCEPT`; use joins as alternatives.
  - Oracle uses `MINUS` instead of `EXCEPT`.
- **Debugging**:
  - Verify column compatibility (number and data types) to avoid errors.
  - Test individual `SELECT` queries before combining.
  - Use `ORDER BY` at the end of the set operation to control result order.
- **Column Naming**: Ensure meaningful column names in the first `SELECT` query, as they define the result set’s column names.

### Common Pitfalls
- **Incompatible Columns**: Queries must have the same number of columns with compatible data types.
- **Duplicate Handling**: Use `UNION ALL` if duplicates are intended; `UNION` removes them unexpectedly.
- **Order of Results**: Without `ORDER BY`, row order is unpredictable.
- **DBMS Limitations**: MySQL’s lack of `INTERSECT` and `EXCEPT` requires workarounds using joins.
- **Performance with Large Sets**: Set operations on large datasets can be slow, especially `UNION` due to duplicate removal.