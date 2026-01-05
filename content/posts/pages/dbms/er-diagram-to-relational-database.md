---
title: "ER Diagram to Relational Database Conversion"
slug: "er-diagram-to-relational-database"
date: 2025-01-25
description: "Step-by-step guide to converting ER Diagrams to Relational Tables. Handle 1:1, 1:N, and N:M relationships with SQL examples."
showToc: true
weight: 6
series: ["DBMS"]
categories: ["DBMS", "Database Design"]
tags: ["ER Diagram", "Database Design", "Normalization", "Relationships", "SQL"]
summary: "Learn the rules of mapping ER models to relational schemas. Includes practical SQL code for implementing One-to-One, One-to-Many, and Many-to-Many relationships."
images: ["/images/dbms3.jpg"]
---

## Conversion of ER relation to Relational Database and How to use it properly

There are basically three types of relation in a database:

### 1:1 Relation

In relational database term,
- **1:1 Relation** means that one record in a table is related to one and only one record in another table.

In ER diagram term,
- **1:1 Relation** means that one entity in an entity set is related to one and only one entity in another entity set.

Conversion of relation from ER to Relational Database:
**Process**
- Consider we have two entities, `A` and `B`, with a 1:1 relationship.
- Create a table for each entity with their attributes.
- Add the primary key of either entity as a foreign key in the other entity.


Example:
![1:1 Relation Example](/images/dbms1.jpg)

### 1: N Relation
In relational database term,
- **1:N Relation** means that one record in a table can be related to multiple records
in another table.

In ER diagram term,
- **1:N Relation** means that one entity in an entity set is related to multiple entities in another entity set.

Conversion of relation from ER to Relational Database:
- **Process**
- Consider we have two entities, `A` and `B`, with a 1:N relationship.
- Create a table for each entity with their attributes.
- Add the primary key of the entity on the "one" side (A) as a foreign key in the entity on the "many" side (B).
Example:
![1:N Relation Example](/images/dbms2.jpg)


### N: M Relation
In relational database term,
- **N:M Relation** means that Consider we have two entities, `A` and `B`, Then one record in table A can be related to multiple records in table B and one record in table B can be related to multiple records in table A.

-Simply, N:M Relation means that one record in a table can be related to multiple records in another table and vice versa.

In ER diagram term,
- **N:M Relation** means that one entity in an entity set is related to multiple entities in another entity set and vice versa.

Conversion of relation from ER to Relational Database:
- Consider we have two entities, `A` and `B`, with a N:M relationship.
- Create a table for each entity with their attributes.
- Create a new table (junction table) to represent the relationship.
- The junction table will have two foreign keys, one referencing the primary key one one entity (A) and the other referencing the primary key of the other entity (B) to maintain the relationship.

Example:
![N:M Relation Example](/images/dbms3.jpg)


## Practical Example (The SQL Code)

### One-to-One Relationship Example

#### 1. Create the `Person` and `Passport` tables with a 1:1 relationship:
```sql
CREATE TABLE Person (
    person_id INT PRIMARY KEY,
    name VARCHAR(100)
);

CREATE TABLE Passport (
    passport_id INT PRIMARY KEY,
    counry VARCHAR(50),
    person_id INT UNIQUE,
    issue_date DATE,
    FOREIGN KEY (person_id) REFERENCES Person(person_id)
);
```

#### 2. Querying the 1:1 Relationship

1. Give the name of the person and their passport details:
```sql
SELECT name,Person.person_id,passport_id,counry,issue_date
FROM Person
INNER JOIN Passport ON Person.person_id = Passport.person_id;
```
2. Find a Person with a specific passwort ID:
```sql
SELECT name, passport_id, counry, issue_date
FROM Person
INNER JOIN Passport ON Person.person_id = Passport.person_id
WHERE Passport.passport_id = 1;
```


3. Find name of all person of a specific country:
```sql
SELECT name
FROM Person
INNER JOIN Passport ON Person.person_id = Passport.person_id
WHERE counry = 'USA';
```

4. Find all passport details of a specific person:
```sql
SELECT passport_id, counry, issue_date
FROM Passport INNER JOIN Person ON Passport.person_id = Person.person_id
WHERE Person.person_id = 1;
```

5. Find all passport details of a person named 'John Doe':
```sql
SELECT passport_id, counry, issue_date
FROM Passport INNER JOIN Person ON Passport.person_id = Person.person_id
WHERE Person.name = 'John Doe';
```




### One-to-Many Relationship Example
#### 1. Create the `Author` and `Book` tables with a 1:N relationship:
```sql
CREATE TABLE Author (
    author_id INT PRIMARY KEY,
    name VARCHAR(100)
);

CREATE TABLE Book (
    book_id INT PRIMARY KEY,
    title VARCHAR(100),
    author_id INT,
    FOREIGN KEY (author_id) REFERENCES Author(author_id)
);
```

#### 2. Querying the 1:N Relationship

1. List all books with their authors:
```sql
SELECT name,title FROM Author
INNER JOIN Book ON Author.author_id = Book.author_id;
```

2. Find authors who have written a specific book:
```sql
SELECT name FROM Author
INNER JOIN Book ON Author.author_id = Book.author_id
WHERE book_id = 1;
```

3. Find author of a specific book title:
```sql
SELECT name FROM Author
INNER JOIN Book ON Author.author_id = Book.author_id
WHERE Book.title = 'Book Title';
```
4. List all books by a specific author:
```sql
SELECT title
FROM Book INNER JOIN Author ON Book.author_id = Author.author_id
WHERE Author.author_id = 1;
```

5. Find all books written by authors with a specific name:
```sql
SELECT title
FROM Book INNER JOIN Author ON Book.author_id = Author.author_id
WHERE Author.name = 'Author Name';
```

### Many-to-Many Relationship Example
#### 1. Create the `Student`, `Course`, and `Enrollment` tables with a N:M relationship:
```sql
CREATE TABLE Student (
    student_id INT PRIMARY KEY,
    name VARCHAR(100)
);
CREATE TABLE Course (
    course_id INT PRIMARY KEY,
    title VARCHAR(100)
);
CREATE TABLE Enrollment (
    student_id INT,
    course_id INT,
    PRIMARY KEY (student_id, course_id),
    FOREIGN KEY (student_id) REFERENCES Student(student_id),
    FOREIGN KEY (course_id) REFERENCES Course(course_id)
);
```

#### 2. Querying the N:M Relationship
1. List all students and their enrolled courses:
```sql
SELECT name, title
FROM Student
INNER JOIN Enrollment ON Student.student_id = Enrollment.student_id
INNER JOIN Course ON Enrollment.course_id = Course.course_id;
```

2. Find all courses a specific student is enrolled in:
```sql
SELECT title
FROM Course INNER JOIN Enrollment ON Course.course_id = Enrollment.course_id
INNER JOIN Student ON Enrollment.student_id = Student.student_id
WHERE Student.student_id = 1;
```
3. Find all students enrolled in a specific course:
```sql
SELECT name
FROM Student INNER JOIN Enrollment ON Student.student_id = Enrollment.student_id
INNER JOIN Course ON Enrollment.course_id = Course.course_id
WHERE Course.course_id = 1;
```

4. Find all courses for students with a specific name:
```sql
SELECT title
FROM Course INNER JOIN Enrollment ON Course.course_id = Enrollment.course_id
INNER JOIN Student ON Enrollment.student_id = Student.student_id
WHERE Student.name = 'Student Name';
```


5. Find all students enrolled in courses with a specific title:
```sql
SELECT name
FROM Student INNER JOIN Enrollment ON Student.student_id = Enrollment.student_id
INNER JOIN Course ON Enrollment.course_id = Course.course_id
WHERE Course.title = 'Course Title';
```


### Mapping the Stong Entity Set
For each strong entity set, we create a table . The number of columns in the table will be equal to the number of attributes of the entity set. The primary key of the entity set will be the primary key of the table.


### Mapping the Composite Attribute
For mapping composite attribute we flatten the composite attribute into simple attributes and include them in the table of the entity.

### Mapping the Multi-valued Attribute
1. Create a table for each entity set
2. the columns of the table will be the attributes of the entity set except the multi-valued attribute.
3. The primary key of the table will be the primary key of the entity set.
4. for each multi-valued attribute create a new table.
5. The columns of the new table will be the multi-valued attribute and the primary key of the entity set.
6. The primary key of the new table will be the combination of the multi-valued attribute and the primary key of the entity set.


![Multi Valued Attribute Example](/images/dbms4.svg)