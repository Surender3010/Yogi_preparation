# SQL Interview Preparation Guide
## Kendriya Vidyalaya (KV) & Navodaya Vidyalaya (NV) — PGT/TGT Computer Science

> **Date Prepared:** 2026-06-09  
> **Scope:** CBSE Class 11/12 aligned SQL topics + standard interview questions

---

## Table of Contents

1. [Basic Concepts](#1-basic-concepts)
2. [Constraints](#2-constraints)
3. [Joins](#3-joins)
4. [Aggregate Functions](#4-aggregate-functions)
5. [Subqueries & Set Operations](#5-subqueries--set-operations)
6. [String & Date Functions](#6-string--date-functions)
7. [Normalization (Theory)](#7-normalization-theory)
8. [Views, Indexes & Stored Procedures](#8-views-indexes--stored-procedures)
9. [Transaction Control (TCL) & ACID](#9-transaction-control-tcl--acid)
10. [Practical Query Writing](#10-practical-query-writing)
11. [Key Differences — Quick Reference](#11-key-differences--quick-reference)
12. [Relational Algebra Basics](#12-relational-algebra-basics)
13. [Interview Tips](#13-interview-tips)

---

## 1. Basic Concepts

### What is SQL?
SQL (Structured Query Language) is a standard language for managing and manipulating relational databases.

### SQL Sub-Languages

| Sub-Language | Full Form | Commands | Description |
|---|---|---|---|
| DDL | Data Definition Language | CREATE, ALTER, DROP, TRUNCATE | Defines database structure |
| DML | Data Manipulation Language | INSERT, UPDATE, DELETE | Manipulates data |
| DQL | Data Query Language | SELECT | Retrieves data |
| DCL | Data Control Language | GRANT, REVOKE | Controls access |
| TCL | Transaction Control Language | COMMIT, ROLLBACK, SAVEPOINT | Manages transactions |

---

### Q1: Difference between DDL and DML?

| Feature | DDL | DML |
|---|---|---|
| Purpose | Defines/modifies structure | Manipulates data |
| Commands | CREATE, ALTER, DROP, TRUNCATE | INSERT, UPDATE, DELETE, SELECT |
| Auto-commit | Yes (implicit commit) | No (explicit COMMIT needed) |
| Rollback | Not possible | Possible |
| Affects | Schema/structure | Data/rows |

---

### Q2: What is a Primary Key?
- Uniquely identifies each row in a table
- Cannot contain NULL values
- Cannot have duplicate values
- Only **ONE** primary key per table

```sql
CREATE TABLE students (
    student_id INT PRIMARY KEY,
    name VARCHAR(50)
);
```

---

### Q3: Primary Key vs Unique Key

| Feature | Primary Key | Unique Key |
|---|---|---|
| Number per table | Only one | Multiple allowed |
| NULL values | Not allowed | One NULL allowed |
| Index type | Clustered index | Non-clustered index |
| Purpose | Row identification | Enforce uniqueness |

---

### Q4: What is a Foreign Key?
A column (or set of columns) that references the **Primary Key** of another table. Enforces **referential integrity**.

```sql
CREATE TABLE marks (
    mark_id INT PRIMARY KEY,
    student_id INT,
    subject VARCHAR(30),
    score INT,
    FOREIGN KEY (student_id) REFERENCES students(student_id)
);
```

---

## 2. Constraints

### Types of Constraints

| Constraint | Description | Example |
|---|---|---|
| `NOT NULL` | Value cannot be empty | `name VARCHAR(50) NOT NULL` |
| `UNIQUE` | All values must be different | `email VARCHAR(100) UNIQUE` |
| `PRIMARY KEY` | NOT NULL + UNIQUE combined | `id INT PRIMARY KEY` |
| `FOREIGN KEY` | Links to another table's PK | `FOREIGN KEY (dept_id) REFERENCES dept(id)` |
| `CHECK` | Validates a condition | `CHECK (marks >= 0 AND marks <= 100)` |
| `DEFAULT` | Sets a default value | `grade CHAR(1) DEFAULT 'F'` |

```sql
CREATE TABLE students (
    id       INT PRIMARY KEY,
    name     VARCHAR(50) NOT NULL,
    email    VARCHAR(100) UNIQUE,
    marks    INT CHECK (marks >= 0 AND marks <= 100),
    grade    CHAR(1) DEFAULT 'F',
    class_id INT,
    FOREIGN KEY (class_id) REFERENCES classes(class_id)
);
```

---

## 3. Joins

### Types of JOINs

| Join Type | Returns |
|---|---|
| INNER JOIN | Matching rows from **both** tables |
| LEFT JOIN | All rows from **left** + matching from right |
| RIGHT JOIN | All rows from **right** + matching from left |
| FULL OUTER JOIN | All rows from **both** tables |
| SELF JOIN | Table joined with **itself** |
| CROSS JOIN | Cartesian product of both tables |

```sql
-- Sample Tables
-- students(id, name, class_id)
-- classes(class_id, class_name, teacher)

-- INNER JOIN: only students who have a class assigned
SELECT s.name, c.class_name
FROM students s
INNER JOIN classes c ON s.class_id = c.class_id;

-- LEFT JOIN: all students, even those without a class
SELECT s.name, c.class_name
FROM students s
LEFT JOIN classes c ON s.class_id = c.class_id;

-- RIGHT JOIN: all classes, even those without students
SELECT s.name, c.class_name
FROM students s
RIGHT JOIN classes c ON s.class_id = c.class_id;

-- SELF JOIN: employee and their manager (both in same table)
SELECT e.name AS employee, m.name AS manager
FROM employees e
JOIN employees m ON e.manager_id = m.id;
```

---

### Q5: WHERE vs HAVING

| Feature | WHERE | HAVING |
|---|---|---|
| Purpose | Filters rows | Filters groups |
| Applied | BEFORE GROUP BY | AFTER GROUP BY |
| Aggregate functions | Cannot use | Can use |

```sql
-- WHERE: filter individual rows
SELECT * FROM students WHERE marks > 50;

-- HAVING: filter groups after aggregation
SELECT class, AVG(marks) AS avg_marks
FROM students
GROUP BY class
HAVING AVG(marks) > 70;
```

---

## 4. Aggregate Functions

| Function | Description |
|---|---|
| `COUNT()` | Counts number of rows |
| `SUM()` | Sum of values |
| `AVG()` | Average of values |
| `MAX()` | Maximum value |
| `MIN()` | Minimum value |

```sql
SELECT
    COUNT(*)        AS total_students,
    SUM(marks)      AS total_marks,
    AVG(marks)      AS average_marks,
    MAX(marks)      AS highest_marks,
    MIN(marks)      AS lowest_marks
FROM students;
```

---

### Q6: Second Highest Salary / Marks (Very Common!)

```sql
-- Method 1: Subquery
SELECT MAX(salary) AS second_highest
FROM employees
WHERE salary < (SELECT MAX(salary) FROM employees);

-- Method 2: LIMIT + OFFSET (MySQL)
SELECT DISTINCT salary
FROM employees
ORDER BY salary DESC
LIMIT 1 OFFSET 1;

-- Method 3: Using NOT IN
SELECT MAX(salary)
FROM employees
WHERE salary NOT IN (SELECT MAX(salary) FROM employees);
```

---

### Q7: Nth Highest Salary (Generalized)

```sql
-- Replace N with desired rank (e.g., N=3 for 3rd highest)
SELECT salary
FROM employees e1
WHERE (N - 1) = (
    SELECT COUNT(DISTINCT salary)
    FROM employees e2
    WHERE e2.salary > e1.salary
);
```

---

## 5. Subqueries & Set Operations

### Types of Subqueries

| Type | Description |
|---|---|
| Scalar | Returns single value |
| Row | Returns single row |
| Column | Returns single column |
| Correlated | Refers to outer query |

```sql
-- Scalar subquery: students with maximum marks
SELECT name, marks FROM students
WHERE marks = (SELECT MAX(marks) FROM students);

-- Correlated subquery: students above their class average
SELECT name, marks, class
FROM students s1
WHERE marks > (
    SELECT AVG(marks)
    FROM students s2
    WHERE s2.class = s1.class
);

-- Subquery with IN
SELECT name FROM students
WHERE class_id IN (
    SELECT class_id FROM classes WHERE teacher = 'Mr. Sharma'
);
```

---

### Set Operations

| Operation | Description |
|---|---|
| `UNION` | Combines results, **removes duplicates** |
| `UNION ALL` | Combines results, **keeps duplicates** |
| `INTERSECT` | Returns **common** rows only |
| `EXCEPT` / `MINUS` | Rows in first query **not in** second |

```sql
-- UNION
SELECT name FROM teachers
UNION
SELECT name FROM students;

-- INTERSECT: names appearing in both tables
SELECT name FROM teachers
INTERSECT
SELECT name FROM students;

-- EXCEPT: teachers who are not students
SELECT name FROM teachers
EXCEPT
SELECT name FROM students;
```

---

## 6. String & Date Functions

### String Functions

```sql
SELECT UPPER('hello');                        -- HELLO
SELECT LOWER('WORLD');                        -- world
SELECT LENGTH('sql');                         -- 3
SELECT CHAR_LENGTH('interview');              -- 9
SELECT SUBSTRING('interview', 1, 5);          -- inter
SELECT LEFT('navodaya', 3);                   -- nav
SELECT RIGHT('navodaya', 4);                  -- daya
SELECT CONCAT('KV', ' ', 'School');           -- KV School
SELECT TRIM('  hello  ');                     -- hello
SELECT LTRIM('   left');                      -- left
SELECT RTRIM('right   ');                     -- right
SELECT REPLACE('hello world', 'world', 'SQL'); -- hello SQL
SELECT INSTR('interview', 'view');             -- 6
SELECT LPAD('5', 3, '0');                     -- 005
SELECT RPAD('KV', 5, '-');                    -- KV---
SELECT REVERSE('SQL');                        -- LQS
```

### Date & Time Functions

```sql
SELECT NOW();                                  -- current date and time
SELECT CURDATE();                              -- current date only
SELECT CURTIME();                              -- current time only
SELECT YEAR('2026-06-09');                     -- 2026
SELECT MONTH('2026-06-09');                    -- 6
SELECT DAY('2026-06-09');                      -- 9
SELECT DAYNAME('2026-06-09');                  -- Tuesday
SELECT MONTHNAME('2026-06-09');               -- June
SELECT DATEDIFF('2026-12-31', '2026-01-01');   -- 364
SELECT DATE_ADD('2026-06-09', INTERVAL 30 DAY); -- 2026-07-09
SELECT DATE_FORMAT(NOW(), '%d-%m-%Y');         -- 09-06-2026
```

---

## 7. Normalization (Theory)

### What is Normalization?
The process of organizing data in a database to:
- Reduce **data redundancy**
- Improve **data integrity**
- Eliminate **anomalies** (insertion, update, deletion)

### Normal Forms

| Normal Form | Condition |
|---|---|
| **1NF** | Atomic values (no repeating groups, no multi-valued attributes) |
| **2NF** | 1NF + No **partial dependency** (non-key attribute depends on whole PK) |
| **3NF** | 2NF + No **transitive dependency** (non-key attribute depends on another non-key) |
| **BCNF** | 3NF + Every **determinant** is a candidate key |
| **4NF** | BCNF + No multi-valued dependencies |
| **5NF** | 4NF + No join dependencies |

### Example

**Unnormalized Table:**

| StudentID | StudentName | Course1 | Course2 | TeacherName | TeacherPhone |
|---|---|---|---|---|---|
| 1 | Ramesh | Math | Science | Mr. Sharma | 9876543210 |

**Issues:**
- Repeating groups (Course1, Course2) — violates 1NF
- Teacher info repeated for every student — redundancy
- Transitive dependency: TeacherPhone depends on TeacherName, not StudentID

**After normalization into 3NF:**

```
Students(StudentID PK, StudentName)
Courses(CourseID PK, CourseName)
Teachers(TeacherID PK, TeacherName, TeacherPhone)
Enrollments(StudentID FK, CourseID FK, TeacherID FK)
```

---

## 8. Views, Indexes & Stored Procedures

### Views

A **view** is a virtual table based on a stored SQL query. It does not store data itself.

```sql
-- Create a view
CREATE VIEW topper_view AS
SELECT name, marks, class
FROM students
WHERE marks > 90;

-- Query the view like a table
SELECT * FROM topper_view;

-- Drop a view
DROP VIEW topper_view;
```

**Advantages of Views:**
- Simplifies complex queries
- Provides security (hide sensitive columns)
- Logical data independence

---

### Indexes

An **index** speeds up data retrieval at the cost of additional storage and slower writes.

```sql
-- Create an index
CREATE INDEX idx_student_name ON students(name);

-- Create a unique index
CREATE UNIQUE INDEX idx_email ON students(email);

-- Drop an index
DROP INDEX idx_student_name ON students;
```

| Feature | With Index | Without Index |
|---|---|---|
| SELECT speed | Fast | Slow (full table scan) |
| INSERT/UPDATE speed | Slower | Faster |
| Storage | More | Less |

---

### Stored Procedure vs Function

| Feature | Stored Procedure | Function |
|---|---|---|
| Return value | Optional (0 or more) | Must return one value |
| Use in SELECT | Cannot be used | Can be used |
| Parameters | IN, OUT, INOUT | Only IN |
| Exception handling | Supported | Limited |
| Purpose | Perform actions | Compute and return value |

```sql
-- Stored Procedure
DELIMITER //
CREATE PROCEDURE GetStudentsByClass(IN className VARCHAR(10))
BEGIN
    SELECT name, marks FROM students WHERE class = className;
END //
DELIMITER ;

-- Call procedure
CALL GetStudentsByClass('10A');

-- Function
DELIMITER //
CREATE FUNCTION GetGrade(marks INT) RETURNS CHAR(1)
DETERMINISTIC
BEGIN
    DECLARE grade CHAR(1);
    IF marks >= 90 THEN SET grade = 'A';
    ELSEIF marks >= 75 THEN SET grade = 'B';
    ELSEIF marks >= 60 THEN SET grade = 'C';
    ELSE SET grade = 'F';
    END IF;
    RETURN grade;
END //
DELIMITER ;

-- Use function in SELECT
SELECT name, marks, GetGrade(marks) AS grade FROM students;
```

---

## 9. Transaction Control (TCL) & ACID

### ACID Properties

| Property | Description |
|---|---|
| **Atomicity** | All operations succeed or all are rolled back (all-or-nothing) |
| **Consistency** | Database remains in a valid state before and after transaction |
| **Isolation** | Concurrent transactions do not interfere with each other |
| **Durability** | Committed data persists even after system failure |

---

### TCL Commands

```sql
-- Start a transaction
START TRANSACTION;

-- Example: Transfer money between accounts
UPDATE accounts SET balance = balance - 5000 WHERE account_id = 101;
UPDATE accounts SET balance = balance + 5000 WHERE account_id = 202;

-- If everything is fine, commit
COMMIT;

-- If something goes wrong, rollback
ROLLBACK;

-- Using savepoints for partial rollback
START TRANSACTION;
INSERT INTO orders VALUES (1, 'Item A', 500);
SAVEPOINT sp1;
INSERT INTO orders VALUES (2, 'Item B', 300);
ROLLBACK TO sp1;   -- undoes Item B only
COMMIT;            -- commits Item A
```

---

## 10. Practical Query Writing

> Practice writing these on paper — KV/NV interviews often include board writing.

### Q: Find students who scored above the class average

```sql
SELECT name, marks, class
FROM students
WHERE marks > (SELECT AVG(marks) FROM students);
```

### Q: Find duplicate records

```sql
SELECT name, COUNT(*) AS count
FROM students
GROUP BY name
HAVING COUNT(*) > 1;
```

### Q: Delete duplicates, keep one record

```sql
DELETE FROM students
WHERE id NOT IN (
    SELECT MIN(id) FROM students GROUP BY name
);
```

### Q: Top 3 students by marks

```sql
SELECT name, marks
FROM students
ORDER BY marks DESC
LIMIT 3;
```

### Q: Students who have not appeared in any exam

```sql
SELECT s.name
FROM students s
LEFT JOIN marks m ON s.id = m.student_id
WHERE m.student_id IS NULL;
```

### Q: Count students in each class

```sql
SELECT class, COUNT(*) AS student_count
FROM students
GROUP BY class
ORDER BY class;
```

### Q: Class-wise highest marks

```sql
SELECT class, MAX(marks) AS highest_marks
FROM students
GROUP BY class;
```

### Q: Rank students using window function

```sql
SELECT name, marks,
       RANK() OVER (ORDER BY marks DESC) AS rank,
       DENSE_RANK() OVER (ORDER BY marks DESC) AS dense_rank,
       ROW_NUMBER() OVER (ORDER BY marks DESC) AS row_num
FROM students;
```

### Q: Find students who scored in all subjects

```sql
SELECT student_id, COUNT(DISTINCT subject) AS subject_count
FROM marks
GROUP BY student_id
HAVING COUNT(DISTINCT subject) = (SELECT COUNT(DISTINCT subject) FROM subjects);
```

### Q: Update marks: add 5 bonus marks to students with marks < 40

```sql
UPDATE students
SET marks = marks + 5
WHERE marks < 40;
```

### Q: Create a table with auto-increment primary key

```sql
CREATE TABLE teachers (
    teacher_id INT AUTO_INCREMENT PRIMARY KEY,
    name       VARCHAR(100) NOT NULL,
    subject    VARCHAR(50),
    joining_date DATE DEFAULT (CURDATE())
);
```

---

## 11. Key Differences — Quick Reference

| Comparison | Option A | Option B |
|---|---|---|
| `DELETE` vs `TRUNCATE` | Row-by-row, WHERE clause allowed, rollback possible, triggers fire | All rows at once, no WHERE, no rollback, resets auto-increment |
| `DROP` vs `TRUNCATE` | Removes table structure + all data | Keeps structure, removes all data |
| `CHAR` vs `VARCHAR` | Fixed length, pads with spaces, faster | Variable length, no padding, saves space |
| `IN` vs `EXISTS` | Checks value in a list/subquery result | Checks if subquery returns any row (more efficient for large sets) |
| `UNION` vs `UNION ALL` | Removes duplicates | Keeps all duplicates |
| `WHERE` vs `HAVING` | Before GROUP BY, no aggregates | After GROUP BY, can use aggregates |
| `Clustered` vs `Non-clustered Index` | Sorts actual table data, one per table | Separate structure with pointer, multiple per table |
| `RANK()` vs `DENSE_RANK()` | Skips rank after tie (1,1,3) | No skipping (1,1,2) |
| `TRUNCATE` vs `DELETE` (speed) | Faster (minimal logging) | Slower (logs each row) |

---

## 12. Relational Algebra Basics

> Relevant for **PGT Computer Science** theory papers.

| Operation | Symbol | SQL Equivalent | Description |
|---|---|---|---|
| Selection | σ (sigma) | WHERE | Filters rows based on condition |
| Projection | π (pi) | SELECT (columns) | Selects specific columns |
| Union | ∪ | UNION | Combines two relations |
| Intersection | ∩ | INTERSECT | Common tuples |
| Difference | − | EXCEPT/MINUS | Tuples in first, not in second |
| Cartesian Product | × | CROSS JOIN | All combinations |
| Join | ⋈ | JOIN | Natural join of two relations |
| Rename | ρ (rho) | AS (alias) | Renames relation/attributes |

**Example:**

```
σ marks > 80 (students)         → SELECT * FROM students WHERE marks > 80;
π name, marks (students)        → SELECT name, marks FROM students;
π name (σ class='10A' (students)) → SELECT name FROM students WHERE class='10A';
```

---

## 13. Interview Tips

### What to Expect in KV/NV Interviews

1. **Board/Paper Query Writing** — Practice writing SQL without an IDE. Focus on syntax accuracy.
2. **Theory Questions** — Normalization, ACID, ER diagrams, types of keys.
3. **CBSE Syllabus Focus** — Class 11 & 12 MySQL topics are most commonly tested.
4. **Practical Scenario Questions** — Given a table structure, write queries for specific outputs.
5. **Difference-based Questions** — Always explain with examples, not just definitions.

---

### Most Frequently Asked Topics

- [ ] Types of JOINs with examples
- [ ] Primary Key vs Foreign Key vs Unique Key
- [ ] WHERE vs HAVING
- [ ] Normalization (1NF, 2NF, 3NF definitions + examples)
- [ ] Aggregate functions (COUNT, SUM, AVG, MAX, MIN)
- [ ] Second/Nth highest salary query
- [ ] DELETE vs TRUNCATE vs DROP
- [ ] DDL vs DML
- [ ] What is a View and why use it?
- [ ] ACID properties
- [ ] Subquery types

---

### Sample Table for Practice

```sql
-- Use this table to practice all queries
CREATE TABLE students (
    student_id   INT AUTO_INCREMENT PRIMARY KEY,
    name         VARCHAR(50) NOT NULL,
    class        VARCHAR(10),
    marks        INT CHECK (marks BETWEEN 0 AND 100),
    subject      VARCHAR(30),
    gender       CHAR(1) CHECK (gender IN ('M', 'F')),
    dob          DATE,
    city         VARCHAR(30) DEFAULT 'Delhi'
);

INSERT INTO students (name, class, marks, subject, gender, dob, city) VALUES
('Ramesh Kumar',  '10A', 88, 'Math',    'M', '2010-03-15', 'Delhi'),
('Sunita Devi',   '10A', 92, 'Science', 'F', '2010-07-22', 'Jaipur'),
('Aakash Singh',  '10B', 74, 'Math',    'M', '2010-01-10', 'Lucknow'),
('Priya Sharma',  '10B', 95, 'English', 'F', '2010-11-05', 'Delhi'),
('Mohan Lal',     '10A', 61, 'Math',    'M', '2010-09-18', 'Agra'),
('Kavita Yadav',  '10B', 55, 'Science', 'F', '2010-04-30', 'Kanpur'),
('Ravi Verma',    '10A', 88, 'English', 'M', '2010-06-25', 'Delhi'),
('Anita Kumari',  '10C', 78, 'Math',    'F', '2010-08-14', 'Patna');
```

---

### Quick Revision Checklist

| Topic | Understood | Practice Done |
|---|---|---|
| DDL / DML / DCL / TCL commands | ☐ | ☐ |
| Constraints (all 6 types) | ☐ | ☐ |
| All JOIN types with examples | ☐ | ☐ |
| Aggregate functions | ☐ | ☐ |
| GROUP BY + HAVING | ☐ | ☐ |
| Subqueries (scalar, correlated) | ☐ | ☐ |
| String functions | ☐ | ☐ |
| Date functions | ☐ | ☐ |
| Normalization (1NF to BCNF) | ☐ | ☐ |
| Views | ☐ | ☐ |
| Indexes | ☐ | ☐ |
| ACID properties | ☐ | ☐ |
| TCL (COMMIT, ROLLBACK, SAVEPOINT) | ☐ | ☐ |
| 2nd / Nth highest value query | ☐ | ☐ |
| DELETE vs TRUNCATE vs DROP | ☐ | ☐ |
| Relational Algebra basics | ☐ | ☐ |

---

*Good luck with your KV/NV interview preparation!*
