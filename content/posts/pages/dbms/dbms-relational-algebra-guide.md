---
title: "Relational Algebra Guide: Operations & Examples"
slug: "dbms-relational-algebra-guide"
date: 2025-01-10
description: "Master Relational Algebra: A comprehensive guide to Selection, Projection, Joins, Set Operations, and mathematical foundations of SQL."
showToc: true
weight: 1
series: ["DBMS"]
categories: ["DBMS", "SQL"]
tags: ["Relational Algebra", "Database", "Selection", "Projection", "Joins"]
summary: "Detailed reference for Relational Algebra operations including Sigma, Pi, Joins, and Set theory with practical database examples."
---

# Relational Algebra - Complete Reference

Relational algebra is a procedural query language that defines operations to manipulate relations (tables) in a relational database. It provides a mathematical foundation for database operations and serves as the theoretical basis for SQL.

## 1. Unary Operations

### 1.1 Selection (σ - Sigma)

**Symbol:** σ<sub>condition</sub>(R)

**Purpose:** Selects tuples (rows) that satisfy a given condition

**Syntax:** σ<sub>condition</sub>(Relation)

**Conditions:**
- Uses comparison operators: =, ≠, <, >, ≤, ≥
- Logical operators: ∧ (AND), ∨ (OR), ¬ (NOT)
- Can combine multiple conditions

**Example Tables:**

**Original Employee Table:**
| EID | Name | Age | Dept | Salary |
|-----|------|-----|------|--------|
| 101 | John | 28 | CS | 55000 |
| 102 | Jane | 24 | IT | 45000 |
| 103 | Bob | 35 | CS | 65000 |
| 104 | Alice | 22 | HR | 40000 |

**Selection Examples:**

1. **σ<sub>age > 25</sub>(Employee)** - Select employees older than 25:
| EID | Name | Age | Dept | Salary |
|-----|------|-----|------|--------|
| 101 | John | 28 | CS | 55000 |
| 103 | Bob | 35 | CS | 65000 |

2. **σ<sub>dept = 'CS' ∧ salary > 50000</sub>(Employee)** - Select CS employees with salary > 50000:
| EID | Name | Age | Dept | Salary |
|-----|------|-----|------|--------|
| 101 | John | 28 | CS | 55000 |
| 103 | Bob | 35 | CS | 65000 |

3. **σ<sub>name = 'John' ∨ name = 'Jane'</sub>(Employee)** - Select employees named John or Jane:
| EID | Name | Age | Dept | Salary |
|-----|------|-----|------|--------|
| 101 | John | 28 | CS | 55000 |
| 102 | Jane | 24 | IT | 45000 |

---

### 1.2 Projection (Π - Pi)

**Symbol:** Π<sub>attributes</sub>(R)

**Purpose:** Selects specific columns from a relation and eliminates duplicates

**Syntax:** Π<sub>A₁, A₂, ..., Aₙ</sub>(Relation)

**Conditions:**
- Selected attributes must exist in the relation
- Automatically removes duplicate tuples
- Order of attributes in result follows the order specified

**Projection Examples:**

**Original Employee Table:**
| EID | Name | Age | Dept | Salary |
|-----|------|-----|------|--------|
| 101 | John | 28 | CS | 55000 |
| 102 | Jane | 24 | IT | 45000 |
| 103 | Bob | 35 | CS | 65000 |
| 104 | Alice | 22 | HR | 40000 |

1. **Π<sub>name, age</sub>(Employee)** - Project name and age columns:
| Name | Age |
|------|-----|
| John | 28 |
| Jane | 24 |
| Bob | 35 |
| Alice | 22 |

2. **Π<sub>dept, salary</sub>(Employee)** - Project department and salary:
| Dept | Salary |
|------|--------|
| CS | 55000 |
| IT | 45000 |
| CS | 65000 |
| HR | 40000 |

3. **Π<sub>dept</sub>(Employee)** - Project only department (duplicates removed):
| Dept |
|------|
| CS |
| IT |
| HR |

---

### 1.3 Rename (ρ - Rho)

**Symbol:** ρ<sub>new_name/old_name</sub>(R) or ρ<sub>S(A₁, A₂, ..., Aₙ)</sub>(R)

**Purpose:** Renames relations and/or attributes

**Syntax Options:**
ρ<sub>new_attr/old_attr</sub>(R)        # Rename attribute
ρ<sub>S</sub>(R)                        # Rename relation to S
ρ<sub>S(A₁, A₂, ..., Aₙ)</sub>(R)      # Rename relation and all attributes

**Rename Examples:**

**Original Employee Table:**
| EID | Name | Age | Dept |
|-----|------|-----|------|
| 101 | John | 28 | CS |
| 102 | Jane | 24 | IT |

1. **ρ<sub>emp_name/name</sub>(Employee)** - Rename 'name' attribute to 'emp_name':
| EID | emp_name | Age | Dept |
|-----|----------|-----|------|
| 101 | John | 28 | CS |
| 102 | Jane | 24 | IT |

2. **ρ<sub>Staff</sub>(Employee)** - Rename Employee relation to Staff:
**Staff Table:**
| EID | Name | Age | Dept |
|-----|------|-----|------|
| 101 | John | 28 | CS |
| 102 | Jane | 24 | IT |

3. **ρ<sub>E(eid, ename, age, department)</sub>(Employee)** - Rename relation and all attributes:
**E Table:**
| eid | ename | age | department |
|-----|--------|-----|------------|
| 101 | John | 28 | CS |
| 102 | Jane | 24 | IT |

---

## 2. Combining Operations

| Operation | Example | Explanation |
|-----------|---------|-------------|
| **Selection + Projection** | Π<sub>name, salary</sub>(σ<sub>dept = 'CS'</sub>(Employee)) | First select CS employees, then project name and salary |


---

## 3. Set Theory Operations

### 3.1 Union (∪)

**Symbol:** R ∪ S

**Purpose:** Combines tuples from two relations, eliminating duplicates

**Conditions (Union Compatibility):**
- Both relations must have the **same number of attributes**
- Corresponding attributes must have **compatible data types**
- Attribute names should be the same (or renamed to match)

**Union Example:**

**Students Table:**
| Name | Age |
|------|-----|
| John | 20 |
| Jane | 22 |
| Bob | 21 |

**Faculty Table:**
| Name | Age |
|------|-----|
| Dr. Smith | 45 |
| Dr. Brown | 50 |
| Jane | 22 |

**Students ∪ Faculty (Union Result):**
| Name | Age |
|------|-----|
| John | 20 |
| Jane | 22 |
| Bob | 21 |
| Dr. Smith | 45 |
| Dr. Brown | 50 |

*Note: Jane appears only once (duplicates eliminated)*

---

### 3.2 Intersection (∩)

**Symbol:** R ∩ S

**Purpose:** Returns tuples common to both relations

**Conditions (Union Compatibility):**
- Both relations must have the **same number of attributes**
- Corresponding attributes must have **compatible data types**

**Intersection Example:**

**Current Students Table:**
| Name | Year |
|------|------|
| John | 2023 |
| Jane | 2024 |
| Bob | 2025 |

**Alumni Table:**
| Name | Year |
|------|------|
| Jane | 2024 |
| Alice | 2022 |
| Tom | 2023 |

**Current Students ∩ Alumni (Intersection Result):**
| Name | Year |
|------|------|
| Jane | 2024 |

*Note: Only Jane with Year 2024 exists in both tables*

---

### 3.3 Set Difference (−)

**Symbol:** R − S

**Purpose:** Returns tuples in R but not in S

**Conditions (Union Compatibility):**
- Both relations must have the **same number of attributes**
- Corresponding attributes must have **compatible data types**

**Set Difference Example:**

**All Students Table:**
| Name | ID |
|------|-----|
| John | 101 |
| Jane | 102 |
| Bob | 103 |
| Alice | 104 |

**Graduated Students Table:**
| Name | ID |
|------|-----|
| John | 101 |
| Alice | 104 |

**All Students − Graduated Students (Difference Result):**
| Name | ID |
|------|-----|
| Jane | 102 |
| Bob | 103 |

*Note: Students who have NOT graduated*

---

### 3.4 Cartesian Product (×)

**Symbol:** R × S

**Purpose:** Combines every tuple from R with every tuple from S

**Conditions:**
- When relations have **common attribute names**, use RelationName.attribute notation
- If no common attributes, proceed normally

**Cartesian Product Examples:**

**Case 1: No Common Attributes**

**Employee Table:**
| Name | Dept |
|------|------|
| John | CS |
| Jane | IT |

**Project Table:**
| PID | Budget |
|-----|--------|
| P1 | 10000 |
| P2 | 15000 |

**Employee × Project:**
| Name | Dept | PID | Budget |
|------|------|-----|--------|
| John | CS | P1 | 10000 |
| John | CS | P2 | 15000 |
| Jane | IT | P1 | 10000 |
| Jane | IT | P2 | 15000 |

**Case 2: Common Attributes (Name)**

**Student Table:**
| Name | Age |
|------|-----|
| John | 20 |
| Jane | 22 |

**Teacher Table:**
| Name | Subject |
|------|---------|
| John | Math |
| Alice | Physics |

**Student × Teacher (using RelationName.attribute):**
| Student.Name | Age | Teacher.Name | Subject |
|-------------|-----|-------------|---------|
| John | 20 | John | Math |
| John | 20 | Alice | Physics |
| Jane | 22 | John | Math |
| Jane | 22 | Alice | Physics |

*Note: Common attribute 'Name' requires RelationName.attribute notation*

---

## 4. Join Operations

### 4.1 Inner Joins

#### 4.1.1 Natural Join (⋈)

**Symbol:** R ⋈ S

**Purpose:** Joins relations on common attributes with same names

**Conditions:**
- Relations must have **at least one attribute with the same name**
- Common attributes must have **compatible data types**
- **Automatically eliminates duplicate columns**

**Natural Join Example:**

**Employee Table:**
| EID | Name | DID |
|-----|------|-----|
| 1 | John | 10 |
| 2 | Jane | 20 |
| 3 | Bob | 10 |
| 4 | Alice | 30 |

**Department Table:**
| DID | DName |
|-----|--------|
| 10 | CS |
| 20 | IT |
| 40 | HR |

**Employee ⋈ Department (Natural Join Result):**
| EID | Name | DID | DName |
|-----|------|-----|--------|
| 1 | John | 10 | CS |
| 2 | Jane | 20 | IT |
| 3 | Bob | 10 | CS |

*Note: Common attribute DID appears only once (combined/eliminated). Alice (DID=30) and HR (DID=40) not included as no match.*

---

#### 4.1.2 Theta Join (θ-Join)

**Symbol:** R ⋈<sub>θ</sub> S where θ is a condition

**Purpose:** Joins relations based on any comparison condition

**Conditions:**
- Can use any comparison operator: =, ≠, <, >, ≤, ≥
- Can combine multiple conditions with ∧, ∨
- **Does not eliminate duplicate columns** (unlike natural join)
- **Uses RelationName.attribute when common attributes exist**

**Theta Join Examples:**

**Case 1: Different Attribute Names**

**Employee Table:**
| EID | Salary |
|-----|--------|
| 1 | 50000 |
| 2 | 70000 |
| 3 | 65000 |

**Manager Table:**
| MID | MaxSal |
|-----|--------|
| 101 | 60000 |
| 102 | 80000 |

**Employee ⋈<sub>Salary < MaxSal</sub> Manager:**
| EID | Salary | MID | MaxSal |
|-----|--------|-----|--------|
| 1 | 50000 | 101 | 60000 |
| 1 | 50000 | 102 | 80000 |
| 2 | 70000 | 102 | 80000 |
| 3 | 65000 | 102 | 80000 |

**Case 2: Common Attribute Names**

**Employee Table:**
| EID | Name | Salary |
|-----|------|--------|
| 1 | John | 50000 |
| 2 | Jane | 70000 |

**Manager Table:**
| MID | Name | Salary |
|-----|------|--------|
| 101 | Bob | 60000 |
| 102 | Alice | 80000 |

**Employee ⋈<sub>Employee.Salary < Manager.Salary</sub> Manager:**
| EID | Employee.Name | Employee.Salary | MID | Manager.Name | Manager.Salary |
|-----|---------------|-----------------|-----|--------------|----------------|
| 1 | John | 50000 | 101 | Bob | 60000 |
| 1 | John | 50000 | 102 | Alice | 80000 |
| 2 | Jane | 70000 | 102 | Alice | 80000 |

*Note: RelationName.attribute used for common attributes (Name, Salary). All columns retained.*

---

#### 4.1.3 Equi-Join

**Symbol:** R ⋈<sub>A = B</sub> S

**Purpose:** Special case of theta join using only equality conditions

**Conditions:**
- Uses only equality (=) operator
- **Does not eliminate duplicate columns** (unlike natural join)
- Attributes being compared can have different names
- **Uses RelationName.attribute when common attributes exist**

**Equi-Join Examples:**

**Case 1: Different Attribute Names**

**Employee Table:**
| EID | DeptID |
|-----|---------|
| 1 | 10 |
| 2 | 20 |
| 3 | 10 |

**Department Table:**
| ID | Name |
|----|------|
| 10 | CS |
| 20 | IT |
| 30 | HR |

**Employee ⋈<sub>DeptID = ID</sub> Department:**
| EID | DeptID | ID | Name |
|-----|---------|-----|------|
| 1 | 10 | 10 | CS |
| 2 | 20 | 20 | IT |
| 3 | 10 | 10 | CS |

**Case 2: Common Attribute Names**

**Student Table:**
| ID | Name | Age |
|----|------|-----|
| 1 | John | 20 |
| 2 | Jane | 22 |

**Course Table:**
| ID | Name | Credits |
|----|------|---------|
| 101 | Math | 3 |
| 102 | Physics | 4 |

**Student ⋈<sub>Student.ID = Course.ID</sub> Course:**
| Student.ID | Student.Name | Age | Course.ID | Course.Name | Credits |
|------------|--------------|-----|-----------|-------------|---------|
| (No matches since IDs don't overlap) |

**Better Example - Student ⋈<sub>Student.Name = Course.Name</sub> Course:**
*Assuming Course has instructor names matching student names*

*Note: RelationName.attribute notation required for common attributes. Both duplicate columns retained.*

**Difference from Natural Join:**
- Equi-join keeps duplicate columns
- Natural join eliminates duplicate columns
- Equi-join can join on different attribute names

---

### 4.2 Outer Joins

**Purpose:** Include unmatched tuples from one or both relations, filling with NULLs

**Important:** When relations have common attributes (other than join attributes), use RelationName.attribute notation for all joins except Natural Join.

**Sample Tables for All Examples:**

**Employee Table:**
| EID | Name | DID |
|-----|------|-----|
| 1 | John | 10 |
| 2 | Jane | 20 |
| 3 | Bob | 30 |

**Department Table:**
| DID | DName |
|-----|--------|
| 10 | CS |
| 20 | IT |
| 40 | HR |

**Outer Join Results Comparison:**

| Join Type | Symbol | Result Explanation |
|-----------|--------|---------|
| **Left Outer** | R ⟕ S | All from left + NULLs for unmatched |
| **Right Outer** | R ⟖ S | All from right + NULLs for unmatched |  
| **Full Outer** | R ⟗ S | All from both + NULLs for unmatched |

**Results (Natural Join Based - Common DID Combined):**

**Employee ⟕ Department (Left)** | **Employee ⟖ Department (Right)** | **Employee ⟗ Department (Full)**
|-----|-----|-----|
| EID \| Name \| DID \| DName | EID \| Name \| DID \| DName | EID \| Name \| DID \| DName |
| 1 \| John \| 10 \| CS | 1 \| John \| 10 \| CS | 1 \| John \| 10 \| CS |
| 2 \| Jane \| 20 \| IT | 2 \| Jane \| 20 \| IT | 2 \| Jane \| 20 \| IT |
| 3 \| Bob \| 30 \| NULL | NULL \| NULL \| 40 \| HR | 3 \| Bob \| 30 \| NULL |
| | | NULL \| NULL \| 40 \| HR |

*Note: Based on Natural Join (common DID combined). For other outer joins, use RelationName.attribute notation.*

**SQL Equivalents:** LEFT JOIN, RIGHT JOIN, FULL OUTER JOIN

---

## 5. Additional Relational Operations

| Operation | Symbol/Syntax | Purpose | Example |
|-----------|---------------|---------|---------|
| **Assignment** | Temp ← Expression | Store intermediate results | `Temp ← σ<sub>age > 25</sub>(Employee)` |
| **Generalized Projection** | Π<sub>F₁, F₂, ..., Fₙ</sub>(R) | Include computed fields | `Π<sub>name, salary*0.1 as tax</sub>(Employee)` |

**Features of Generalized Projection:**
- Arithmetic operations: +, -, ×, ÷
- String operations: concatenation  
- Functions: mathematical, date functions

---

### 5.3 Aggregate Functions and Grouping

#### 5.3.1 Basic Aggregation

**Symbol:** G<sub>F₁, F₂, ..., Fₙ</sub>(R) 

**Aggregate Functions:**
- **SUM:** Total of numeric values
- **AVG:** Average of numeric values  
- **COUNT:** Number of tuples
- **MAX:** Maximum value
- **MIN:** Minimum value

**Aggregate Function Examples:**

**Employee Table:**
| EID | Name | Dept | Salary |
|-----|------|------|--------|
| 1 | John | CS | 55000 |
| 2 | Jane | IT | 45000 |
| 3 | Bob | CS | 65000 |
| 4 | Alice | IT | 50000 |
| 5 | Tom | HR | 40000 |

**Basic Aggregation Examples:**

1. **G<sub>COUNT(*)</sub>(Employee)** - Count all employees:
| COUNT |
|-------|
| 5 |

2. **G<sub>AVG(salary)</sub>(Employee)** - Average salary:
| AVG_Salary |
|------------|
| 51000 |

3. **G<sub>MAX(salary), MIN(salary)</sub>(Employee)** - Max and min salary:
| MAX_Salary | MIN_Salary |
|------------|------------|
| 65000 | 40000 |



### 5.4 Division (÷)

**Symbol:** R ÷ S

**Purpose:** Finds tuples in R that are related to ALL tuples in S

**Conditions:**
- Attributes of S must be a **subset** of attributes of R
- Complex operation often used for "for all" queries

**Division Example - "Find students enrolled in ALL courses":**

**Enrollment Table (R):**
| SID | CID |
|-----|-----|
| S1 | C1 |
| S1 | C2 |
| S1 | C3 |
| S2 | C1 |
| S2 | C2 |
| S3 | C1 |

**AllCourses Table (S):**
| CID |
|-----|
| C1 |
| C2 |
| C3 |

**Enrollment ÷ AllCourses (Division Result):**
| SID |
|-----|
| S1 |

*Note: Only S1 is enrolled in ALL courses (C1, C2, C3). S2 is missing C3, S3 is missing C2 and C3.*

**Step-by-step Process:**
1. S1 has {C1, C2, C3} - contains all courses ✓
2. S2 has {C1, C2} - missing C3 ✗  
3. S3 has {C1} - missing C2, C3 ✗

---



## Generalized Projection
Generalized projection allows for more complex expressions in the projection operation, including arithmetic operations, string concatenation, and function applications.
**Symbol:** Π<sub>F₁, F₂, ..., Fₙ</sub>(R)
**Purpose:** Projects specific attributes and allows for computed fields


Table Example:
| EID | Name | Salary |
|-----|------|--------|
| 1 | John | 55000 |
| 2 | Jane | 45000 |
| 3 | Bob | 65000 |

Example:

Π<sub>name, salary * 0.1</sub>(Employee)


| Name | Salary * 0.1 |
|------|--------------|
| John | 5500 |
| Jane | 4500 |
| Bob  | 6500 |

---

## 6. Database Manipulation Operations

Database manipulation operations in relational algebra are used to modify the contents of relations. These operations correspond to SQL's INSERT, UPDATE, and DELETE operations.

### 6.1 Insertion (∪ with new tuples)

**Purpose:** Add new tuples to a relation

**Syntax:** R ← R ∪ {new tuples}

**Conditions:**
- New tuples must have same schema as existing relation
- Duplicate tuples are automatically eliminated (set property)

**Insertion Examples:**

**Original Employee Table:**
| EID | Name | Dept | Salary |
|-----|------|------|--------|
| 1 | John | CS | 55000 |
| 2 | Jane | IT | 45000 |

**Insert Single Tuple:**
```
Employee ← Employee ∪ {(3, 'Bob', 'HR', 50000)}
```

**Result:**
| EID | Name | Dept | Salary |
|-----|------|------|--------|
| 1 | John | CS | 55000 |
| 2 | Jane | IT | 45000 |
| 3 | Bob | HR | 50000 |

**Insert Multiple Tuples:**
```
Employee ← Employee ∪ {(4, 'Alice', 'CS', 60000), (5, 'Tom', 'IT', 48000)}
```

**Insert from Another Relation:**
```
Employee ← Employee ∪ Π<sub>EID, Name, Dept, Salary</sub>(NewHires)
```

---

### 6.2 Deletion (− Difference)

**Purpose:** Remove tuples that satisfy certain conditions

**Syntax:** R ← R − σ<sub>condition</sub>(R)

**Conditions:**
- Uses selection to identify tuples to delete
- Remaining tuples form the new relation

**Deletion Examples:**

**Original Employee Table:**
| EID | Name | Dept | Salary |
|-----|------|------|--------|
| 1 | John | CS | 55000 |
| 2 | Jane | IT | 45000 |
| 3 | Bob | HR | 50000 |
| 4 | Alice | CS | 60000 |

**Delete by Condition:**
```text
Employee ← Employee − σ<sub>Dept = 'HR'</sub>(Employee)
```

**Result:**
| EID | Name | Dept | Salary |
|-----|------|------|--------|
| 1 | John | CS | 55000 |
| 2 | Jane | IT | 45000 |
| 4 | Alice | CS | 60000 |

**Delete Multiple Conditions:**
```text
Employee ← Employee − σ<sub>Salary < 50000 ∨ Dept = 'IT'</sub>(Employee)
```

**Delete All Tuples:**
```text
Employee ← Employee − Employee  (Results in empty relation)
```

---

### 6.3 Update (Combination of Delete and Insert)

**Purpose:** Modify attribute values of existing tuples

**Approach:** Update = Delete old tuples + Insert modified tuples

**Syntax:** 
```text
R ← (R − σ<sub>condition</sub>(R)) ∪ Π<sub>modified_attributes</sub>(σ<sub>condition</sub>(R))
```

**Update Examples:**

**Original Employee Table:**
| EID | Name | Dept | Salary |
|-----|------|------|--------|
| 1 | John | CS | 55000 |
| 2 | Jane | IT | 45000 |
| 3 | Bob | HR | 50000 |

**Update Single Attribute:**
*Increase salary by 10% for CS employees*

```text
Employee ← (Employee − σ<sub>Dept = 'CS'</sub>(Employee)) ∪ 
           Π<sub>EID, Name, Dept, Salary*1.1</sub>(σ<sub>Dept = 'CS'</sub>(Employee))
```

**Result:**
| EID | Name | Dept | Salary |
|-----|------|------|--------|
| 1 | John | CS | 60500 |
| 2 | Jane | IT | 45000 |
| 3 | Bob | HR | 50000 |

**Update Multiple Attributes:**
*Change Bob's department to IT and increase salary*

```text
Employee ← (Employee − σ<sub>Name = 'Bob'</sub>(Employee)) ∪ 
           {(3, 'Bob', 'IT', 55000)}
```

**Update with Complex Conditions:**
*Give 5% raise to employees earning less than 50000*

```text
LowSalary ← σ<sub>Salary < 50000</sub>(Employee)
Employee ← (Employee − LowSalary) ∪ 
           Π<sub>EID, Name, Dept, Salary*1.05</sub>(LowSalary)
```

---
