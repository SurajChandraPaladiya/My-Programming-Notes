# SQL Study Notes

## 1. What is Data, Database, Information, and Related Terms

### Overview
- **Data**: Raw, unprocessed facts or figures (e.g., numbers, text, images).
- **Information**: Processed, organized, or structured data that provides meaning or context.
- **Database**: An organized collection of data, typically stored and accessed electronically from a computer system.
- **Database Management System (DBMS)**: Software that manages databases, allowing users to create, read, update, and delete data.
- **Other Key Terms**:
  - **Table**: A structure in a database that stores data in rows and columns.
  - **Record (Row)**: A single entry in a table.
  - **Field (Column)**: A specific attribute of a record.
  - **Query**: A request for data or information from a database.
  - **Schema**: The structure or blueprint of a database, defining tables, fields, and relationships.

### Characteristics of a Database
- Organized and structured for efficient storage and retrieval.
- Supports data integrity, security, and consistency.
- Enables multiple users to access data simultaneously.

### Example
- **Data**: 123, "John", 25
- **Information**: "John is 25 years old."
- **Database**: A collection of tables storing customer details, orders, and products.
- **Table Example**:
  ```
  Table: Customers
  | ID  | Name | Age |
  |-----|------|-----|
  | 123 | John | 25  |
  ```

---

## 2. Types of Databases, DBMS, and RDBMS

### Types of Databases
1. **Hierarchical Database**:
   - Data organized in a tree-like structure (parent-child relationships).
   - Example: File systems, Windows Registry.
   - Pros: Fast for specific queries.
   - Cons: Rigid, hard to modify.
2. **Network Database**:
   - Data organized in a graph structure, allowing multiple relationships.
   - Example: Integrated Data Store (IDS).
   - Pros: Flexible relationships.
   - Cons: Complex to manage.
3. **Relational Database**:
   - Data stored in tables with rows and columns, linked by keys.
   - Example: MySQL, PostgreSQL, Oracle.
   - Pros: Easy to query, scalable, supports SQL.
   - Cons: Can be slower for complex joins.
4. **NoSQL Database**:
   - Non-relational, designed for unstructured or semi-structured data.
   - Types: Document (MongoDB), Key-Value (Redis), Column-Family (Cassandra), Graph (Neo4j).
   - Pros: Scalable, handles big data.
   - Cons: Less standardized, limited SQL support.
5. **Object-Oriented Database**:
   - Combines database capabilities with object-oriented programming.
   - Example: ObjectDB.
   - Pros: Good for complex data.
   - Cons: Less common, complex queries.

### DBMS (Database Management System)
- Software that manages databases, providing an interface between users and data.
- Functions:
  - Data storage, retrieval, and updates.
  - Ensures data integrity, security, and concurrency.
  - Supports backup and recovery.
- Examples: Oracle, MySQL, Microsoft SQL Server, PostgreSQL.

### RDBMS (Relational Database Management System)
- A type of DBMS that manages relational databases.
- Key Features:
  - Data stored in tables with relationships defined by keys (primary key, foreign key).
  - Supports SQL for querying and management.
  - Ensures ACID properties (Atomicity, Consistency, Isolation, Durability).
- Examples: MySQL, PostgreSQL, Oracle, SQL Server.

### Key Differences: DBMS vs. RDBMS
| Feature                | DBMS                          | RDBMS                         |
|------------------------|-------------------------------|-------------------------------|
| **Data Structure**     | Any (file-based, hierarchical)| Tables with relationships     |
| **Query Language**     | Varies (may not use SQL)      | Uses SQL                      |
| **Relationships**      | Limited or none               | Primary/Foreign keys          |
| **Examples**           | FoxPro, File Systems          | MySQL, PostgreSQL, Oracle     |

### Example: Relational Database
- **Tables**:
  ```
  Table: Employees
  | EmpID | Name  | DeptID |
  |-------|-------|--------|
  | 1     | Alice | 10     |

  Table: Departments
  | DeptID | DeptName |
  |--------|----------|
  | 10     | HR       |
  ```
- **SQL Query** (Joining tables):
  ```sql
  SELECT Name, DeptName
  FROM Employees
  JOIN Departments ON Employees.DeptID = Departments.DeptID;
  ```
  **Output**:
  ```
  | Name  | DeptName |
  |-------|----------|
  | Alice | HR       |
  ```

---

## 3. Database Design and Normalization

### Overview
- **Database Design**: The process of creating a structured database to store data efficiently, ensuring data integrity, consistency, and ease of retrieval.
- **Normalization**: A systematic approach to organizing data in a relational database to eliminate redundancy, avoid data anomalies (insertion, update, deletion), and ensure data consistency.
- **Goals**:
  - Eliminate redundant data to save storage and improve performance.
  - Prevent anomalies during data insertion, update, or deletion.
  - Ensure data integrity and logical relationships between tables.
  - Optimize query efficiency.

### Key Concepts
- **Entities**: Real-world objects represented in the database (e.g., Customer, Order, Product).
- **Attributes**: Properties of entities (e.g., CustomerID, OrderDate, ProductName).
- **Relationships**: Associations between entities (e.g., Customer places Order).
- **Functional Dependency**: A relationship where one attribute uniquely determines another (e.g., `CustomerID` → `CustomerName`).
- **Anomalies**:
  - **Insertion Anomaly**: Inability to add data without violating structure (e.g., cannot add a customer without an order in an unnormalized table).
  - **Update Anomaly**: Inconsistent data due to partial updates (e.g., updating a customer’s name in one place but not others).
  - **Deletion Anomaly**: Losing data unintentionally (e.g., deleting an order deletes the customer’s info).

### Normalization Forms
Normalization is achieved through a series of steps (normal forms). Each form builds on the previous one, with increasing strictness.

1. **First Normal Form (1NF)**:
   - **Rule**: Eliminate repeating groups; ensure all attributes are atomic (indivisible); each record is uniquely identifiable (primary key).
   - **Purpose**: Removes multi-valued attributes or repeating groups.
   - **Example**:
     - **Unnormalized**:
       ```
       Table: Orders
       | OrderID | Customer | ItemsOrdered         |
       |---------|----------|----------------------|
       | 1       | John     | Pen, Book, Notebook  |
       ```
     - **1NF (Split multi-valued attributes, add primary key)**:
       ```
       Table: Orders
       | OrderID | Customer | Item     |
       |---------|----------|----------|
       | 1       | John     | Pen      |
       | 1       | John     | Book     |
       | 1       | John     | Notebook |
       ```

2. **Second Normal Form (2NF)**:
   - **Rule**: Must be in 1NF; eliminate partial dependencies (non-key attributes must depend on the entire primary key, not part of it).
   - **Purpose**: Ensures attributes are fully dependent on the primary key, especially in tables with composite keys.
   - **Example**:
     - **1NF Table**:
       ```
       Table: OrderDetails
       | OrderID | ItemID | CustomerName | ItemName |
       |---------|--------|--------------|----------|
       | 1       | 101    | John         | Pen      |
       | 1       | 102    | John         | Book     |
       ```
       - Partial Dependency: `CustomerName` depends only on `OrderID`, not `ItemID`.
     - **2NF (Separate customer data)**:
       ```
       Table: Orders
       | OrderID | CustomerName |
       |---------|--------------|
       | 1       | John         |

       Table: OrderItems
       | OrderID | ItemID | ItemName |
       |---------|--------|----------|
       | 1       | 101    | Pen      |
       | 1       | 102    | Book     |
       ```

3. **Third Normal Form (3NF)**:
   - **Rule**: Must be in 2NF; eliminate transitive dependencies (non-key attributes should not depend on other non-key attributes).
   - **Purpose**: Removes dependencies where attributes indirectly depend on the primary key.
   - **Example**:
     - **2NF Table**:
       ```
       Table: Orders
       | OrderID | CustomerID | CustomerName | CustomerCity |
       |---------|------------|--------------|--------------|
       | 1       | 101        | John         | New York     |
       ```
       - Transitive Dependency: `CustomerCity` depends on `CustomerID`, not directly on `OrderID`.
     - **3NF (Separate customer data)**:
       ```
       Table: Customers
       | CustomerID | CustomerName | CustomerCity |
       |------------|--------------|--------------|
       | 101        | John         | New York     |

       Table: Orders
       | OrderID | CustomerID |
       |---------|------------|
       | 1       | 101        |
       ```

4. **Boyce-Codd Normal Form (BCNF)**:
   - **Rule**: Must be in 3NF; for every functional dependency (X → Y), X must be a superkey (a candidate key or superset).
   - **Purpose**: Addresses anomalies in cases where a non-key attribute determines another attribute.
   - **Example**:
     - **3NF Table**:
       ```
       Table: Teaching
       | Professor | Course | Department |
       |-----------|--------|------------|
       | Smith     | CS101  | CS         |
       | Smith     | CS102  | CS         |
       | Jones     | CS101  | CS         |
       ```
       - Dependency: `Course` → `Department` (non-key determines non-key).
       - **BCNF (Split table)**:
       ```
       Table: CourseDept
       | Course | Department |
       |--------|------------|
       | CS101  | CS         |
       | CS102  | CS         |

       Table: Teaching
       | Professor | Course |
       |-----------|--------|
       | Smith     | CS101  |
       | Smith     | CS102  |
       | Jones     | CS101  |
       ```

5. **Higher Normal Forms (4NF and 5NF)**:
   - **4NF**: Eliminates multi-valued dependencies (e.g., a table with multiple independent multi-valued attributes).
     - Example: Split a table storing `Employee | Skill | Project` where skills and projects are independent.
   - **5NF (Projection-Join Normal Form)**: Ensures no data loss when decomposing tables and recomposing them via joins.
   - **Note**: 4NF and 5NF are less common in practice but used in complex databases.

### Steps to Normalize a Database
1. Identify entities, attributes, and relationships.
2. Create an unnormalized table with all data.
3. Apply 1NF: Remove repeating groups, ensure atomicity, assign a primary key.
4. Apply 2NF: Remove partial dependencies by splitting tables.
5. Apply 3NF: Remove transitive dependencies by further splitting.
6. (Optional) Apply BCNF or higher forms for complex cases.
7. Define primary and foreign keys to establish relationships.

### Practical Example: Normalization Process
- **Unnormalized Data** (Customer orders with multiple items and customer details):
  ```
  Table: Unnormalized_Orders
  | OrderID | CustomerName | CustomerCity | ItemsOrdered         | ItemPrices       |
  |---------|--------------|--------------|----------------------|------------------|
  | 1       | John         | New York     | Pen, Book, Notebook  | $2, $10, $5      |
  ```

- **Step 1: 1NF** (Remove repeating groups, make atomic):
  ```
  Table: Orders_1NF
  | OrderID | CustomerName | CustomerCity | Item     | ItemPrice |
  |---------|--------------|--------------|----------|-----------|
  | 1       | John         | New York     | Pen      | $2        |
  | 1       | John         | New York     | Book     | $10       |
  | 1       | John         | New York     | Notebook | $5        |
  ```

- **Step 2: 2NF** (Remove partial dependencies; `CustomerName`, `CustomerCity` depend only on `CustomerID`):
  ```
  Table: Customers
  | CustomerID | CustomerName | CustomerCity |
  |------------|--------------|--------------|
  | 101        | John         | New York     |

  Table: Orders_2NF
  | OrderID | CustomerID | Item     | ItemPrice |
  |---------|------------|----------|-----------|
  | 1       | 101        | Pen      | $2        |
  | 1       | 101        | Book     | $10       |
  | 1       | 101        | Notebook | $5        |
  ```

- **Step 3: 3NF** (Remove transitive dependencies; `ItemPrice` depends on `Item`):
  ```
  Table: Customers
  | CustomerID | CustomerName | CustomerCity |
  |------------|--------------|--------------|
  | 101        | John         | New York     |

  Table: Items
  | ItemID | ItemName | ItemPrice |
  |--------|----------|-----------|
  | 1      | Pen      | $2        |
  | 2      | Book     | $10       |
  | 3      | Notebook | $5        |

  Table: Orders_3NF
  | OrderID | CustomerID | ItemID |
  |---------|------------|--------|
  | 1       | 101        | 1      |
  | 1       | 101        | 2      |
  | 1       | 101        | 3      |
  ```

### SQL Syntax for Normalized Tables
- **Create Normalized Tables**:
  ```sql
  CREATE TABLE Customers (
      CustomerID INT PRIMARY KEY,
      CustomerName VARCHAR(50) NOT NULL,
      CustomerCity VARCHAR(50)
  );

  CREATE TABLE Items (
      ItemID INT PRIMARY KEY,
      ItemName VARCHAR(50) NOT NULL,
      ItemPrice DECIMAL(10,2) NOT NULL
  );

  CREATE TABLE Orders (
      OrderID INT,
      CustomerID INT,
      ItemID INT,
      PRIMARY KEY (OrderID, ItemID),
      FOREIGN KEY (CustomerID) REFERENCES Customers(CustomerID),
      FOREIGN KEY (ItemID) REFERENCES Items(ItemID)
  );
  ```

- **Insert Data**:
  ```sql
  INSERT INTO Customers (CustomerID, CustomerName, CustomerCity)
  VALUES (101, 'John', 'New York');

  INSERT INTO Items (ItemID, ItemName, ItemPrice)
  VALUES (1, 'Pen', 2.00), (2, 'Book', 10.00), (3, 'Notebook', 5.00);

  INSERT INTO Orders (OrderID, CustomerID, ItemID)
  VALUES (1, 101, 1), (1, 101, 2), (1, 101, 3);
  ```

- **Query Example** (Retrieve order details):
  ```sql
  SELECT o.OrderID, c.CustomerName, i.ItemName, i.ItemPrice
  FROM Orders o
  JOIN Customers c ON o.CustomerID = c.CustomerID
  JOIN Items i ON o.ItemID = i.ItemID;
  ```
  **Output**:
  ```
  | OrderID | CustomerName | ItemName | ItemPrice |
  |---------|--------------|----------|-----------|
  | 1       | John         | Pen      | 2.00      |
  | 1       | John         | Book     | 10.00     |
  | 1       | John         | Notebook | 5.00      |
  ```

### Advantages of Normalization
- **Reduces Redundancy**: Saves storage by eliminating duplicate data (e.g., `CustomerName` stored once in `Customers` table).
- **Prevents Anomalies**:
  - **Insertion**: Can add a customer without an order.
  - **Update**: Updating `CustomerCity` in one place updates all related orders.
  - **Deletion**: Deleting an order doesn’t delete customer or item data.
- **Improves Consistency**: Ensures data integrity through relationships and constraints.
- **Scalability**: Normalized tables are easier to maintain and query efficiently.

### Disadvantages of Normalization
- **Increased Complexity**: More tables mean more joins, which can complicate queries.
- **Performance Overhead**: Joins can be slower for large datasets compared to denormalized tables.
- **Design Effort**: Requires careful planning to identify dependencies and relationships.

### Denormalization
- **Definition**: Intentionally introducing redundancy by combining tables to improve query performance, often at the cost of data integrity.
- **Use Case**: Read-heavy applications (e.g., data warehouses) where speed is prioritized over strict consistency.
- **Example**: Combining `Customers` and `Orders` into one table for faster reporting, accepting some redundancy.

### Practical Tips
- Normalize to at least 3NF for most transactional databases (OLTP systems).
- Consider denormalization for analytical databases (OLAP systems) where performance is critical.
- Use Entity-Relationship Diagrams (ERDs) to visualize entities, attributes, and relationships before normalizing.
- Validate normalization by checking for anomalies and ensuring all functional dependencies are satisfied.
---


## 4. Keys in Databases

### Overview
- **Keys**: Attributes or sets of attributes used to uniquely identify records, enforce relationships, and ensure data integrity in a relational database.
- Keys are fundamental for designing, querying, and maintaining relational databases, ensuring efficient data retrieval and consistency.

### Types of Keys
1. **Super Key**:
   - A set of one or more attributes that uniquely identifies a record in a table.
   - Can include more attributes than necessary for uniqueness.
   - Example: In a `Customers` table, `{CustomerID, Name, Email}` or `{CustomerID}` can uniquely identify a record.
2. **Candidate Key**:
   - A minimal super key (no subset of it can uniquely identify records).
   - Multiple candidate keys may exist; one is chosen as the primary key.
   - Example: In a `Customers` table, `CustomerID` and `Email` (if unique) are candidate keys.
3. **Primary Key**:
   - A candidate key chosen to uniquely identify records in a table.
   - Must be unique and cannot be NULL.
   - Only one primary key per table.
   - Example: `CustomerID` in a `Customers` table.
4. **Foreign Key**:
   - An attribute (or set of attributes) in one table that references the primary key (or candidate key) of another table.
   - Ensures referential integrity by linking related tables.
   - Example: `CustomerID` in an `Orders` table referencing `CustomerID` in `Customers`.
5. **Composite Key (Compound Key)**:
   - A primary key consisting of two or more attributes to ensure uniqueness.
   - Used when no single attribute is unique.
   - Example: `(OrderID, ItemID)` in an `OrderDetails` table.
6. **Unique Key**:
   - Ensures all values in a column (or set of columns) are unique, but may allow NULL values (unlike primary key).
   - A table can have multiple unique keys.
   - Example: `Email` in a `Customers` table.
7. **Alternate Key**:
   - A candidate key that is not chosen as the primary key.
   - Example: If `CustomerID` is the primary key, `Email` (if unique) is an alternate key.
8. **Surrogate Key**:
   - An artificially generated key (e.g., auto-incremented ID) used as a primary key when no natural unique attribute exists.
   - Example: An auto-incremented `EmployeeID` in an `Employees` table.
9. **Natural Key**:
   - A key based on real-world, meaningful data (e.g., Social Security Number, ISBN).
   - Example: `SSN` in an `Employees` table, if guaranteed to be unique.

### Key Characteristics
- **Super Key**: May include redundant attributes; not necessarily minimal.
- **Candidate Key**: Minimal super key; sufficient to uniquely identify records.
- **Primary Key**: Chosen for simplicity, performance, and stability (e.g., unlikely to change).
- **Foreign Key**: Enforces relationships and referential integrity.
- **Composite Key**: Used in junction tables (e.g., many-to-many relationships).
- **Unique Key**: Ensures uniqueness but allows flexibility (e.g., NULLs in some DBMS).
- **Surrogate Key**: Simplifies design when natural keys are absent or unreliable.
- **Natural Key**: Meaningful but may change (e.g., email addresses), causing maintenance issues.

### Example
- **Tables with Keys**:
  ```
  Table: Customers
  | CustomerID (PK) | Name  | Email (Unique, Alternate) | SSN (Natural, Alternate) |
  |-----------------|-------|--------------------------|--------------------------|
  | 101             | John  | john@email.com           | 123-45-6789              |

  Table: Orders
  | OrderID (PK) | CustomerID (FK) | Item     |
  |--------------|-----------------|----------|
  | 1            | 101             | Pen      |

  Table: OrderDetails
  | OrderID (PK, Composite) | ItemID (PK, Composite) | Quantity |
  |-------------------------|------------------------|----------|
  | 1                       | 201                    | 2        |
  ```
- **Key Details**:
  - **Super Keys** in `Customers`: `{CustomerID}`, `{CustomerID, Name}`, `{Email}`, `{SSN, Name}`.
  - **Candidate Keys** in `Customers`: `{CustomerID}`, `{Email}`, `{SSN}`.
  - **Primary Key** in `Customers`: `CustomerID`.
  - **Alternate Keys** in `Customers`: `Email`, `SSN`.
  - **Foreign Key** in `Orders`: `CustomerID` references `Customers(CustomerID)`.
  - **Composite Key** in `OrderDetails`: `(OrderID, ItemID)`.

### SQL Syntax for Keys
- **Primary Key**:
  ```sql
  CREATE TABLE Customers (
      CustomerID INT PRIMARY KEY,
      Name VARCHAR(50) NOT NULL,
      Email VARCHAR(50) UNIQUE,
      SSN VARCHAR(11) UNIQUE
  );
  ```
- **Foreign Key**:
  ```sql
  CREATE TABLE Orders (
      OrderID INT PRIMARY KEY,
      CustomerID INT,
      Item VARCHAR(50),
      FOREIGN KEY (CustomerID) REFERENCES Customers(CustomerID)
  );
  ```
- **Composite Key**:
  ```sql
  CREATE TABLE OrderDetails (
      OrderID INT,
      ItemID INT,
      Quantity INT,
      PRIMARY KEY (OrderID, ItemID),
      FOREIGN KEY (OrderID) REFERENCES Orders(OrderID),
      FOREIGN KEY (ItemID) REFERENCES Items(ItemID)
  );
  ```
- **Unique Key**:
  ```sql
  CREATE TABLE Employees (
      EmployeeID INT PRIMARY KEY,
      Email VARCHAR(50) UNIQUE,
      SSN VARCHAR(11) UNIQUE,
      Name VARCHAR(50)
  );
  ```
- **Surrogate Key (Auto-Increment)**:
  ```sql
  CREATE TABLE Products (
      ProductID INT PRIMARY KEY AUTO_INCREMENT,
      ProductName VARCHAR(50),
      Price DECIMAL(10,2)
  );
  ```

### Practical Example: Applying Keys
- **Scenario**: A database for a library system with books, borrowers, and loans.
- **Tables**:
  ```
  Table: Borrowers
  | BorrowerID (PK, Surrogate) | Name | Email (Unique, Alternate) | SSN (Natural, Alternate) |
  |----------------------------|------|--------------------------|--------------------------|
  | 1                          | Alice| alice@email.com          | 123-45-6789              |

  Table: Books
  | BookID (PK, Surrogate) | Title       | ISBN (Natural, Unique) |
  |------------------------|-------------|------------------------|
  | 101                    | SQL Basics  | 978-1234567890         |

  Table: Loans
  | LoanID (PK, Surrogate) | BorrowerID (FK) | BookID (FK) | LoanDate   |
  |------------------------|-----------------|-------------|------------|
  | 1001                   | 1               | 101         | 2025-07-01 |
  ```
- **SQL Creation**:
  ```sql
  CREATE TABLE Borrowers (
      BorrowerID INT PRIMARY KEY AUTO_INCREMENT,
      Name VARCHAR(50) NOT NULL,
      Email VARCHAR(50) UNIQUE,
      SSN VARCHAR(11) UNIQUE
  );

  CREATE TABLE Books (
      BookID INT PRIMARY KEY AUTO_INCREMENT,
      Title VARCHAR(100) NOT NULL,
      ISBN VARCHAR(13) UNIQUE
  );

  CREATE TABLE Loans (
      LoanID INT PRIMARY KEY AUTO_INCREMENT,
      BorrowerID INT,
      BookID INT,
      LoanDate DATE,
      FOREIGN KEY (BorrowerID) REFERENCES Borrowers(BorrowerID),
      FOREIGN KEY (BookID) REFERENCES Books(BookID)
  );
  ```
- **Query Example** (Retrieve loan details):
  ```sql
  SELECT l.LoanID, b.Name, k.Title, l.LoanDate
  FROM Loans l
  JOIN Borrowers b ON l.BorrowerID = b.BorrowerID
  JOIN Books k ON l.BookID = k.BookID;
  ```
  **Output**:
  ```
  | LoanID | Name  | Title      | LoanDate   |
  |--------|-------|------------|------------|
  | 1001   | Alice | SQL Basics | 2025-07-01 |
  ```

### Importance of Keys
- **Data Integrity**: Primary and unique keys ensure no duplicate or NULL records; foreign keys maintain valid relationships.
- **Efficient Querying**: Keys enable fast lookups and joins (e.g., indexing on `CustomerID` speeds up queries).
- **Normalization**: Keys (especially primary and foreign) are critical for splitting tables during normalization.
- **Uniqueness**: Super, candidate, and alternate keys provide flexibility in identifying records uniquely.
---

## 5. Data Integrity and Constraints

### Overview
- **Data Integrity**: Ensuring the accuracy, consistency, and reliability of data in a database.
- **Constraints**: Rules enforced by the DBMS to maintain data integrity.

### Types of Data Integrity
1. **Entity Integrity**: Ensures each record is uniquely identifiable (via primary keys).
2. **Referential Integrity**: Ensures foreign keys reference valid primary keys.
3. **Domain Integrity**: Ensures data values meet specific conditions (e.g., data type, range).
4. **User-Defined Integrity**: Custom rules defined by the user (e.g., business rules).

### Common Constraints
1. **PRIMARY KEY**: Ensures uniqueness and non-NULL values.
2. **FOREIGN KEY**: Ensures valid references to another table’s primary key.
3. **UNIQUE**: Ensures all values in a column are distinct.
4. **NOT NULL**: Ensures a column cannot have NULL values.
5. **CHECK**: Ensures values meet a specific condition (e.g., `Age > 0`).
6. **DEFAULT**: Sets a default value if none is provided.

### Example
- **Table with Constraints**:
  ```
  Table: Employees
  | EmpID (PK) | Name (NOT NULL) | Age (CHECK) | DeptID (FK) |
  |------------|-----------------|-------------|-------------|
  | 1          | Alice           | 25          | 10          |
  ```
- **Violations**:
  - Inserting `NULL` into `Name` fails (NOT NULL).
  - Inserting `Age = -5` fails (CHECK Age > 0).
  - Inserting `DeptID = 99` fails if `99` doesn’t exist in the Departments table.

### Basic Syntax
- **Constraints in Table Creation**:
  ```sql
  CREATE TABLE Employees (
      EmpID INT PRIMARY KEY,
      Name VARCHAR(50) NOT NULL,
      Age INT CHECK (Age > 0),
      DeptID INT,
      FOREIGN KEY (DeptID) REFERENCES Departments(DeptID),
      Salary DECIMAL(10,2) DEFAULT 0.00
  );
  ```


## 6. What is SQL, History, and How It Works

### What is SQL?
- **SQL (Structured Query Language)**: A standardized programming language used to manage and manipulate relational databases.
- Functions:
  - Create, modify, and delete database structures (tables, schemas).
  - Insert, update, delete, and retrieve data.
  - Manage database permissions and security.

### History
- **1970s**: Developed by IBM (Donald D. Chamberlin and Raymond F. Boyce) as "SEQUEL" for System R.
- **1979**: Renamed to SQL and adopted by Oracle (then Relational Software).
- **1986**: Standardized by ANSI (American National Standards Institute).
- **Current**: SQL is widely used, with variations across DBMS like MySQL, PostgreSQL, SQL Server, etc.

### How SQL Works
- SQL interacts with a DBMS to process queries.
- Steps:
  1. User writes an SQL query (e.g., `SELECT * FROM Customers;`).
  2. DBMS parses and optimizes the query.
  3. DBMS retrieves or manipulates data from the database.
  4. Results are returned to the user.
- SQL is **declarative**: Users specify *what* data they want, not *how* to retrieve it.

### Types of SQL Commands
1. **DDL (Data Definition Language)**: Defines database structure (e.g., `CREATE`, `ALTER`, `DROP`).
2. **DML (Data Manipulation Language)**: Manipulates data (e.g., `INSERT`, `UPDATE`, `DELETE`, `SELECT`).
3. **DCL (Data Control Language)**: Manages access and permissions (e.g., `GRANT`, `REVOKE`).
4. **TCL (Transaction Control Language)**: Manages transactions (e.g., `COMMIT`, `ROLLBACK`).

### Basic Syntax Examples
- **Create a Table (DDL)**:
  ```sql
  CREATE TABLE Students (
      ID INT PRIMARY KEY,
      Name VARCHAR(50),
      Age INT
  );
  ```
- **Insert Data (DML)**:
  ```sql
  INSERT INTO Students (ID, Name, Age) VALUES (1, 'Alice', 20);
  ```
- **Retrieve Data (DML)**:
  ```sql
  SELECT * FROM Students WHERE Age > 18;
  ```
- **Grant Permission (DCL)**:
  ```sql
  GRANT SELECT ON Students TO User1;
  ```
- **Commit Transaction (TCL)**:
  ```sql
  COMMIT;
  ```
---