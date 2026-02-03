---
title: "Circular Linked Lists in Python: Implementation Guide"
slug: "circular-linked-lists-python"
date: 2024-10-23
description: "Master Circular Linked Lists in Python. Learn the ring topology, circular traversal, and edge cases for insertion/deletion."
showToc: true
weight: 4
series: ["DSA"]
categories: ["DSA", "Python"]
tags: ["Linked List", "Circular Linked List", "Data Structures", "Python"]
summary: "Implementing Circular Linked Lists in Python: handling the circular reference and avoiding infinite loops."
images: ["/images/cll.png"]
---

A **Circular Linked List (CLL)** is a linked list where the **last node points back to the first node (head)** instead of pointing to `NULL`.  
This forms a circular structure, allowing continuous traversal.

Circular linked lists are commonly used in:
- Round-robin CPU scheduling
- Games (turn rotation)
- Circular queues
- Memory management

---

## Structure of a Circular Linked List

A circular linked list contains:

- **Head** → Reference to the first node
- **Nodes** → Each node contains:
  - Data
  - Pointer to the next node
- **Last node** → Points back to the head

[Data | Next] -> [Data | Next] -> [Data | Next]  
^______________________________________________|

---

## Key Characteristics

- No `NULL` pointer
- Last node points to the head
- Traversal stops when head is reached again
- Traversal can start from any node

---

## Core Components of CLL

To implement a Circular Linked List, we need:
1. Node
2. CircularLinkedList

---

## 1. Node Class

### Concept

Each node stores data and a reference to the next node.

### Node Implementation

```python
class Node:
    def __init__(self, data):
        self.data = data
        self.next = None
```

---

## 2. CircularLinkedList Class

### Concept

- Maintains the head node
- Manages operations like insertion, deletion, traversal, and search

### Initialization

```python
class CircularLinkedList:
    def __init__(self):
        self.head = None
```

---

## Helper Function

### is_empty()

**Concept:**  
Checks whether the circular linked list is empty.

```python
def is_empty(self):
    return self.head is None
```

**Time Complexity:** O(1)

---

### get_head()
**Concept:**
Returns the head node of the circular linked list.

```python
def get_head(self):
    return self.head
```
**Time Complexity:** O(1)
---

## Insertion Operations

### Insert at Head

**Concept:**
- New node becomes head
- Last node updates its next pointer
- New node points to old head

```python
def insert_at_head(self, data):
    node = Node(data)

    if self.is_empty():
        self.head = node
        node.next = self.head
        return

    temp = self.head
    while temp.next != self.head:
        temp = temp.next

    node.next = self.head
    temp.next = node
    self.head = node
```

**Time Complexity:** O(n)

---

### Insert at End

**Concept:**
- Traverse to last node
- Link last node to new node
- New node points to head

```python
def insert_at_end(self, data):
    node = Node(data)

    if self.is_empty():
        self.head = node
        node.next = self.head
        return

    temp = self.head
    while temp.next != self.head:
        temp = temp.next

    temp.next = node
    node.next = self.head
```

**Time Complexity:** O(n)

---

### Insert After a Given Node
**Concept:**
- Find the target node
- Link new node after target
- Update pointers accordingly
```python
def insert_after_node(self,k,data):
    if self.is_empty():
        return
    
    if k==1:
        self.insert_at_head(data)
        return
    temp = self.head
    position = 1
    node = Node(data)
    while position < k-1 :
        temp = temp.next
        position += 1
    if temp.next == self.head:
        temp.next = node
        node.next = self.head
    else:
        node.next = temp.next
        temp.next = node
    
    
```




## Deletion Operation

### Delete at Front
**Concept:**
- Head node is removed
- Last node updates its next pointer to new head
- If only one node, head becomes None
```python
def delete_at_front(self):
    if self.is_empty():
        print("List is empty")
        return

    if self.head.next == self.head:  # Only one node
        self.head = None
        return

    temp = self.head
    last = self.head
    while last.next != self.head:
        last = last.next

    self.head = self.head.next
    last.next = self.head
```

**Time Complexity:** O(n)

---
### Delete at End
**Concept:**
- Traverse to second-last node
- Update its next pointer to head
- Remove last node
```python
def delete_at_back(self):
    if self.is_empty():
        print("List is empty")
        return

    if self.head.next == self.head:  # Only one node
        self.head = None
        return

    temp = self.head
    while temp.next.next != self.head:
        temp = temp.next

    temp.next = self.head
```

**Time Complexity:** O(n)

---

### Delete kth Node
Concept:
- Traverse to k-1 node
- Update its next pointer to skip k-th node
- Handle head deletion separately
```python
def delete_k_node(self, k):
    if self.is_empty():
        print("List is empty")
        return

    if k == 1:
        self.delete_at_front()
        return

    temp = self.head
    count = 1

    # Traverse to (k-1)-th node safely
    while count < k-1 :
        temp = temp.next
        count += 1


    # Delete k-th node
    if temp.next.next == self.head:  # If deleting last node
        temp.next = self.head
    else:
        temp.next = temp.next.next

```


## Traversal and Printing

**Concept:**
- Start from head
- Stop when head is reached again

```python
def print_list(self):
    if self.is_empty():
        print("List is empty")
        return

    temp = self.head
    while True:
        print(f"{temp.data} ->", end=" ")
        temp = temp.next
        if temp == self.head:
            break
    print("(HEAD)")
```

**Time Complexity:** O(n)

---

## Search Operation

**Concept:**
- Traverse circularly
- Stop when head is reached again

```python
def search(self, key):
    if self.is_empty():
        return False

    temp = self.head
    while True:
        if temp.data == key:
            return True
        temp = temp.next
        if temp == self.head:
            break
    return False
```

**Time Complexity:** O(n)

---
> Note: We can also implement circular linked lists using doubly linked list structure by adding a previous pointer to each node.
