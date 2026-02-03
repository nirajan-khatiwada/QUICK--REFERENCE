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
### 1. Stack Using Core C Concepts
```python
class Stack:
    def __init__(self, size):
        self.size = size
        self.stack = [None] * size
        self.top = -1

    def push(self, value):
        if self.top == self.size - 1:
            raise Exception("Stack Overflow")
        self.top += 1
        self.stack[self.top] = value

    def pop(self):
        if self.top == -1:
            raise Exception("Stack Underflow")
        value = self.stack[self.top]
        self.stack[self.top] = None   # optional cleanup
        self.top -= 1
        return value

    def peek(self):
        if self.top == -1:
            raise Exception("Stack is Empty")
        return self.stack[self.top]

stack = Stack(5)
stack.push(10)
print(stack.peek())  # Output: 10
stack.push(20)
print(stack.pop())   # Output: 20
```

### 2. Stack Using Python List

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

### 3. Stack Using Singly Linked List

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
### 1. Queue Using Core C Concepts
```python
class Queue:
    def __init__(self, size):
        self.size = size
        self.queue = [None] * size
        self.front = 0
        self.rear = -1

    def enqueue(self,value):
        if self.rear == self.size-1:
            raise Exception("Queue Overflow")
        else:
            self.rear += 1
            self.queue[self.rear] = value
    def dequeue(self):
        if self.front > self.rear:
            raise Exception("Queue Underflow")
        else:
            value = self.queue[self.front]
            self.queue[self.front] = None  # optional cleanup
            self.front += 1
            return value
queue = Queue(5)
queue.enqueue(10)
print(queue.dequeue())  # Output: 10
```


### 2. Queue Using Python List

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
### 1. Priority Queue Using Core C Concepts

```python
class PriorityQueue:
    def __init__(self, size):
        self.size = size
        self.queue = [None] * size
        self.rear = -1
        self.front = 0
    def enqueue(self, value):
        if self.rear == self.size - 1:
            raise Exception("Priority Queue Overflow")
        else:
            # put value at its correct position
            for i in range(self.front,self.rear+1):
                if self.queue[i] < value:
                    # shift elements to right
                    for j in range(self.rear,i-1,-1):
                        self.queue[j+1] = self.queue[j]
                    self.queue[i] = value
                    self.rear += 1
                    return
            self.rear += 1
            self.queue[self.rear] = value
    def dequeue(self):
        if self.front > self.rear:
            raise Exception("Priority Queue Underflow")
        else:
            value = self.queue[self.front]
            self.queue[self.front] = None  # optional cleanup
            self.front += 1
            return value
queue = PriorityQueue(5)
queue.enqueue(20)
queue.enqueue(10)
print(queue.dequeue())  # Output: 20

```


### 2. Priority Queue Using List (Max Priority)

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

### 3. Priority Queue Using Linked List

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

> Note: Learn Circular Queue for efficient memory usage in fixed-size queues.We won't cover it here.