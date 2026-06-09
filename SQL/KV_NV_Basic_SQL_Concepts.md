# Basic SQL Concepts for KV/NV Interview
## Kendriya Vidyalaya and Navodaya Vidyalaya Interview Preparation

This note is designed for quick revision of basic SQL concepts with KV/NV interview expectations in mind. It focuses on clear definitions, common differences, and small examples that are easy to explain in an interview.

---

## 1. What is SQL?

SQL stands for Structured Query Language.
It is used to create, manage, and retrieve data from relational databases.

### Interview Answer
SQL is a standard language used to store, retrieve, update, and manage data in a relational database.

---

## 2. What is a Database?

A database is an organized collection of related data.

### Example
A school database can store:
- Student details
- Teacher details
- Subject details
- Marks records

---

## 3. What is DBMS?

DBMS stands for Database Management System.
It is software used to create and manage databases.

### Examples of DBMS
- MySQL
- Oracle
- SQL Server
- PostgreSQL

### Interview Answer
A DBMS is software that allows users to create, store, manage, and retrieve data efficiently.

---

## 4. What is RDBMS?

RDBMS stands for Relational Database Management System.
It stores data in the form of tables.

### Examples
- MySQL
- Oracle
- PostgreSQL

### Difference between DBMS and RDBMS

| DBMS | RDBMS |
|---|---|
| Stores data in files or simple structures | Stores data in related tables |
| May not support relationships | Supports relationships between tables |
| Less suitable for large systems | Better for large structured systems |

---

## 5. Table, Row, Column

### Table
A table stores data in a structured format.

### Row
A row represents one record.

### Column
A column represents one attribute or field.

### Example

| RollNo | Name | Class |
|---|---|---|
| 1 | Amit | 12A |
| 2 | Neha | 12B |

Here:
- `Student` is a table
- Each horizontal entry is a row
- `RollNo`, `Name`, and `Class` are columns

---

## 6. SQL Sub-Languages

| Type | Full Form | Commands |
|---|---|---|
| DDL | Data Definition Language | CREATE, ALTER, DROP, TRUNCATE |
| DML | Data Manipulation Language | INSERT, UPDATE, DELETE |
| DQL | Data Query Language | SELECT |
| DCL | Data Control Language | GRANT, REVOKE |
| TCL | Transaction Control Language | COMMIT, ROLLBACK, SAVEPOINT |

### Interview Tip
In school interviews, they often ask the full form and one or two examples from each category.

---

## 7. DDL Commands

DDL commands are used to define or change the structure of a table.

### CREATE
Used to create a new table.

```sql
CREATE TABLE Student (
    RollNo INT PRIMARY KEY,
    Name VARCHAR(50),
    Class VARCHAR(10)
);
```

### ALTER
Used to change the structure of an existing table.

```sql
ALTER TABLE Student ADD City VARCHAR(30);
```

### DROP
Used to delete the entire table structure and data.

```sql
DROP TABLE Student;
```

### TRUNCATE
Used to remove all rows from a table but keeps the table structure.

```sql
TRUNCATE TABLE Student;
```

---

## 8. DML Commands

DML commands are used to manipulate table data.

### INSERT
```sql
INSERT INTO Student VALUES (1, 'Amit', '12A');
```

### UPDATE
```sql
UPDATE Student SET Class = '12B' WHERE RollNo = 1;
```

### DELETE
```sql
DELETE FROM Student WHERE RollNo = 1;
```

---

## 9. SELECT Statement

`SELECT` is used to retrieve data from a table.

### Display all data
```sql
SELECT * FROM Student;
```

### Display selected columns
```sql
SELECT Name, Class FROM Student;
```

### Use condition with WHERE
```sql
SELECT * FROM Student WHERE Class = '12A';
```

---

## 10. WHERE Clause

`WHERE` is used to filter rows based on a condition.

### Examples
```sql
SELECT * FROM Student WHERE RollNo = 1;
SELECT * FROM Student WHERE Name = 'Amit';
SELECT * FROM Student WHERE Marks > 80;
```

### Common Operators
- `=` equal to
- `>` greater than
- `<` less than
- `>=` greater than or equal to
- `<=` less than or equal to
- `<>` not equal to

---

## 11. ORDER BY Clause

Used to sort records in ascending or descending order.

```sql
SELECT * FROM Student ORDER BY Name ASC;
SELECT * FROM Student ORDER BY Marks DESC;
```

---

## 12. DISTINCT Keyword

Used to display only unique values.

```sql
SELECT DISTINCT Class FROM Student;
```

---

## 13. LIKE Operator

Used for pattern matching.

### Examples
```sql
SELECT * FROM Student WHERE Name LIKE 'A%';
SELECT * FROM Student WHERE Name LIKE '%a';
SELECT * FROM Student WHERE Name LIKE '_m%';
```

### Meaning
- `A%` means starts with A
- `%a` means ends with a
- `_m%` means second letter is m

---

## 14. NULL Value

`NULL` means no value, missing value, or unknown value.

### Correct usage
```sql
SELECT * FROM Student WHERE City IS NULL;
SELECT * FROM Student WHERE City IS NOT NULL;
```

### Important Interview Point
Do not use:
```sql
SELECT * FROM Student WHERE City = NULL;
```
This is incorrect.

---

## 15. Aggregate Functions

Aggregate functions perform calculations on multiple rows.

| Function | Use |
|---|---|
| COUNT() | Counts rows |
| SUM() | Adds values |
| AVG() | Finds average |
| MAX() | Finds maximum value |
| MIN() | Finds minimum value |

### Examples
```sql
SELECT COUNT(*) FROM Student;
SELECT AVG(Marks) FROM Student;
SELECT MAX(Marks) FROM Student;
SELECT MIN(Marks) FROM Student;
```

---

## 16. GROUP BY Clause

Used to group rows with the same values.

```sql
SELECT Class, COUNT(*)
FROM Student
GROUP BY Class;
```

### Interview Explanation
If you want class-wise total students, you use `GROUP BY Class`.

---

## 17. HAVING Clause

`HAVING` is used to filter grouped data.

```sql
SELECT Class, COUNT(*)
FROM Student
GROUP BY Class
HAVING COUNT(*) > 2;
```

### Difference between WHERE and HAVING

| WHERE | HAVING |
|---|---|
| Filters rows | Filters groups |
| Used before grouping | Used after grouping |
| Cannot use aggregate functions directly | Can use aggregate functions |

---

## 18. Keys in SQL

### Primary Key
- Uniquely identifies each row
- Cannot be NULL
- Cannot contain duplicate values

### Foreign Key
- Refers to the primary key of another table
- Creates relationship between two tables

### Candidate Key
- A column that can uniquely identify records
- One candidate key becomes the primary key

### Unique Key
- Prevents duplicate values
- Usually allows NULL depending on DBMS

---

## 19. Primary Key and Foreign Key Example

```sql
CREATE TABLE Student (
    RollNo INT PRIMARY KEY,
    Name VARCHAR(50)
);

CREATE TABLE Marks (
    RollNo INT,
    Subject VARCHAR(30),
    Score INT,
    FOREIGN KEY (RollNo) REFERENCES Student(RollNo)
);
```

### Interview Explanation
`Student.RollNo` is the primary key.
`Marks.RollNo` is the foreign key.
This connects the student table with the marks table.

---

## 20. Joins

Joins are used to combine data from two or more tables.

### INNER JOIN
Returns matching rows from both tables.

```sql
SELECT s.Name, m.Subject, m.Score
FROM Student s
INNER JOIN Marks m ON s.RollNo = m.RollNo;
```

### LEFT JOIN
Returns all rows from left table and matching rows from right table.

```sql
SELECT s.Name, m.Subject, m.Score
FROM Student s
LEFT JOIN Marks m ON s.RollNo = m.RollNo;
```

### Interview Tip
KV/NV panels often ask basic join definitions with one example.

---

## 21. Constraints

Constraints are rules applied on columns.

### Common Constraints
- `NOT NULL`
- `UNIQUE`
- `PRIMARY KEY`
- `FOREIGN KEY`
- `CHECK`
- `DEFAULT`

### Example
```sql
CREATE TABLE Student (
    RollNo INT PRIMARY KEY,
    Name VARCHAR(50) NOT NULL,
    Marks INT CHECK (Marks >= 0 AND Marks <= 100),
    City VARCHAR(30) DEFAULT 'Delhi'
);
```

---

## 22. Difference Questions Common in Interview

### DELETE vs TRUNCATE vs DROP

| DELETE | TRUNCATE | DROP |
|---|---|---|
| Removes selected rows | Removes all rows | Removes complete table |
| WHERE clause can be used | WHERE cannot be used | Table itself is deleted |
| Can rollback in many cases | Faster than DELETE | Structure also removed |

### CHAR vs VARCHAR

| CHAR | VARCHAR |
|---|---|
| Fixed length | Variable length |
| Wastes space if text is short | Saves space |
| Faster in some fixed-length cases | More flexible |

---

## 23. Basic Interview Queries to Practice

### Display all students
```sql
SELECT * FROM Student;
```

### Display names of students from class 12A
```sql
SELECT Name FROM Student WHERE Class = '12A';
```

### Count total students
```sql
SELECT COUNT(*) FROM Student;
```

### Show highest marks
```sql
SELECT MAX(Marks) FROM Student;
```

### Show class-wise student count
```sql
SELECT Class, COUNT(*) FROM Student GROUP BY Class;
```

### Show students whose name starts with A
```sql
SELECT * FROM Student WHERE Name LIKE 'A%';
```

### Show students with NULL city
```sql
SELECT * FROM Student WHERE City IS NULL;
```

---

## 24. KV/NV Interview Focus Areas

For KV/NV interviews, prepare these topics very well:
- SQL basics and definitions
- DDL and DML commands
- Primary key and foreign key
- WHERE, GROUP BY, HAVING
- Aggregate functions
- Joins
- Constraints
- NULL handling
- Difference between DELETE, TRUNCATE, and DROP
- Small board-based query writing

---

## 25. How to Answer in Interview

When answering SQL questions in KV/NV interviews:
- Start with the definition
- Give one simple example
- If asked for difference, explain in points
- If asked for query, write clearly and explain each clause
- Keep language simple and teacher-like

---

## 26. One-Minute SQL Revision

SQL is used to manage relational databases. Data is stored in tables made of rows and columns. Main SQL command groups include DDL, DML, DQL, DCL, and TCL. Primary key uniquely identifies a record, and foreign key creates relationship between tables. `SELECT` retrieves data, `WHERE` filters rows, `GROUP BY` groups rows, and `HAVING` filters grouped data. Aggregate functions like `COUNT`, `SUM`, and `AVG` are commonly asked. `JOIN` is used to combine data from tables. `NULL` represents missing value, and it should be checked with `IS NULL`.

---

## 27. Final Preparation Suggestion

Revise these concepts daily and practice writing at least 10 small SQL queries by hand. In KV/NV interviews, clarity of explanation is as important as technical correctness.
