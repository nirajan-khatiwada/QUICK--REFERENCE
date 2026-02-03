  In Python, an **array** is commonly implemented using a **list**. A list is an **ordered, mutable collection** that can store elements of any data type.

---

## Creating an Array with a Fixed Size

Python does not have fixed-size arrays like C/C++. However, we can **pre-allocate** space using `None`.

```python
size = 10
a = [None] * size
```

---

## Inserting Elements into an Array

We can insert values into the array using a loop.

```python
for i in range(0, len(a)):
    inp = input("Enter the number: ")
    a[i] = inp
```

---

## Initializing an Array at Declaration Time

We can declare and insert elements **at the same time**.

```python
a = [1, 2, 3, 4, 5, 3, 2, 3]
```

---

## Printing Array Elements Using a Loop

```python
for i in range(0, len(a)):
    print(a[i])
```

---

## Printing the Entire Array Directly

```python
print(a)
```

**Output Example:**

```
[1, 2, 3, 4, 5, 3, 2, 3]
```

This is the easiest way to view all elements at once.

---
