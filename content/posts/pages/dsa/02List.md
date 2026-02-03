List is a linear data structure that stores elements in a sequential manner .

# Implementation Lists in Python
We can implement a list in Python using 3 main ways:
1. Using Array Based Implementation  
2. Using Python Inbuilt List  
3. Using Linked List Based Implementation  

## Using Array Based Implementation
```python
class List:
    def __init__(self, size):
        self.array = [None] * size
        self.length = 0
        self.size = size

    # Insert at end
    def insert_at_end(self, x):
        if self.length == self.size:
            return "List is Full"
        self.array[self.length] = x
        self.length += 1

    # Delete at end
    def delete_at_end(self):
        if self.length == 0:
            return "List is Empty"
        deleted = self.array[self.length - 1]
        self.array[self.length - 1] = None
        self.length -= 1
        return deleted

    # Insert at beginning
    def insert_at_beg(self, x):
        if self.length == self.size:
            return "List is Full"
        for i in range(self.length, 0, -1):
            self.array[i] = self.array[i - 1]
        self.array[0] = x
        self.length += 1

    # Delete at beginning
    def delete_at_beg(self):
        if self.length == 0:
            return "List is Empty"
        deleted = self.array[0]
        for i in range(0, self.length - 1):
            self.array[i] = self.array[i + 1]
        self.array[self.length - 1] = None
        self.length -= 1
        return deleted

    # Insert at position k (0-based index)
    def insert_at_k(self, position, x):
        if self.length == self.size:
            return "List is Full"
        for i in range(self.length, position, -1):
            self.array[i] = self.array[i - 1]
        self.array[position] = x
        self.length += 1

    # Delete at position k (0-based index)
    def delete_at_k(self, position):
        if self.length == 0:
            return "List is Empty"
        deleted = self.array[position]
        for i in range(position, self.length - 1):
            self.array[i] = self.array[i + 1]
        self.array[self.length - 1] = None
        self.length -= 1
        return deleted

    # Traverse list
    def traverse(self):
        for i in range(self.length):
            print(self.array[i])

    # Merge with another array
    def merge(self, other_array, other_length):
        new_array = [None] * (self.length + other_length)

        for i in range(other_length):
            new_array[i] = other_array[i]

        for i in range(self.length):
            new_array[other_length + i] = self.array[i]

        return new_array
```


## Using Python Inbuilt List
```python
class List:
    def __init__(self, size):
        self.lists= []

    # Insert at end
    def insert_at_end(self, x):
        self.lists.append(x)

    # Delete at end
    def delete_at_end(self):
        return self.lists.pop()

    # Insert at beginning
    def insert_at_beg(self, x):
        self.lists.insert(0,x)

    # Delete at beginning
    def delete_at_beg(self):
        self.lists.pop(0)

    # Insert at position k (0-based index)
    def insert_at_k(self, position, x):
        self.lists.insert(position,x)

    # Delete at position k (0-based index)
    def delete_at_k(self, position):
        self.lists.pop(position)

    # Traverse list
    def traverse(self):
        print(self.lists)

    # Merge with another array
    def merge(self, other_array):
        new_array = other_array + self.lists
        return new_array
```


## Using Linked List Based Implementation
There are 3 common ways to implement linked lists:
- Singly Linked List
- Doubly Linked List
- Circular Linked List
We will cover these implementations in the next files.