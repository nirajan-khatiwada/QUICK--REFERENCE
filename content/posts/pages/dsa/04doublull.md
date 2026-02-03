---
title: "Doubly Linked Lists in Python: Bidirectional Traversal"
slug: "doubly-linked-lists-python"
date: 2024-10-22
description: "Understand Doubly Linked Lists (DLL) in Python. Learn to implement bidirectional nodes, insertion, and deletion logic."
showToc: true
weight: 3
series: ["DSA"]
categories: ["DSA", "Python"]
tags: ["Linked List", "Doubly Linked List", "Data Structures", "Python"]
summary: "A complete guide to Doubly Linked Lists in Python, featuring bidirectional traversal and advanced node operations."
images: ["/images/dll.png"]
---

A **Doubly Linked List (DLL)** is a linear data structure where elements are stored in **nodes**, and each node contains **pointers to both the next and previous nodes**. Unlike Singly Linked Lists, DLL allows **bidirectional traversal**. DLLs are important for coding interviews, memory management, and implementing structures like deque and LRU cache.

---

## Structure of a Doubly Linked List

A doubly linked list consists of:

* **Head** → Reference to the first node
* **Tail** → Reference to the last node (optional)
* **Nodes** → Each node contains:

  * Data
  * Pointer to the next node (`next_element`)
  * Pointer to the previous node (`previous_element`)

```
None <- [Prev | Data | Next] <-> [Prev | Data | Next] <-> [Prev | Data | Next] -> None
```

### Key Characteristics

* Each node points both forward and backward
* Traversal can start from head or tail
* Last node points next to None, first node points previous to None

---

## Core Components of DLL

To implement a DLL, we need **two classes**:

1. Node
2. DoublyLinkedList

---

## 1. Node Class

### Components

* **Data** → Value stored in the node
* **Pointer (next_element)** → Reference to the next node
* **Pointer (previous_element)** → Reference to the previous node

### Node Implementation

```python
class Node:
    def __init__(self, data):
        # Store the value in the node
        self.data = data
        # Pointer to the next node
        self.next_element = None
        # Pointer to the previous node
        self.previous_element = None
```

### Explanation

* `data` holds the value
* `next_element` links to the next node
* `previous_element` links to the previous node
* Initially, both pointers are `None`

---

## 2. DoublyLinkedList Class

### Head and Tail

* **head_node** → Reference to the first node
* **tail_node** → Reference to the last node

### Initialization

```python
class DoublyLinkedList:
    def __init__(self):
        # Start with an empty list
        self.head_node = None
        self.tail_node = None
```

---

## Helper Functions

### get_head()

```python
def get_head_node(self):
    # Return the head node of the list
    return self.head_node
```

**Time Complexity:** O(1)

### is_empty()

```python
def is_empty(self):
    # Returns True if list is empty
    return self.head_node is None
```

**Time Complexity:** O(1)

---

## Insertion Operations

### Insert at Head

**Concept:**

* New node becomes the first node
* New node points to the old head
* Old head's previous points to new node
* Update tail if list was empty

```python
def insert_at_head(self, data):
    # Create a new node
    node = Node(data)

    # Link new node to current head
    node.next_element = self.head_node

    # If list is empty, set both head and tail to new node
    if self.is_empty():
        self.tail_node = node
        self.head_node = node

    # If list is not empty, update previous of old head and move head
    else:
        self.head_node.previous_element = node
        self.head_node = node

```

**Time Complexity:** O(1)

---

### Insert at Tail

**Concept:**

* Attach new node at the end
* Update previous pointer of new node
* Update tail

```python
def insert_at_tail(self, data):
    # Create a new node
    node = Node(data)

    # If list is empty, set both head and tail to new node
    if self.is_empty():
        self.head_node = node
        self.tail_node = node
        return

    # If list is not empty, link node after tail and move tail
    else:
        self.tail_node.next_element = node
        node.previous_element = self.tail_node
        self.tail_node = node
```

**Time Complexity:** O(1)

---

### Insert at k-th Position

**Concept:**

* Traverse to (k-1) node
* Rewire pointers for next and previous

```python
def insert_at_k_node(self, data, k):
    # Create a new node
    node = Node(data)

    # If position is 1, insert at head
    if k == 1:
        self.insert_at_head(data)
        return

    # Traverse to (k-1)th node
    temp = self.head_node
    position = 1
    while position < k-1:
        temp = temp.next_element
        position += 1

    # If insertion is at the end, insert at tail
    if temp.next_element is None:
        self.insert_at_tail(data)
        return

    # Insert node between (k-1)th and kth nodes
    else:
        node.next_element = temp.next_element
        node.previous_element = temp
        temp.next_element.previous_element = node
        temp.next_element = node
```

**Time Complexity:** O(n)

---

## Deletion Operations

### Delete at Head

**Concept:**

* Move head to next node
* Update previous pointer of new head
* Update tail if list becomes empty

```python
def delete_head(self):
    # If list is empty, nothing to delete
    if self.is_empty():
        return

    # Move head to next node
    self.head_node = self.head_node.next_element

    # If list becomes empty, update tail
    if self.head_node is None:
        self.tail_node = None

    # Otherwise, update previous pointer of new head
    else:
        self.head_node.previous_element = None

```

**Time Complexity:** O(1)

---

### Delete at Tail

**Concept:**

* Update second last node's next to None
* Update tail pointer

```python
def delete_tail(self):
    # If list is empty, nothing to delete
    if self.is_empty():
        return

    # If list has only one node, clear head and tail
    if self.head_node.next_element is None:
        self.head_node = None
        self.tail_node = None
        return

    # Move tail to previous node and update next pointer
    else:
        temp = self.tail_node
        self.tail_node = temp.previous_element
        self.tail_node.next_ele

```

**Time Complexity:** O(1)

---

### Delete k-th Node

**Concept:**

* Traverse to k-th node
* Update previous and next pointers to remove it
* Update head or tail if needed

```python
def delete_k_node(self, k):
    if self.is_empty():
        return
    if k == 1:
        self.delete_head()
        return
    else:
        temp = self.head_node
        position = 1
        while position < k:
            temp = temp.next_element
            position += 1

        # If deleting the tail node
        if temp.next_element is None:
            self.delete_tail()
            return

        # Rewire pointers to remove k-th node
        temp.previous_element.next_element = temp.next_element
        temp.next_element.previous_element = temp.previous_element
```

**Time Complexity:** O(n)

---

## Traversal and Printing

**Concept:**

* Start from head and move forward

```python
def print_list(self):
    temp = self.head_node
    while temp is not None:
        print(f"{temp.data} <->", end=" ")
        temp = temp.next_element
    print("NULL")
```

**Time Complexity:** O(n)

---

## Search Operation

**Concept:**

* Traverse forward
* Return True if node contains key

```python
def search(self, key):
    temp = self.head_node
    while temp is not None:
        if temp.data == key:
            return True
        temp = temp.next_element
    return False
```

**Time Complexity:** O(n)

---
