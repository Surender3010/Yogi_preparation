# KVS & NVS Interview SQL Queries Compilation
## Actual Interview Queries, Scenarios, and Explanations

This guide compiles the SQL queries most commonly asked in **Kendriya Vidyalaya (KVS)** and **Navodaya Vidyalaya (NVS)** PGT/TGT Computer Science interviews. It includes the sample schema, the target question, the SQL query, and a pedagogical explanation of how to walk the interviewers through your code.

---

## 1. Practice Schema Setup
Interviewers will often give you a basic schema on the board or ask you to assume one. We will use the following two tables for all sample queries:

### Table: `STUDENT`
| Column Name | Data Type | Constraint |
|---|---|---|
| `RollNo` | INT | PRIMARY KEY, AUTO_INCREMENT |
| `Name` | VARCHAR(50) | NOT NULL |
| `Class` | VARCHAR(5) | NOT NULL |
| `Gender` | CHAR(1) | CHECK (`Gender` IN ('M', 'F')) |
| `City` | VARCHAR(30) | DEFAULT 'Delhi' |
| `Stream` | VARCHAR(20) | e.g., 'Science', 'Commerce', 'Humanities' |

### Table: `MARKS`
| Column Name | Data Type | Constraint |
|---|---|---|
| `RollNo` | INT | FOREIGN KEY REFERENCES `STUDENT(RollNo)` |
| `Subject` | VARCHAR(30) | NOT NULL |
| `Score` | INT | CHECK (`Score` BETWEEN 0 AND 100) |

---

## 2. String Manipulation & Pattern Matching

### Q1: Find all students whose name starts with 'S'.
* **SQL Query:**
  ```sql
  SELECT * FROM STUDENT 
  WHERE Name LIKE 'S%';
  ```
* **Explanation for Interviewers:** Use the `LIKE` operator for pattern matching. The `%` wildcard matches zero or more characters. Mention that it is case-insensitive in standard MySQL but case-sensitive in some other SQL dialects.

### Q2: Find all students whose name is exactly 5 characters long and ends with 'a'.
* **SQL Query:**
  ```sql
  SELECT * FROM STUDENT 
  WHERE Name LIKE '____a';  -- Exactly four underscores followed by 'a'
  ```
* **Explanation for Interviewers:** Explain that the underscore `_` matching operator represents exactly one character, while `%` represents any number of characters.

### Q3: Find all students whose name has 'a' as the second character.
* **SQL Query:**
  ```sql
  SELECT * FROM STUDENT 
  WHERE Name LIKE '_a%';
  ```
* **Explanation for Interviewers:** The first character is matching any single character via `_`, the second character must be `a`, and any trailing characters are matched using `%`.

---

## 3. Aggregations, Grouping & Filtering

### Q4: Count the number of male and female students in each class.
* **SQL Query:**
  ```sql
  SELECT Class, Gender, COUNT(*) AS Total
  FROM STUDENT
  GROUP BY Class, Gender
  ORDER BY Class;
  ```
* **Explanation for Interviewers:** This is a composite grouping. Explain that `GROUP BY Class, Gender` first groups the rows by `Class`, and then within each class, groups them by `Gender`.

### Q5: Display the classes having more than 5 students.
* **SQL Query:**
  ```sql
  SELECT Class, COUNT(*) AS TotalStudents
  FROM STUDENT
  GROUP BY Class
  HAVING COUNT(*) > 5;
  ```
* **Key Interview Point:** Emphasize the difference between `WHERE` and `HAVING`. Explain that `WHERE` filters rows before grouping, and `HAVING` filters group statistics after aggregation. 

### Q6: Find the average score of each subject, but only include subjects where the average score is greater than 60.
* **SQL Query:**
  ```sql
  SELECT Subject, AVG(Score) AS AverageScore
  FROM MARKS
  GROUP BY Subject
  HAVING AVG(Score) > 60;
  ```

---

## 4. Subqueries & Nested Queries

### Q7: Find students who scored maximum marks in 'Math'.
* **SQL Query:**
  ```sql
  SELECT RollNo, Score 
  FROM MARKS 
  WHERE Subject = 'Math' 
    AND Score = (SELECT MAX(Score) FROM MARKS WHERE Subject = 'Math');
  ```
* **Explanation for Interviewers:** Point out that we cannot write `WHERE Score = MAX(Score)` directly because aggregate functions are not allowed in the `WHERE` clause. Therefore, we use a scalar subquery to find the maximum marks first.

### Q8: Find students who scored more than the overall average marks across all subjects.
* **SQL Query:**
  ```sql
  SELECT s.RollNo, s.Name, m.Subject, m.Score
  FROM STUDENT s
  INNER JOIN MARKS m ON s.RollNo = m.RollNo
  WHERE m.Score > (SELECT AVG(Score) FROM MARKS);
  ```

### Q9: Find the names of students who did not appear in any exam.
* **SQL Query (Using SUBQUERY):**
  ```sql
  SELECT Name 
  FROM STUDENT 
  WHERE RollNo NOT IN (SELECT DISTINCT RollNo FROM MARKS);
  ```
* **SQL Query (Using LEFT JOIN):**
  ```sql
  SELECT s.Name 
  FROM STUDENT s
  LEFT JOIN MARKS m ON s.RollNo = m.RollNo
  WHERE m.RollNo IS NULL;
  ```
* **Key Interview Point:** Discuss why the `LEFT JOIN` approach is often more performant than `NOT IN` because of how database engines optimize anti-joins.

---

## 5. Working with NULL Values

### Q10: Find students who have not been assigned to a Stream.
* **Incorrect Query (Write this and show why it is wrong if asked):**
  ```sql
  SELECT * FROM STUDENT WHERE Stream = NULL; -- Returns 0 rows
  ```
* **Correct SQL Query:**
  ```sql
  SELECT * FROM STUDENT WHERE Stream IS NULL;
  ```
* **Explanation for Interviewers:** Explain that `NULL` is not a constant value or zero; it represents 'unknown' or 'missing' data. Standard comparison operators (`=`, `!=`, `<>`) yield `UNKNOWN` when compared with `NULL`. Hence, special operators standard is `IS NULL` or `IS NOT NULL`.

---

## 6. Table Relationships & JOINs

### Q11: Display the Name, Class, Subject, and Marks of all students.
* **SQL Query:**
  ```sql
  SELECT s.Name, s.Class, m.Subject, m.Score
  FROM STUDENT s
  INNER JOIN MARKS m ON s.RollNo = m.RollNo;
  ```
* **Explanation for Interviewers:** An `INNER JOIN` matches rows that have common `RollNo` values in both tables. Students who have not given exams, or exam marks that don't match any student (e.g., orphan records), will be excluded.

### Q12: Display all students' names and their marks, including those students who didn't appear for any exams.
* **SQL Query:**
  ```sql
  SELECT s.Name, m.Subject, IFNULL(m.Score, 0) AS Score
  FROM STUDENT s
  LEFT JOIN MARKS m ON s.RollNo = m.RollNo;
  ```
* **Explanation for Interviewers:** A `LEFT JOIN` returns all records from the left table (`STUDENT`), even if there are no corresponding matches in the right table (`MARKS`). If there are no marks, `m.Subject` and `m.Score` will return `NULL`. We can use `IFNULL` (or `COALESCE` in standard SQL) to represent `NULL` as `0`.

---

## 7. Advanced / Critical Queries

### Q13: Find the 2nd Highest Score in the school.
* **SQL Query (Standard Subquery — SQL dialect safe):**
  ```sql
  SELECT MAX(Score) AS SecondHighest 
  FROM MARKS
  WHERE Score < (SELECT MAX(Score) FROM MARKS);
  ```
* **SQL Query (Using MySql position manipulation):**
  ```sql
  SELECT DISTINCT Score 
  FROM MARKS 
  ORDER BY Score DESC 
  LIMIT 1 OFFSET 1;
  ```

### Q14: Find the N-th Highest Score (General Query).
* **SQL Query:**
  ```sql
  SELECT DISTINCT Score 
  FROM MARKS m1
  WHERE N-1 = (
      SELECT COUNT(DISTINCT Score) 
      FROM MARKS m2 
      WHERE m2.Score > m1.Score
  );
  ```
* **Explanation for Interviewers:** This is a **Correlated Subquery**. For every row evaluated by the outer query, the inner query counts how many distinct scores are higher than it. When the count of higher scores equals `N-1`, we have found the Nth highest score.

### Q15: Delete duplicate records based on Student Name but keep the one with the lowest RollNo.
* **SQL Query:**
  ```sql
  DELETE FROM STUDENT 
  WHERE RollNo NOT IN (
      SELECT * FROM (
          SELECT MIN(RollNo) 
          FROM STUDENT 
          GROUP BY Name
      ) AS Temp
  );
  ```
* **Key Interview Point:** In MySQL, you cannot directly delete from a table you're selecting from in a subquery. Creating a nested subquery alias (`AS Temp`) creates a temporary table that allows the execution to update successfully.

---

## 8. Ranking & Analytical Window Functions
> *(Increasingly asked in both TGT and PGT CS interviews to check depth of knowledge)*

### Q16: Rank students based on their total marks.
* **SQL Query:**
  ```sql
  SELECT RollNo, 
         SUM(Score) AS TotalMarks,
         RANK() OVER (ORDER BY SUM(Score) DESC) AS StudentRank,
         DENSE_RANK() OVER (ORDER BY SUM(Score) DESC) AS StudentDenseRank
  FROM MARKS
  GROUP BY RollNo;
  ```
* **Explanation showing Difference between `RANK()` and `DENSE_RANK()`:**
  If two students are tied for rank 1:
  * `RANK()` will outputs ranks as: `1, 1, 3, 4` (it skips rank 2 because of duplicate ties).
  * `DENSE_RANK()` will outputs ranks as: `1, 1, 2, 3` (no ranks are skipped).

---

## 9. Data Definition (DDL) Queries

### Q17: Create a table 'TEACHER' where 'TeacherID' is primary key, and 'Salary' cannot be negative.
* **SQL Query:**
  ```sql
  CREATE TABLE TEACHER (
      TeacherID INT AUTO_INCREMENT,
      Name VARCHAR(60) NOT NULL,
      Subject VARCHAR(35),
      Salary DECIMAL(10,2) CHECK (Salary >= 0),
      Email VARCHAR(100) UNIQUE,
      PRIMARY KEY (TeacherID)
  );
  ```

### Q18: Add a new column 'PhoneNo' of type VARCHAR(15) to an existing table.
* **SQL Query:**
  ```sql
  ALTER TABLE STUDENT 
  ADD PhoneNo VARCHAR(15);
  ```

### Q19: Change the data type of column 'Stream' in table 'STUDENT' from VARCHAR(20) to VARCHAR(35).
* **SQL Query:**
  ```sql
  ALTER TABLE STUDENT 
  MODIFY Stream VARCHAR(35);
  ```

---

*Keep this reference sheet handy when reviewing! You can copy-paste the DDL lines to practice these queries on your local terminal or online editors.*
MySQL command line client before the interview.*
