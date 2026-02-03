---
title: "Singly Linked Lists in Python: Implementation & Operations"
slug: "singly-linked-lists-python"
date: 2024-10-21
description: "Learn how to implement Singly Linked Lists in Python. Covers insertion, deletion, reversal, and traversal with code."
showToc: true
weight: 2
series: ["DSA"]
categories: ["DSA", "Python"]
tags: ["Linked List", "Singly Linked List", "Data Structures", "Python"]
summary: "Step-by-step implementation of Singly Linked Lists in Python, including standard operations and interview questions."
images: ["/images/linked-list.png"]
---

A **Singly Linked List (SLL)** is a fundamental linear data structure where elements are stored in **nodes**, and each node points to the **next node** in the sequence. Unlike Python lists (dynamic arrays), Python does **not** provide a built-in linked list because lists already cover most use cases efficiently.

However, **linked lists are extremely important for coding interviews and low-level understanding of data structures**, especially for understanding how data is managed in memory. Learning SLLs also helps you understand the **internal behavior of Python lists and other back-end systems**.

---

## Structure of a Singly Linked List

A singly linked list consists of:

* **Head** → A reference to the first node in the list
* **Nodes** → Each node contains:

  * Data
  * A pointer to the next node

```
Head → [Data | Next] → [Data | Next] → [Data | Next] → None
```

### Key Characteristics

* Each node points **only forward**
* No backward traversal is possible
* The last node points to None
* Traversal always starts from the **head**

---

## Core Components of SLL

To implement a singly linked list, we need **two classes**:

1. Node
2. LinkedList

---

## 1. Node Class

The Node class represents an individual element of the linked list.

### Components

* **Data** → The value stored in the node (int, string, object, etc.)
* **Pointer (next_element)** → Reference to the next node

### Node Implementation

```python
class Node:
    def __init__(self, data):
        # Store the value in the node
        self.data = data
        # Pointer to the next node, initially None
        self.next_element = None
```

### Explanation

* `data` holds the value
* `next_element` links to the next node
* Initially, `next_element` is None

---

## 2. LinkedList Class

The LinkedList class manages the collection of nodes.

### Head Node

* The **head** stores the reference to the first node
* If the list is empty, `head = None`
* All operations begin from the head

### LinkedList Initialization

```python
class LinkedList:
    def __init__(self):
        # Start with an empty list
        self.head_node = None
```

---

## Basic Linked List Operations

### Common Operations

| Operation         | Description             | Time Complexity |
| ----------------- | ----------------------- | --------------- |
| get_head()        | Returns head node       | O(1)            |
| is_empty()        | Checks if list is empty | O(1)            |
| insert_at_head()  | Insert at beginning     | O(1)            |
| insert_at_tail()  | Insert at end           | O(n)            |
| insert_at_k()     | Insert at position k    | O(n)            |
| delete_at_head()  | Remove first node       | O(1)            |
| delete_by_value() | Remove a node by value  | O(n)            |
| delete_at_tail()  | Remove last node        | O(n)            |
| search()          | Find value in list      | O(n)            |

---

## Helper Functions

### get_head()

```python
def get_head_node(self):
    # Return the head node of the list
    return self.head_node
```

**Time Complexity:** O(1)

---

### is_empty()

```python
def is_empty(self):
    # Returns True if list is empty
    return self.head_node is None
```

**Time Complexity:** O(1)

---

## Singly Linked List Insertion

### Types of Insertion

1. Insert at Head
2. Insert at Tail
3. Insert at k-th Position

---

### Insertion at Head

**Concept:**

* New node becomes the first node
* New node points to the old head

```
NewNode → OldHead → ... → None
```

### Code

```python
def insert_at_head(self, data):
    # Step 1: Create a new node
    temp_node = Node(data)
    # Step 2: Point new node to current head
    temp_node.next_element = self.head_node
    # Step 3: Update head to new node
    self.head_node = temp_node
```

**Time Complexity:** O(1)

---

### Insertion at Tail

**Concept:**

* Traverse to the last node
* Attach the new node at the end

### Code

```python
def insert_at_tail(self, data):
    # Step 1: Create a new node
    node = Node(data)
    # Step 2: If list is empty, set head to new node
    if self.is_empty():
        self.head_node = node
    else:
        # Step 3: Traverse to the last node
        temp = self.head_node
        while temp.next_element is not None:
            temp = temp.next_element
        # Step 4: Link new node at the end
        temp.next_element = node
```

**Time Complexity:** O(n)

---

### Insertion at k-th Position

**Concept:**

* Traverse to (k-1) position
* Rewire pointers

### Code

```python
def insert_at_k_node(self, data, k):
    # Step 1: Create a new node
    node = Node(data)
    # Step 2: Insert at head if k == 1
    if k == 1:
        node.next_element = self.head_node
        self.head_node = node
        return

    # Step 3: Traverse to (k-1) node
    position = 1
    temp = self.head_node
    while position < k-1 and temp is not None:
        temp = temp.next_element
        position += 1

    # Step 4: Rewire pointers to insert new node
    node.next_element = temp.next_element
    temp.next_element = node
```

**Time Complexity:** O(n)

---

## Singly Linked List Deletion

### Deletion at Head

**Concept:**

* Move head to the next node

### Code

```python
def remove_first_node(self):
    # Step 1: Check if list is empty
    if not self.is_empty():
        # Step 2: Update head to next node
        self.head_node = self.head_node.next_element
```

**Time Complexity:** O(1)

---

### Deletion at Tail

**Concept:**

* Traverse to the **second last node**
* Set its `next_element` to None

### Code

```python
def remove_last_node(self):
    # Step 1: If list is empty, return
    if self.is_empty():
        return
    # Step 2: If only one node exists, remove head
    if self.head_node.next_element is None:
        self.head_node = None
        return

    # Step 3: Traverse to second last node
    temp = self.head_node
    while temp.next_element.next_element is not None:
        temp = temp.next_element

    # Step 4: Remove last node
    temp.next_element = None
```

**Time Complexity:** O(n)

---

### Deletion by Value

**Concept:**

* Traverse list and find the node whose next node contains the value
* Rewire pointers to skip the node with the value

### Code

```python
def delete_k_value(self, k):
    # Step 1: Check if list is empty
    if self.is_empty():
        return
    # Step 2: If head contains the value, remove head
    if self.head_node.data == k:
        self.head_node = self.head_node.next_element
        return

    # Step 3: Traverse to find node with value
    temp = self.head_node
    while temp.next_element is not None:
        if temp.next_element.data == k:
            # Step 4: Remove node by rewiring pointer
            temp.next_element = temp.next_element.next_element
            return
        temp = temp.next_element
```

**Time Complexity:** O(n)

---

### Delete k-th Node

**Concept:**

* Traverse to (k-1) position
* Skip the k-th node

### Code

```python
def delete_k_node(self, k):
    # Step 1: Check if list is empty
    if self.is_empty():
        return
    # Step 2: Delete head if k == 1
    if k == 1:
        self.head_node = self.head_node.next_element
        return

    # Step 3: Traverse to (k-1) node
    temp = self.head_node
    position = 1
    while position < k-1:
        temp = temp.next_element
        position += 1

    # Step 4: Delete k-th node by rewriting pointer
    if temp.next_element.next_element is None:
        temp.next_element = None
    else:
        temp.next_element = temp.next_element.next_element
```

**Time Complexity:** O(n)

---

## Traversal and Printing

**Concept:**

* Start from head
* Move to next node until None

### Code

```python
def print_list(self):
    # Step 1: Start from head
    temp = self.head_node
    # Step 2: Traverse and print each node
    while temp is not None:
        print(f"{temp.data} ->", end=" ")
        temp = temp.next_element
    # Step 3: Print NULL at end
    print("NULL")
```

**Time Complexity:** O(n)

---

## Search Operation

**Concept:**

* Traverse list
* Return True if node contains key

### Code

```python
def search(self, key):
    # Step 1: Start from head
    temp = self.head_node
    # Step 2: Traverse and check each node
    while temp is not None:
        if temp.data == key:
            return True
        temp = temp.next_element
    # Step 3: Key not found
    return False
```

**Time Complexity:** O(n)

---

## Linked Lists vs Python Lists

| Feature       | Linked List    | Python List       |
| ------------- | -------------- | ----------------- |
| Memory        | Non-contiguous | Contiguous        |
| Insert/Delete | Efficient      | Costly (shifting) |
| Access        | Sequential     | Random (O(1))     |
| Traversal     | Forward only   | Bidirectional     |
