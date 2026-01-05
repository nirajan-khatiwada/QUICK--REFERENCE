---
title: "Stack and Queue in Data Structures: Implementation & Operations"
slug: "stack-and-queue-data-structures"
date: 2025-12-29
description: "Comprehensive guide to understanding and implementing Stack and Queue data structures using Python lists and linked lists."
showToc: true
weight: 6
series: ["DSA"]
categories: ["DSA", "Python"]
tags: ["Stack", "Queue", "Priority Queue", "Data Structures", "Python", "Linked List"]
summary: "Detailed implementation and explanation of Stack and Queue data structures, including linked list and array-based approaches, with priority queue variations."
images: ["/images/stack-queue.png"]
---

# Introduction to Stack and Queue

**Stack** and **Queue** are fundamental linear data structures widely used in computer science for managing ordered data efficiently.

- A **Stack** follows **LIFO** (Last In First Out) principle — the last element inserted is the first one to be removed.
- A **Queue** follows **FIFO** (First In First Out) principle — the first element inserted is the first one to be removed.

These structures form the basis for more complex algorithms and systems such as parsing, scheduling, and resource management.

---

## Stack Data Structure

### Overview

- Elements are inserted and removed only from the **top** of the stack.
- Supports three primary operations: `push` (insert), `pop` (remove), and `peek` (view top element).
- Commonly used for function call management, undo mechanisms, expression evaluation, etc.

### Implementation Approaches

---

### 1. Stack Using Python List

Python’s list structure can be used as a stack, where:

- `append()` adds an element to the top.
- `pop()` removes the element from the top.

```python
class Stack:
    def __init__(self):
        self.values = []

    def push(self, x):
        self.values.append(x)

    def pop(self):
        if not self.is_empty():
            return self.values.pop()
        return None

    def is_empty(self):
        return len(self.values) == 0

    def peek(self):
        if not self.is_empty():
            return self.values[-1]
        return None
```

**Time Complexity:**

| Operation | Time Complexity |
|-----------|-----------------|
| push      | O(1)            |
| pop       | O(1)            |
| peek      | O(1)            |
| is_empty  | O(1)            |

---

### 2. Stack Using Singly Linked List

A custom linked list can efficiently implement a stack by pushing and popping from the head.

```python
class Node:
    def __init__(self, x):
        self.data = x
        self.next_node = None

class Stack:
    def __init__(self):
        self.top = None

    def is_empty(self):
        return self.top is None

    def push(self, x):
        node = Node(x)
        node.next_node = self.top
        self.top = node

    def pop(self):
        if self.is_empty():
            return None
        popped_data = self.top.data
        self.top = self.top.next_node
        return popped_data

    def peek(self):
        if not self.is_empty():
            return self.top.data
        return None
```

**Time Complexity:**

| Operation | Time Complexity |
|-----------|-----------------|
| push      | O(1)            |
| pop       | O(1)            |
| peek      | O(1)            |
| is_empty  | O(1)            |

---

## Queue Data Structure

### Overview

- Elements are inserted at the rear and removed from the front.
- Supports enqueue (insert at rear), dequeue (remove from front), get_front, and get_rear.
- Widely used in scheduling, buffering, and breadth-first search (BFS).

---

### 1. Queue Using Python List

```python
class Queue:
    def __init__(self):
        self.values = []

    def is_empty(self):
        return len(self.values) == 0

    def enqueue(self, x):
        self.values.append(x)

    def dequeue(self):
        if self.is_empty():
            return None
        return self.values.pop(0)  # O(n) operation

    def get_front(self):
        if self.is_empty():
            return None
        return self.values[0]

    def get_rear(self):
        if self.is_empty():
            return None
        return self.values[-1]
```

**Note:** `pop(0)` is O(n) in Python lists. For efficient queue, use `collections.deque`.

---

### 2. Queue Using Linked List

Efficient FIFO operations with O(1) enqueue and dequeue using front and rear pointers.

```python
class Node:
    def __init__(self, data):
        self.data = data
        self.next_element = None

class Queue:
    def __init__(self):
        self.front = None
        self.rear = None

    def is_empty(self):
        return self.front is None and self.rear is None

    def enqueue(self, x):
        node = Node(x)
        if self.is_empty():
            self.front = node
            self.rear = node
        else:
            self.rear.next_element = node
            self.rear = node

    def dequeue(self):
        if self.is_empty():
            return None
        dequeued_data = self.front.data
        if self.front.next_element is None:
            self.front = None
            self.rear = None
        else:
            self.front = self.front.next_element
        return dequeued_data

    def get_front(self):
        if self.is_empty():
            return None
        return self.front.data

    def get_rear(self):
        if self.is_empty():
            return None
        return self.rear.data
```

**Time Complexity:**

| Operation  | Time Complexity |
|------------|-----------------|
| enqueue    | O(1)            |
| dequeue    | O(1)            |
| get_front  | O(1)            |
| get_rear   | O(1)            |

---

## Priority Queue

### Overview

- A Priority Queue orders elements by priority rather than insertion order.
- Higher priority elements dequeued first.
- Implemented using heaps or sorted linked lists/arrays.

---

### 1. Priority Queue Using List (Max Priority)

```python
class PriorityQueue:
    def __init__(self):
        self.values = []

    def is_empty(self):
        return len(self.values) == 0

    def enqueue(self, x):
        if self.is_empty():
            self.values.append(x)
            return
        for i in range(len(self.values)):
            if self.values[i] < x:
                self.values.insert(i, x)
                return
        self.values.append(x)

    def dequeue(self):
        if self.is_empty():
            return None
        return self.values.pop(0)  # highest priority is at front
```

**Time Complexity:**

| Operation | Time Complexity |
|-----------|-----------------|
| enqueue   | O(n)            |
| dequeue   | O(1)            |

---

### 2. Priority Queue Using Linked List

Sorted insertion maintains priority order.

```python
class Node:
    def __init__(self, x):
        self.data = x
        self.next_element = None

class PriorityQueue:
    def __init__(self):
        self.front = None
        self.rear = None

    def is_empty(self):
        return self.front is None and self.rear is None

    def dequeue(self):
        if self.is_empty():
            return None
        dequeued_data = self.front.data
        if self.front.next_element is None:
            self.front = None
            self.rear = None
        else:
            self.front = self.front.next_element
        return dequeued_data

    def enqueue(self, x):
        node = Node(x)
        if self.is_empty():
            self.front = node
            self.rear = node
            return
        if x > self.front.data:
            node.next_element = self.front
            self.front = node
        else:
            temp = self.front
            while temp.next_element and temp.next_element.data > x:
                temp = temp.next_element
            node.next_element = temp.next_element
            temp.next_element = node
            if node.next_element is None:
                self.rear = node
```
