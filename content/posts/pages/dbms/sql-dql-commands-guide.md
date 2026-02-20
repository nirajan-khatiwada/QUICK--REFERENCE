---
title: "SQL DQL Commands: Select, Joins & Subqueries"
slug: "sql-dql-commands-guide"
date: 2025-01-25
description: "Master SQL Data Query Language (DQL). Deep dive into SELECT, WHERE, JOINS, Aggregate Functions, and nested Subqueries."
showToc: true
weight: 4
series: ["DBMS"]
categories: ["DBMS", "SQL"]
tags: ["DQL", "SELECT", "JOINS", "Aggregation", "Subqueries", "SQL"]
summary: "The ultimate guide to querying databases. Covers simple SELECTs, complex JOINS, Grouping, Aggregation, and advanced Subquery techniques."
images: ["/images/dbms2.jpg"]
---
## Data Query Language (DQL)

### Basic SELECT Statements
```sql
SELECT * FROM TABLENAME
SELECT COLUMN1, COLUMN2, COLUMN3 FROM TABLENAME
SELECT COLUMN1, COLUMN2, COLUMN3 FROM TABLENAME WHERE CONDITION

-- Applying operations on columns during SELECT
SELECT COLUMN1, COLUMN2*5, COLUMN3+10 FROM TABLENAME
SELECT STUDENT_NAME, MARKS*1.1 FROM STUDENT  -- Increase marks by 10%
SELECT EMPLOYEE_NAME, SALARY/12 FROM EMPLOYEE  -- Monthly salary
SELECT PRODUCT_NAME, PRICE-DISCOUNT FROM PRODUCTS
SELECT LENGTH, WIDTH, LENGTH*WIDTH FROM RECTANGLE  -- Calculate area
```

**Operations in SELECT:**
- Arithmetic operations: `+`, `-`, `*`, `/`
- Can combine multiple columns: `COLUMN1 + COLUMN2`
- Can use constants: `MARKS * 1.1`, `PRICE - 100`
- Results appear as calculated values in output
- Original data in table remains unchanged

### DISTINCT Clause
```sql
SELECT DISTINCT COLUMN1, COLUMN2... FROM TABLENAME WHERE CONDITION
SELECT DISTINCT COLUMN1, COLUMN2... FROM TABLENAME 
```

**Important DISTINCT Rules:**
- DISTINCT can only be placed first and only once
-  Correct: `SELECT DISTINCT COLUMN1, COLUMN2... FROM TABLENAME`
-  Wrong: `SELECT COLUMN1, DISTINCT COLUMN2, COLUMN3... FROM TABLENAME`
-  Wrong: `SELECT DISTINCT COLUMN1, DISTINCT COLUMN2... FROM TABLENAME`

**How DISTINCT Works:**
Returns unique combinations of all selected columns.

```sql
SELECT DISTINCT COLUMN1, COLUMN2 FROM TABLENAME;
```

Example:
```
Original Data:
COLUMN1  COLUMN2  COLUMN3
1        1        1
1        2        3
1        1        3    (duplicate)
1        3        2

After DISTINCT:
COLUMN1  COLUMN2  COLUMN3
1        1        1
1        2        3
1        3        2
```

```sql
SELECT DISTINCT COLUMN1 FROM TABLE_NAME;
```

Example:
```
Original Data:
COLUMN1 COLUMN2 Column3
1       1       1   
1       2       3    (duplicate)
1       1       3    (duplicate)
1       3       2    (duplicate)

After DISTINCT:
COLUMN1
1
```


## WHERE Clause Operators

| Operator | Usage |
|----------|-------|
| = | Equal to |
| != | Not equal to |
| < | Less than |
| > | Greater than |
| <= | Less than equal to |
| >= | Greater than equal to |
| !< | Not less than |
| !> | Not greater than |
| AND | Logical AND |
| OR | Logical OR |
| BETWEEN | Between two values |
| NOT BETWEEN | Not between two values |
| IN | Multiple OR conditions |
| NOT IN | Not in specified values |
| IS NULL | Is a null value |
| IS NOT NULL | Is not null |
| LIKE | Pattern matching |

### WHERE Examples
```sql
SELECT * FROM TABLENAME WHERE COLUMN1=VALUE AND COLUMN2>30
SELECT * FROM TABLENAME WHERE COLUMN1 IS NULL
SELECT * FROM TABLENAME WHERE AGE BETWEEN 19 AND 23
SELECT * FROM TABLENAME WHERE AGE IN (1,2,3,19,20)
SELECT * FROM TABLENAME WHERE AGE BETWEEN 19 AND 23 AND COLUMN2 IS NULL
```

## LIKE Pattern Matching

**Syntax:** `SELECT COLUMN1, COLUMN2... WHERE COLUMN_NAME LIKE PATTERN`

### Wildcards
- `%` - Matches any string of zero or more characters
- `_` - Matches any one character

### Pattern Examples
| Pattern | Description | Examples |
|---------|-------------|----------|
| `A%` | Starts with A | A, AA, ARBI, ARBIND |
| `%A` | Ends with A | A, NIRA |
| `%AA%` | Contains AA | NIRAA, AA, MUAAL |
| `____` | Exactly 4 characters | NIRA, BIRA, JIRA, KIRA |
| `QUA__` | 5 chars starting with QUA | QUALA, QUAHD |
| `_RE%` | 2nd char R, 3rd char E | TREE, AREA, FRETFUL |
| `%RE_` | RE as 2nd and 3rd last | |
| `2___3` | 5 chars, starts with 2, ends with 3 | |
| `2%3` | Starts with 2, ends with 3 | |
| `2_%_%` | Starts with 2, at least 3 chars | |

## ORDER BY

```sql
SELECT COLUMNNAME,... FROM TABLENAME WHERE CONDITION ORDER BY COLUMNNAME [ASC/DESC]
```

### Examples
```sql
-- Sort by name in ascending order
SELECT * FROM STUDENTS WHERE AGE > 30 ORDER BY NAME ASC;

-- Sort by name first, then by age if names are same
SELECT * FROM STUDENTS WHERE AGE > 30 ORDER BY NAME, AGE ASC;
```

## Aggregate Functions

| Function | Description |
|----------|-------------|
| `AVG(COLUMN)` | Returns average of column |
| `MIN(COLUMN)` | Returns minimum value |
| `MAX(COLUMN)` | Returns maximum value |
| `SUM(COLUMN)` | Returns total sum |
| `COUNT(COLUMN)` | Returns number of rows without NULL |
| `COUNT(*)` | Returns total number of records (with and without NULL) |

### Example
```sql
-- For pass students
SELECT MIN(STUDENT_MARKS), MAX(STUDENT_MARKS), AVG(STUDENT_MARKS), SUM(STUDENT_MARKS) 
FROM STUDENT 
WHERE STUDENT_MARKS > 35;

SELECT COUNT(STUDENT_MARKS), COUNT(*) FROM STUDENT;
```

## GROUP BY

```sql
SELECT COLUMN FROM TABLENAME GROUP BY COLUMNNAME;
```

### How GROUP BY Works

Example:
```sql
SELECT DEPARTMENT, AVG(SALARY) FROM EMPLOYEE GROUP BY DEPARTMENT;
```

Example :
consider a table with employee data:
```
| EMPLOYEE_ID | NAME   | DEPARTMENT | SALARY |
|-------------|--------|------------|--------|
| 1           | Alice  | HR         | 5000   |
| 2           | Bob    | IT         | 6000   |
| 3           | Charlie| HR        | 5500   |
| 4           | David  | IT         | 7000   |
```
```sql
SELECT DEPARTMENT, AVG(SALARY) FROM EMPLOYEE GROUP BY DEPARTMENT;
```

Then it first groups the rows by department:
```
HR -> Alice(5000), Charlie(5500) -> Average = (5000 + 5500) / 2 = 5250
IT -> Bob(6000), David(7000) -> Average = (6000 + 7000) / 2 = 6500
```

final table:
```
| DEPARTMENT | AVG(SALARY) |
|------------|-------------|
| HR         | 5250        |
| IT         | 6500        |
```


### Multiple Column GROUP BY
```sql
SELECT Country, City, COUNT(*)
FROM Customers 
GROUP BY Country, City;
```

Example:
Consider a table with customer data:
```
| CustomerID | Country | City      | name   |
|------------|---------|-----------|--------|
| 1          | USA     | New York  | Alice  |
| 2          | USA     | Los Angeles| Bob    |
| 3          | Canada  | Toronto   | Charlie|
| 4          | Canada  | Vancouver | David  |
| 5          | USA     | New York  | Eve    |
```

Then it groups by both Country and City:
```
USA,New York -> Alice, Eve -> Count = 2
USA,Los Angeles -> Bob -> Count = 1
Canada,Toronto -> Charlie -> Count = 1
Canada,Vancouver -> David -> Count = 1
```


final table:
```
| Country | City        | COUNT(*) |
|---------|-------------|----------|
| USA     | New York    | 2        |
| USA     | Los Angeles | 1        |
| Canada  | Toronto     | 1        |
| Canada  | Vancouver   | 1        |
```





### GROUP BY with WHERE
```sql
SELECT DEPARTMENT, AVG(SALARY)
FROM EMPLOYEE
WHERE SALARY > 1000
GROUP BY DEPARTMENT;
```

First filters records where salary > 1000, then groups and calculates average.


## HAVING Clause

Used to filter groups created by GROUP BY.

```sql
-- Find students whose average marks > 30
SELECT STUDENT_NAME, AVG(MARKS) 
FROM STUDENT 
GROUP BY STUDENT_NAME 
HAVING AVG(MARKS) > 30;

-- Get only student names
SELECT STUDENT_NAME 
FROM STUDENT 
GROUP BY STUDENT_NAME 
HAVING AVG(MARKS) > 30;
```

### WHERE vs HAVING
- **WHERE**: Filters individual rows before grouping
- **HAVING**: Filters groups after GROUP BY

```sql
SELECT STUDENT_NAME 
FROM STUDENT 
WHERE MARKS > 30 
GROUP BY STUDENT_NAME 
HAVING AVG(MARKS) > 50;
```

Process: Filter rows (MARKS > 30) → Group by name → Filter groups (AVG > 50)

## JOINS

### CROSS JOIN
```sql
SELECT COLUMN_NAME FROM TABLE1 CROSS JOIN TABLE2 CROSS JOIN TABLE3...
```
Returns Cartesian product of all tables.

### INNER JOIN
```sql
SELECT  TEACHER.STUDENT_ID, STUDENT.STUDENT_ID,TEACHER.ENROLLMENT_ID,
 STUDENT.ENROLLMENT_ID,teacher_name, student_name,teacher_subject, 
 student_ageenrollment_date 
FROM TEACHER 
INNER JOIN STUDENT 
ON TEACHER.STUDENT_ID = STUDENT.STUDENT_ID
INNER JOIN ENROLLMENT 
ON TEACHER.STUDENT_ID = ENROLLMENT.STUDENT_ID
```

**How INNER JOIN Works:**
1. First, it performs a Cartesian product of both tables (creates all possible combinations)
2. Then, it keeps only those rows that satisfy the JOIN condition
3. Discards all rows where the condition is false

More performance-optimized than CROSS JOIN with WHERE condition because it applies the condition during the join process rather than after creating the full Cartesian product.

### NATURAL JOIN
```sql
SELECT * FROM TEACHER NATURAL JOIN STUDENTS;
```

**Requirements:**
- Tables must have at least one attribute with the same name
- Automatically joins on columns with identical names

**How NATURAL JOIN Works:**
1. First, it identifies all columns with the same name in both tables
2. Performs a join where corresponding values of same-named attributes are equal
3. Keeps only rows where all common attribute values match
4. Finally, merges the common columns into one (removes duplicates)

**Key Difference from Other Joins:**
- **Other Joins**: If two tables have same attribute names, both columns appear separately in result
  - Need to specify using `TABLE.ATTRIBUTE` notation to distinguish
  - Example: `TEACHER.STUDENT_ID` and `STUDENT.STUDENT_ID` both appear
- **Natural Join**: Automatically merges/combines same-named columns into one
  - Only one column appears in result for each matching attribute name
  - No need for `TABLE.ATTRIBUTE` specification

### OUTER JOINS

#### LEFT OUTER JOIN
```sql
SELECT * FROM STUDENTS 
LEFT OUTER JOIN TEACHER 
ON STUDENTS.STUDENT_ID = TEACHER.STUDENT_ID;
```
Returns all rows from left table, matched rows from right table, NULL for unmatched.
Also it is union of INNER JOIN and a left table.

#### RIGHT OUTER JOIN
```sql
SELECT * FROM STUDENTS 
RIGHT OUTER JOIN TEACHER 
ON STUDENTS.STUDENT_ID = TEACHER.STUDENT_ID;
```
Returns all rows from right table, matched rows from left table, NULL for unmatched.
Also it is union of INNER JOIN and a right table.

#### FULL OUTER JOIN
```sql
-- Oracle syntax
SELECT * FROM STUDENT 
FULL OUTER JOIN TEACHER 
ON STUDENTS.STUDENT_ID = TEACHER.STUDENT_ID;

-- MySQL equivalent
SELECT * FROM STUDENT 
LEFT JOIN TEACHER ON STUDENT.STUDENT_ID = TEACHER.STUDENT_ID
UNION
SELECT * FROM STUDENT 
RIGHT JOIN TEACHER ON STUDENT.STUDENT_ID = TEACHER.STUDENT_ID;
```

## Set Operations

**Prerequisites:**
- Two relations must have same number of attributes
- All attributes must be of same domain/type

### UNION
```sql
SELECT TEACHER_NAME FROM TEACHER 
UNION 
SELECT STUDENT_NAME FROM STUDENTS;
```
Returns unique records from both queries.

### UNION ALL
```sql
SELECT TEACHER_NAME FROM TEACHER 
UNION ALL 
SELECT STUDENT_NAME FROM STUDENTS;
```
Returns all records including duplicates.

### INTERSECT
```sql
SELECT TEACHER_NAME FROM TEACHER 
INTERSECT 
SELECT STUDENT_NAME FROM STUDENTS;
```
Returns common records from both queries.

### MINUS/EXCEPT
```sql
-- SQL standard
SELECT TEACHER_NAME FROM TEACHER 
EXCEPT 
SELECT STUDENT_NAME FROM STUDENTS;

-- Oracle
SELECT TEACHER_NAME FROM TEACHER 
MINUS 
SELECT STUDENT_NAME FROM STUDENTS;
```
Returns records from first query that are not in second query.

## Nested Queries (Subqueries)

A query that is embedded within another SQL query is called a nested query or subquery.A sub query is used to return data that will be used in the main query as a condition to further restrict the data to be retrieved.
Properties of Subqueries:
- Subqueries must be enclosed in parentheses.
- A subquery can have only one column in the SELECT statement.


Syntax:
```sql
SELECT column_name(s)
FROM table_name
WHERE column_name operator
    (SELECT column_name FROM table_name WHERE condition);
```

Types of Subqueries:
1. **Single Row Subquery**: Returns a single row.
   ```sql
   SELECT * FROM STUDENTS 
   WHERE AGE = (SELECT MAX(AGE) FROM STUDENTS);
   ```

2. **Multiple Row Subquery**: Returns multiple rows.
  
   if using `IN`:
   ```sql
    SELECT * FROM STUDENTS 
    WHERE AGE IN (SELECT AGE FROM STUDENTS WHERE AGE > 20);
    ```

    if using `ANY`
    ```sql
    SELECT * FROM STUDENTS 
    WHERE AGE > ANY (SELECT AGE FROM STUDENTS WHERE AGE < 30);
    ```

3. if using `ALL`
   ```sql
   SELECT * FROM STUDENTS 
   WHERE AGE > ALL (SELECT AGE FROM STUDENTS WHERE AGE < 30);
   ```




## Aliases (AS Keyword)

### Column Aliases
```sql
-- Using AS keyword
SELECT COLUMN1 AS COL1, COLUMN2 AS COL2 FROM TABLENAME
SELECT STUDENT_NAME AS NAME, STUDENT_MARKS AS MARKS FROM STUDENT
SELECT AVG(SALARY) AS AVERAGE_SALARY FROM EMPLOYEE

-- Without AS keyword (optional)
SELECT COLUMN1 COL1, COLUMN2 COL2 FROM TABLENAME
SELECT COUNT(*) TOTAL_STUDENTS FROM STUDENT
```

### Table Aliases
```sql
-- Using AS keyword
SELECT S.STUDENT_NAME, T.TEACHER_NAME 
FROM STUDENT AS S, TEACHER AS T 
WHERE S.STUDENT_ID = T.STUDENT_ID

-- Without AS keyword (optional)
SELECT S.STUDENT_NAME, T.TEACHER_NAME 
FROM STUDENT S, TEACHER T 
WHERE S.STUDENT_ID = T.STUDENT_ID

-- Useful in JOINs
SELECT S.NAME, T.SUBJECT 
FROM STUDENT AS S 
INNER JOIN TEACHER AS T ON S.ID = T.STUDENT_ID
```

**Benefits of Aliases:**
- Makes column names more readable in output
- Shortens long table names for easier reference
- Essential when using aggregate functions
- Required when same column names exist in multiple tables


### Views
Views are virtual table that does not store data physically but represent the data which is already stored in another table of the database. 

Advantage:
- The Integrity constraints of parent table is same in view as in parent table.
- If data is inserted in view, it will be inserted in parent table and vice versa.

Create View Syntax:
```sql
CREATE VIEW view_name AS
SELECT column1, column2, ...
FROM table_name
WHERE condition;
```

### Example
```sql
CREATE VIEW student_view AS
SELECT student_id, student_name, gpa
FROM Students
WHERE gpa > 3.0;
```


### Trigger
A trigger is a procedure that is automatically invoked by the DBMS in a response to a specific changes in the database.

Trigger is automatically fired whenever the table is affected by SQL operation


Trigger follow Event-Condition-Action (ECA) model:
- **Event**: The database operation that triggers the action (INSERT, UPDATE, DELETE)
- **Condition**: The condition that must be met for the action to be executed
- **Action**: The operation that is performed when the event occurs and the condition is met


General Syntax:
```sql
CREATE TRIGGER <trigger_name> <time_event>
ON <table_name> When <Predicate> 
<Action_name>
```

There are three event type:
- Insert: Triggered when a new record is inserted into the table.
- Update: Triggered when an existing record is updated.
- Delete: Triggered when a record is deleted from the table.


Two tiggering time:
- BEFORE: Triggered before the event occurs.
- AFTER: Triggered after the event occurs.


Example:
```sql
CREATE TRIGGER overdraft AFTER UPDATE ON pre-paid 
REFERENCING NEW ROW AS new_row
FOR EACH ROW
WHEN new_row.balance < 0
UPDATE pre-paid
SET BLOCKED = 'T';
```

```sql
CREATE TRIGGER BACKUP AFTER DELETE ON pre-paid
REFERENCING OLD ROW AS old_row
FOR EACH ROW
INSERT INTO pre-paid_backup (id, balance, blocked)
VALUES (old_row.id, old_row.balance, old_row.blocked);
```
Which means:
For Each old row deleted from the pre-paid table, insert a new row into the pre-paid_backup table with the same id, balance, and blocked status.



