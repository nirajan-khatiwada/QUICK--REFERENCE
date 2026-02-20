---
title: "Python OOP Guide: Classes, Objects & Inheritance"
slug: "python-oop-concepts"
date: 2024-11-10
description: "Master Object-Oriented Programming (OOP) in Python. Learn to create classes, objects, and use inheritance, encapsulation, and polymorphism effectively."
showToc: true
weight: 5
series: ["Python"]
categories: ["Python"]
tags: ["Python", "Programming", "OOP", "Classes", "Objects", "Inheritance", "Tutorial"]
summary: "A comprehensive introduction to Object-Oriented Programming in Python covering classes, objects, inheritance, method resolution, access specifiers, and advanced OOP concepts."
images: ["/images/python.png"]
---

## Object Oriented Programming in Python

Object oriented programming is a programming paradigm that provides a means of structuring programs so that properties and behaviors are bundled into individual objects. This approach makes code more modular, reusable, and easier to maintain, especially for complex systems.

## Understanding Python's Object Storage: The __dict__ Concept

Before diving into classes and objects, it's crucial to understand how Python stores object data internally.

### The __dict__ Dictionary
Every Python object has a special attribute called `__dict__` that stores all the object's attributes as key-value pairs in a dictionary. This is fundamental to understanding how Python objects work.

```python
# Even a simple object has a __dict__
class SimpleClass:
    pass

obj = SimpleClass()
print(obj.__dict__)  # Output: {} (empty dictionary initially)

# When we add attributes, they go into __dict__
obj.name = "Alice"
obj.age = 25
print(obj.__dict__)  # Output: {'name': 'Alice', 'age': 25}
```

### Key Points About __dict__:
- **Single Storage Location**: Each object instance has exactly ONE `__dict__` that stores all its attributes
- **Dynamic Creation**: Attributes are created when first assigned
- **Unified Storage**: All methods (from base class, derived class) modify the same `__dict__`
- **Dictionary Access**: You can directly access and modify `__dict__` like any dictionary

## 1. Classes and Objects: The Foundation

### 1.1 What is a Class?
A class is a blueprint for creating objects (a particular data structure), providing initial values for state (member variables or attributes), and implementations of behavior (member functions or methods). Think of a class as a template that defines what an object will look like and how it will behave.

### 1.2 What is an Object?
An object is an instance of a class. When a class is defined, only the description for the object is defined. Therefore, no memory or storage is allocated. Memory is allocated only when an object is created. Each object has its own copy of the attributes defined in the class.

### 1.3 Creating a Class
We can create a class using the `class` keyword followed by the class name. The class definition can contain class variables, instance variables, methods, and constructors.

**Syntax:**
```python
class ClassName:
    # code block
```

**Example:**
```python
class Person:
    def __init__(self):
        self.name = "Nirajan"  # attribute
        self.age = 20          # attribute
        self.classes = "Bachelor"  # attribute
```

### 1.4 Creating an Object
To create an object of a class, we use the class name followed by parentheses. This calls the constructor method of the class and returns an object.

**Syntax:**
```python
object_name = ClassName()
```

**Example:**
```python
person1 = Person()  # Creates an instance of Person class
```

## 2. Types of Attributes

There are two kinds of attributes in a class: instance attributes and class attributes.

### 2.1 Instance Attributes
Instance attributes are specific to each object of the class. They are defined using the `self` keyword inside the constructor or other methods. Each object has its own copy of instance attributes.

**Example:**
```python
class Person:
    def __init__(self, name, age):
        self.name = name  # Instance attribute
        self.age = age    # Instance attribute

person1 = Person("Alice", 30)
person2 = Person("Bob", 25)
print(person1.name)  # Output: Alice
print(person2.name)  # Output: Bob
```

### 2.2 Class Attributes
Class attributes are shared across all instances of the class. They are defined within the class but outside any methods. All objects of the class share the same copy of class attributes.

**Example:**
```python
class Person:
    Company = "ABC Corp"  # Class attribute

    def __init__(self, name, age):
        self.name = name  # Instance attribute
        self.age = age    # Instance attribute

    def display(self):
        print(f"Name: {self.name}, Age: {self.age}, Company: {self.Company}")

person1 = Person("Alice", 30)
person2 = Person("Bob", 25)
print(person1.Company)  # Output: ABC Corp
print(person2.Company)  # Output: ABC Corp
print(person1.display())  # Output: Name: Alice, Age: 30, Company: ABC Corp
print(person2.display())  # Output: Name: Bob, Age: 25, Company: ABC Corp
```

**Note:** Instance attributes have higher priority than class attributes. If a class has both an instance attribute and a class attribute with the same name, the instance attribute will be accessed when accessing it through an object.

### 2.3 @classmethod Decorator
Class methods are used to change class attribute values such that it will be reflected in all objects of the class. Class methods take `cls` as the first parameter, which refers to the class itself.

**Syntax:**
```python
class ClassName:
    @classmethod
    def method_name(cls, parameters):
        # code block
```

**Example:**
```python
class Person:
    Company = "ABC Corp"  # Class attribute

    def __init__(self, name, age):
        self.name = name  # Instance attribute
        self.age = age    # Instance attribute

    @classmethod
    def change_company(cls, new_company):
        cls.Company = new_company  # Modify class attribute

person1 = Person("Alice", 30)
person2 = Person("Bob", 25)
print(person1.Company)  # Output: ABC Corp
print(person2.Company)  # Output: ABC Corp
Person.change_company("XYZ Inc")  # Change class attribute using class method
print(person1.Company)  # Output: XYZ Inc
print(person2.Company)  # Output: XYZ Inc
```

### 2.4 Ambiguity in Class and Instance Attributes
Consider the following example:
```python
class Person:
    Company = "ABC Corp"  # Class attribute

    def __init__(self, name, age):
        self.name = name  # Instance attribute
        self.age = age    # Instance attribute

    def change_company(self, new_company):
        self.Company = new_company  # This creates an instance attribute

    def display(self):
        print(f"Name: {self.name}, Age: {self.age}, Company: {self.Company}")

person1 = Person("Alice", 30)
person2 = Person("Bob", 25)
person1.display()  # Output: Name: Alice, Age: 30, Company: ABC Corp
person2.display()  # Output: Name: Bob, Age: 25, Company: ABC Corp
person1.change_company("XYZ Inc")  # Creates an instance attribute for person1
person1.display()  # Output: Name: Alice, Age: 30, Company: XYZ Inc
person2.display()  # Output: Name: Bob, Age: 25, Company: ABC Corp
```

**Note:** In this example, when we call `person1.change_company("XYZ Inc")`, it creates an instance attribute `Company` for `person1` since we know that the priority of instance attributes is higher than class attributes. So when we access `self.Company` inside the `display()` method for `person1`, it refers to the instance attribute, not the class attribute. However, `person2` still refers to the class attribute since it doesn't have its own instance attribute named `Company`.

**Note:** To change a class attribute, we should always use a `@classmethod`; otherwise, it will create an instance attribute with the same name as the class attribute if we try to modify the class using `self.attribute_name = new_value` inside a method or `object.attribute_name = new_value` outside a method.

## 3. Methods: Adding Behavior to Classes

### 3.1 Creating a Member Function
We can create a member function (method) inside a class using the `def` keyword followed by the function name. The first parameter of the method should be `self`, which refers to the current instance of the class.

**Syntax:**
```python
class ClassName:
    def method_name(self, parameters):
        # code block
```

**Example:**
```python
class Person:
    name = "Nirajan"
    age = 20
    classes = "Bachelor"

    def display(self):  # Method to display person information
        print(f"Name: {self.name}")
        print(f"Age: {self.age}")
        print(f"Class: {self.classes}")
```

### 3.2 The self Parameter
The `self` parameter is a reference to the current instance of a class. It is used to access variables and methods that belong to that specific object. Additionally, `self` allows the creation of instance attributes, which are unique to each object of the class and can be accessed by any member function within the class.

**Example:**
```python
class Person:
    def __init__(self):
        self.name = "Nirajan"
        self.age = 20
        self.classes = "Bachelor"

    def display(self):
        print(f"Name: {self.name}")  # Accessing attribute with self
        print(f"Age: {self.age}")    # Accessing attribute with self
        print(f"Class: {self.classes}")  # Accessing attribute with self
        self.greet()  # Calling another method with self

    def greet(self):
        print("Hello, Welcome to the class")
```

### 3.3 Calling a Member Function
We can call a member function of a class using the dot operator (.) followed by the function name and parentheses.

**Syntax:**
```python
object_name.method_name(arguments)
```

**Example:**
```python
person1.display()  # Calls the display method for person1 object
```

### 3.4 Nested Member Function
We can call a member function from another member function of the same class using the `self` keyword.

**Syntax:**
```python
class ClassName:
    def method1(self):
        # code block
        self.method2()  # Calling method2 from method1

    def method2(self):
        # code block
```

**Example:**
```python
class Person:
    def __init__(self):
        self.name = "Nirajan"
        self.age = 20
        self.classes = "Bachelor"

    def display(self):
        print(f"Name: {self.name}")
        print(f"Age: {self.age}")
        print(f"Class: {self.classes}")
        self.greet()  # Calling the greet method from display method

    def greet(self):
        print("Hello, Welcome to the class")
```

## 4. Constructor: Initializing Objects

A constructor is a special type of method (function) which is used to initialize the instance members of the class. It is called when an object of the class is created. This allows you to set up each object with its specific initial state.

### 4.1 Creating a Constructor
In Python, the constructor method is called `__init__`. It is a special method that is automatically called when an object is created.

**Syntax:**
```python
class ClassName:
    def __init__(self, parameters):
        # code block
```

**Example:**
```python
class Person:
    def __init__(self, name, age, classes):
        # Initialize instance attributes with provided values
        self.name = name        # Instance attribute
        self.age = age          # Instance attribute
        self.classes = classes  # Instance attribute

    def display(self):
        print(f"Name: {self.name}")
        print(f"Age: {self.age}")
        print(f"Class: {self.classes}")
```

### 4.2 Creating an Object with Constructor
When an object is created, the constructor method is automatically called with the arguments passed to the class.

**Syntax:**
```python
object_name = ClassName(arguments)
```

**Example:**
```python
# Creating a Person object with name, age, and classes values
person1 = Person("Nirajan", 20, "Bachelor")
```

In this example, the `__init__` method is called with "Nirajan", 20, and "Bachelor" as arguments, which initializes the object's attributes.

## 5. Access Specifiers in Python

Access specifiers control the visibility and accessibility of class members (attributes and methods). Understanding access control is important for implementing encapsulation - one of the four pillars of OOP.

### 5.1 Public Members
Public members are accessible from both inside and outside the class. By default, all members in Python are public unless specified otherwise.

**Example:**
```python
class Person:
    def __init__(self):
        self.name = "Alice"  # Public member

    def display(self):      # Public method
        print(f"Name: {self.name}")

person1 = Person()
print(person1.name)  # Output: Alice
person1.display()    # Output: Name: Alice
```

### 5.2 Protected Members
Protected members are accessible within the class and its subclasses. They are denoted by a single underscore (_) before the member name.

**Example:**
```python
class Person:
    def __init__(self):
        self._name = "Alice"  # Protected member

    def _display(self):      # Protected method
        print(f"Name: {self._name}")

class Student(Person):
    def show(self):
        self._display()  # Accessing protected method from subclass
        print(f"Accessing protected member: {self._name}")

student1 = Student()
student1.show()
```

```
Name: Alice
Accessing protected member: Alice
```

**Explanation:** Here, the `_name` and `_display()` are protected members of the Person class. They can be accessed within the Person class and also in the Student subclass. However, they should not be accessed directly from outside the class or subclass.

> **Note:** Accessing a protected member using `object._member_name` from outside the class is possible in Python but not recommended, as it violates the principle of encapsulation. However, it is perfectly valid to access protected members within the class itself or any of its subclasses.

### 5.3 Private Members
Private members are accessible only within the class. They cannot be accessed from outside the class or even in subclasses. They are denoted by a double underscore (__) before the member name.

**Example:**
```python
class Person:
    __city = "New York"  # Private member

person1 = Person()
print(person1.__city)  # Error: 'Person' object has no attribute '__city'
```

**Example of private variable and method:**
```python
class Person:
    def __init__(self):
        self.__name = "Alice"  # Private member

    def __display(self):  # Private method
        print(f"Name: {self.__name}")
    
    def show(self):
        self.__display()  # Accessing private method within the class
        print(f"Accessing private member: {self.__name}")  # Accessing private member within the class

person1 = Person()
print(person1.__name)  # Error: 'Person' object has no attribute '__name'
person1.__display()  # Error: 'Person' object has no attribute '__display'
person1.show()  # Correct way to access private members within the class as show() is public
```

**Table of Access Specifiers in Python:**

| Access Specifier | Accessible from class | Accessible from subclass | Accessible from outside class |
|------------------|----------------------|--------------------------|-------------------------------|
| Public           | Yes                  | Yes                      | Yes                           |
| Protected        | Yes                  | Yes                      | No                            |
| Private          | Yes                  | No                       | No                            |

## 6. Inheritance in Python

Inheritance is a mechanism in which one class acquires the properties and behavior of another class. The class which inherits the properties and behavior is known as the child class, and the class whose properties and behavior are inherited is known as the parent class.

### 6.1 Creating a Child Class
To create a child class that inherits from a parent class, we specify the parent class in parentheses after the child class name.

**Syntax:**
```python
class ChildClassName(ParentClassName):
    # code block
```

**Example:**
```python
class Employee:
    def set_name(self, name):
        self.name = name
    def display_name(self):
        print(f"Name: {self.name}")

class TypeEmployee(Employee):  # Child class inheriting from Employee
    def set_salary(self, salary):
        self.salary = salary
    def display_salary(self):
        print(f"Salary: {self.salary}")

type_emp1 = TypeEmployee()
type_emp1.set_name("Nirajan")  # Inherited method from Employee
type_emp1.set_salary(50000)     # Method from TypeEmployee
type_emp1.display_name()         # Inherited method from Employee
type_emp1.display_salary()       # Method from TypeEmployee
```

**Note:** Only public and protected members are inherited by the child class. Private members are not inherited by the child class.

> **Note:** In the above example, the TypeEmployee class is the child class that inherits from the Employee class, which means all the public and protected members of the Employee class are available in the TypeEmployee class. Such that the new type class looks like:
> 
> **Attributes:**
> - Name
> - Salary
> 
> **Methods:**
> - set_name()
> - display_name()
> - set_salary()
> - display_salary()

### 6.2 Method Overriding
Method overriding is a feature of object-oriented programming that allows a subclass to provide a specific implementation of a method that is already provided by its parent class. When a method in a subclass has the same name as a method in the parent class, the method in the subclass overrides the method in the parent class.

**Example:**
```python
class Person:
    def display(self):
        print("Person class")

class Student(Person):
    def display(self):  # This overrides Person's display method
        print("Student class")

student1 = Student()
student1.display()  # Calls Student's display method, not Person's
```

We can also call the parent class's method from the overridden method using the `super()` function.

### 6.3 The super() Method in Python
When both a parent class and a child class define a method with the same name, creating an object of the child class and calling that method will execute the version defined in the child class, not the one in the parent class.

To explicitly invoke the parent class's version of the method, we use the `super()` function. This is especially useful when the child class method needs to extend or build upon the functionality of the parent class method.

**Syntax:**
```python
super().method_name()
```

**Example:**
```python
class Person:
    def display(self):
        print("Person class")
    
class Student(Person):
    def display(self):
        super().display()  # Calls the parent class's display method
        Person.display(self)  # Another way to call parent class method
        print("Student class")

student1 = Student()
student1.display()
```

```
# Output:
Person class
Student class
```

**Use case:**
Consider the following implementation:
```python
class Employee:
    def __init__(self, name, age):
        self.name = name
        self.age = age
    def display_employee(self):
        print(f"Name: {self.name}, Age: {self.age}")

class TypeEmployee(Employee):
    def __init__(self, name, age, salary):
        self.age = age
        self.salary = salary
    def display_specific_employee(self):
        print(f"Salary: {self.salary}")

type_emp1 = TypeEmployee("Nirajan", 25, 50000)
type_emp1.display_employee()  # Error: 'TypeEmployee' object has no attribute 'display_employee'
```

In this example, the `__init__` method of the TypeEmployee class overrides the `__init__` method of the Employee class. So during the creation of TypeEmployee object, the `__init__` method of Employee class is not called, and hence the name attribute is not initialized. When we try to call display_employee() method, it raises an error because the name attribute does not exist in the TypeEmployee object. To fix this issue we have two solutions:

- Copying the code of parent class's `__init__` method inside child class's `__init__` method
```python
class TypeEmployee(Employee):
    def __init__(self, name, age, salary):
        self.name = name  # Initializing name attribute
        self.age = age
        self.salary = salary
```

- Using `super()` to call the parent class's `__init__` method inside child class's `__init__` method
```python
class TypeEmployee(Employee):
    def __init__(self, name, age, salary):
        super().__init__(name, age)  # Calling parent class's __init__ method
        self.salary = salary
```

**Note:** When we use `super()`, it works based on MRO to resolve which class function to use, but `ClassName.functionName()` explicitly tells which class function to use.

### 6.4 Method Resolution Order (MRO)
Method Resolution Order (MRO) is the order in which Python looks for a method or attribute in an inheritance hierarchy. It determines the sequence in which base classes are searched when executing a method. MRO is especially important in multiple inheritance scenarios.

**Example:**
```python
class C:
    f = "dirajan"

class A(C):
    f = "nirajan"

class B(C):
    f = "kirajan"

class D(A, B):
    def display(self):
        print(self.f)

d = D()
print(D.__mro__)  # Shows the method resolution order
d.display()
```

**Output:**
```
(<class '__main__.D'>, <class '__main__.A'>, <class '__main__.B'>, <class '__main__.C'>, <class 'object'>)
nirajan
```

It means Python will search for the attribute or method in the order of D->A->B->C->object. If the attribute/method is not found in D, it will search in A, and so on. But if the attribute/method is found in D, it will not search further in A, B, C, or object.

### 6.5 Types of Inheritance in Python
Inheritance is a mechanism in which one class acquires the properties and behavior of another class. There are different types of inheritance in Python:

#### 6.5.1 Single Inheritance
In single inheritance, a class inherits from only one parent class.

The pictorial representation of single inheritance is:
```
    A
    |
    B
```

**Syntax:**
```python
class ParentClass:
    # code block

class ChildClass(ParentClass):
    # code block
```

**Example:**
```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age
    def display(self):
        return f"Name: {self.name}, Age: {self.age}"

class Student(Person):
    def __init__(self, name, age, id):
        super().__init__(name, age)  # or Person.__init__(self, name, age)
        self.id = id
    
    def display(self):
        print(f"ID: {self.id}", super().display())  # or Person.display(self)

student1 = Student("Alice", 30, 101)
student1.display()
```

**Output:**
```
ID: 101 Name: Alice, Age: 30
```

#### 6.5.2 Multiple Inheritance
In multiple inheritance, a class inherits from more than one parent class.

The pictorial representation of multiple inheritance is:
```
    A    B
     \  /
      C
```

**Syntax:**
```python
class ParentClass1:
    # code block

class ParentClass2:
    # code block

class ChildClass(ParentClass1, ParentClass2):
    # code block
```

**Example:**
```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def display(self):
        return f"Name: {self.name}, Age: {self.age}"

class Address:
    def __init__(self, city, state):
        self.city = city
        self.state = state
    
    def display(self):
        return f"City: {self.city}, State: {self.state}"

class Student(Person, Address):
    def __init__(self, name, age, id, city, state):
        Person.__init__(self, name, age)
        Address.__init__(self, city, state)
        self.id = id
    
    def display(self):
        print(f"ID: {self.id}", Person.display(self), Address.display(self))

student1 = Student("Alice", 30, 101, "New York", "New York")
student1.display()
```

**Output:**
```
ID: 101 Name: Alice, Age: 30 City: New York, State: New York
```

#### 6.5.3 Multilevel Inheritance
In multilevel inheritance, a class inherits from a parent class, and another class inherits from the child class.

The pictorial representation of multilevel inheritance is:
```
    A
    |
    B
    |
    C
```

**Syntax:**
```python
class ParentClass:
    # code block

class ChildClass(ParentClass):
    # code block

class GrandChildClass(ChildClass):
    # code block
```

**Example:**
```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def display(self):
        return f"Name: {self.name}, Age: {self.age}"

class Student(Person):
    def __init__(self, name, age, id):
        super().__init__(name, age)  # or Person.__init__(self, name, age)
        self.id = id
    
    def display(self):
        print(f"ID: {self.id}", super().display())  # or print(f"ID: {self.id}", Person.display(self))

class CollegeStudent(Student):
    def __init__(self, name, age, id, classes):
        super().__init__(name, age, id)  # or Student.__init__(self, name, age, id)
        self.classes = classes
    
    def display(self):
        print(f"Class: {self.classes}", super().display())  # or print(f"Class: {self.classes}", Student.display(self))

student1 = CollegeStudent("Alice", 30, 101, "Bachelor")
student1.display()
```

**Output:**
```
Class: Bachelor ID: 101 Name: Alice, Age: 30
```

#### 6.5.4 Hierarchical Inheritance
In hierarchical inheritance, more than one class inherits from a single parent class.

The pictorial representation of hierarchical inheritance is:
```
    A
   / \
  B   C
```

**Syntax:**
```python
class ParentClass:
    # code block

class ChildClass1(ParentClass):
    # code block

class ChildClass2(ParentClass):
    # code block
```

**Example:**
```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def display(self):
        return f"Name: {self.name}, Age: {self.age}"

class Student(Person):
    def __init__(self, name, age, id):
        super().__init__(name, age)  # or Person.__init__(self, name, age)
        self.id = id
    
    def display(self):
        print(f"ID: {self.id}", super().display())  # or print(f"ID: {self.id}", Person.display(self))

class Employee(Person):
    def __init__(self, name, age, emp_id):
        super().__init__(name, age)  # or Person.__init__(self, name, age)
        self.emp_id = emp_id
    
    def display(self):
        print(f"Emp ID: {self.emp_id}", super().display())  # or print(f"Emp ID: {self.emp_id}", Person.display(self))

student1 = Student("Alice", 30, 101)
student1.display()

employee1 = Employee("Bob", 25, 201)
employee1.display()
```

**Output:**
```
ID: 101 Name: Alice, Age: 30
Emp ID: 201 Name: Bob, Age: 25
```

**Note:** Hybrid inheritance is a combination of two or more types of inheritance.

> **Note:** For Multiple Inheritance and Multilevel inheritance, it uses MRO to resolve which class function to use when there is ambiguity.

## 7. Properties - Getters and Setters

Properties provide controlled access to class attributes. They allow you to implement getter and setter methods that act like attributes.

### 7.1 Getters
Getters are methods implemented using the `@property` decorator. They are specially used:
- To act as a value (data) instead of a method
- To access the value of a private attribute without directly accessing it

**Syntax:**
```python
class ClassName:
    @property
    def method_name(self):
        # code block
```

**Example:**
```python
class Person:
    def __init__(self, name, age):
        self._name = name  # Protected attribute
        self._age = age    # Protected attribute

    @property
    def display(self):
        return f"Name: {self._name}, Age: {self._age}"

person1 = Person("Nirajan", 20)
print(person1.display)  # Output: Name: Nirajan, Age: 20
# Note: No parentheses used - accessed like an attribute, not a method
```

### 7.2 Setters
Setters are methods implemented using the `@method_name.setter` decorator. They are specially used:
- To set the value of a private attribute without directly setting it
- To perform validation before setting the value of an attribute

**Syntax:**
```python
class ClassName:
    @method_name.setter
    def method_name(self, value):
        # code block
```

**Example:**
```python
class Person:
    def __init__(self, name, age):
        self._name = name
        self._age = age

    @property
    def display(self):
        return f"Name: {self._name}, Age: {self._age}"

    @display.setter
    def display(self, value):
        self._name, age_str = value.split(",")
        self._age = int(age_str)

person1 = Person("Nirajan", 20)
print(person1.display)  # Output: Name: Nirajan, Age: 20

person1.display = "Alice, 30"  # Using setter to modify attributes
print(person1.display)  # Output: Name: Alice, Age: 30
```

## 8. Special Methods (Magic/Dunder Methods)

Magic methods, also known as dunder methods (double underscore), are special methods that have double underscores at the beginning and end of their names. They are used to define the behavior of objects and are automatically called when certain operations are performed on objects.

Some of the commonly used magic methods are:

- `__init__`: Constructor method, called when an object is created
- `__str__`: Called by the str() built-in function to return a string representation of an object
- `__repr__`: Called by the repr() built-in function to return an unambiguous string representation of an object
- `__add__`: Called by the + operator to perform addition
- `__len__`: Called by the len() built-in function to return the length of an object
- `__call__`: Called when an object is called as a function

**Note:** All magic methods can be seen using `dir(object_name)` method, and we can override these methods in our class.

**Example of various magic methods:**
```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def __str__(self):
        return f"Name: {self.name}, Age: {self.age}"

    def __repr__(self):
        return f"Person('{self.name}', {self.age})"

    def __add__(self, other):
        return self.age + other.age

    def __len__(self):
        return len(self.name)
    
    def __call__(self):
        return f"Name: {self.name}, Age: {self.age}"

person1 = Person("Alice", 30)
person2 = Person("Bob", 25)

print(person1)  # Output: Name: Alice, Age: 30 (uses __str__)

print(repr(person1))  # Output: Person('Alice', 30) (uses __repr__)

print(person1 + person2)  # Output: 55 (uses __add__)

print(len(person1))  # Output: 5 (uses __len__ to get length of name)

print(person1())  # Output: Name: Alice, Age: 30 (uses __call__)
```

## 9. Operator Overloading

Operator overloading is a feature of object-oriented programming that allows us to define the behavior of operators for user-defined objects. It lets us define how operators like +, -, *, /, ==, !=, etc., behave with objects of our class.

To overload an operator, we define the corresponding magic method in the class:

**Example:**
```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __add__(self, other):
        # Define what happens when + is used with Point objects
        x = self.x + other.x
        y = self.y + other.y
        return Point(x, y)

    def __str__(self):
        return f"({self.x}, {self.y})"

point1 = Point(1, 2)
point2 = Point(3, 4)
point3 = point1 + point2  # Uses the __add__ method
print(point3)  # Output: (4, 6)
```

For unary operators like negation (-), we can overload the `__neg__` method:
```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __neg__(self):
        # Define what happens when - is used with Point objects
        return Point(-self.x, -self.y)

    def __str__(self):
        return f"({self.x}, {self.y})"

point1 = Point(1, 2)
point2 = -point1  # Uses the __neg__ method
print(point2)  # Output: (-1, -2)
```

**Note:** We can only overload existing operators. We cannot create new operators.

## 10. Static Method

Static methods are methods that can be called without creating an object of the class. They are defined using the `@staticmethod` decorator and can be called using the class name.

**Syntax:**
```python
class ClassName:
    @staticmethod
    def method_name(parameters):
        # code block
```

**Example:**
```python
class Calculator:
    @staticmethod
    def add(a, b):
        return a + b

result = Calculator.add(5, 3)  # Calling static method directly
print(result)  # Output: 8

# Can also call using an object (though not typical)
calculator = Calculator()
result = calculator.add(5, 3)
print(result)  # Output: 8
```

## 11. Introspection with dir() and __dict__

### 11.1 The dir() Method
The `dir()` method returns a list of attributes and methods of any object. It provides a way to introspect objects at runtime.

**Syntax:**
```python
dir(object)
```

**Example:**
```python
class Person:
    def __init__(self):
        self.name = "Alice"
        self.age = 30

person1 = Person()
print(dir(person1))
# Output: ['__class__', '__delattr__', '__dict__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__getstate__', '__gt__', '__hash__', '__init__', '__init_subclass__', '__le__', '__lt__', '__module__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', '__weakref__', 'age', 'name']
```

**Example with list:**
```python
l = [1, 2, 3]
print(dir(l))
# Output: ['__add__', '__class__', '__class_getitem__', '__contains__', '__delattr__', '__delitem__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__getitem__', '__getstate__', '__gt__', '__hash__', '__iadd__', '__imul__', '__init__', '__init_subclass__', '__iter__', '__le__', '__len__', '__lt__', '__mul__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__reversed__', '__rmul__', '__setattr__', '__setitem__', '__sizeof__', '__str__', '__subclasshook__', 'append', 'clear', 'copy', 'count', 'extend', 'index', 'insert', 'pop', 'remove', 'reverse', 'sort']
```

### 11.2 The __dict__ Method
The `__dict__` attribute contains a dictionary of an object's attributes. It provides another way to examine an object's state.

**Syntax:**
```python
object.__dict__
```

**Example:**
```python
class Person:
    def __init__(self):
        self.name = "Alice"
        self.age = 30

person1 = Person()
print(person1.__dict__)  # {'name': 'Alice', 'age': 30}

person1.name = "Bob"  # Set an instance attribute
print(person1.__dict__)  # {'name': 'Bob', 'age': 30}
```

## 12. How Python Works Internally

### 12.1 Understanding Instance Variable Storage

#### Key Concepts:
- **Single Object per Instance**: When you create a class instance, Python creates only ONE object
- **`__dict__` Storage**: All instance attributes are stored in the instance's `__dict__` dictionary
- **Dynamic Creation**: Attributes are created when first assigned, not when accessed
- **Direct Modification**: All attribute changes modify the same `__dict__`

#### Basic Example:
```python
class Person:
    def __init__(self, name):
        self.name = name  # Creates "name" key in __dict__

p = Person("Alice")
print(p.__dict__)  # {'name': 'Alice'}

p.age = 25         # Creates "age" key in __dict__
print(p.__dict__)  # {'name': 'Alice', 'age': 25}

p.name = "Bob"     # Modifies existing "name" key
print(p.__dict__)  # {'name': 'Bob', 'age': 25}
```

### 12.2 How Attribute Assignment and Access Works

#### Core Rules:
- **Assignment (self.attr = value)**: Always creates/modifies key in instance's `__dict__`
- **Access (self.attr)**: Checks instance `__dict__` first, then class hierarchy
- **Missing Attribute**: Accessing non-existent attribute raises `AttributeError`
- **Creating Attribute**: Assigning to non-existent attribute creates new `__dict__` entry

#### Example:
```python
class Demo:
    def __init__(self):
        self.x = 10

    def modify(self):
        self.x = self.x * 3    # Modifies existing key
        self.y = 100           # Creates new key

d = Demo()
print(d.__dict__)  # {'x': 10}

d.modify()
print(d.__dict__)  # {'x': 30, 'y': 100}

# This would raise AttributeError:
# print(d.z)  # AttributeError: 'Demo' object has no attribute 'z'

# This creates new attribute:
d.z = 50
print(d.__dict__)  # {'x': 30, 'y': 100, 'z': 50}
```

### 12.3 Inheritance: One Instance, One __dict__

#### Key Points:
- **Single `__dict__`**: Child class instance has only ONE `__dict__`
- **Shared Storage**: Parent and child methods modify the SAME `__dict__`
- **No Separate Variables**: Unlike C++, there's no separate storage for parent/child attributes

#### Example:
```python
class Parent:
    def __init__(self):
        self.name = "parent"

    def set_parent_attr(self):
        self.parent_var = "from parent"

class Child(Parent):
    def __init__(self):
        super().__init__()
        self.name = "child"        # Overwrites parent's name

    def set_child_attr(self):
        self.child_var = "from child"

c = Child()
print(c.__dict__)  # {'name': 'child'}

c.set_parent_attr()  # Parent method modifies child's __dict__
print(c.__dict__)    # {'name': 'child', 'parent_var': 'from parent'}

c.set_child_attr()
print(c.__dict__)    # {'name': 'child', 'parent_var': 'from parent', 'child_var': 'from child'}
```

### 12.4 Variable Access Levels: Public, Protected, Private

#### Concepts:
- **Public (self.var)**: Accessible everywhere
- **Protected (self._var)**: Convention for internal use (still accessible)
- **Private (self.__var)**: Name mangling applied, creates `_ClassName__var`

#### Public and Protected Variables
- Public variables (`name`) and protected variables (`_name`) are directly shareable between base and derived classes
- They are stored in the instance dictionary with their original names
- No name transformation occurs

#### Private Variables - Name Mangling Behavior

##### Instance Variable Storage
- When creating private instance variables (`__name`) in base and derived classes, Python applies name mangling
- All variables are stored in the **same single `__dict__`** for the instance
- However, private variables get *different keys* due to name mangling

##### Name Mangling Process
- **Base class A**: `__name` becomes `_A__name` in the dictionary
- **Derived class B**: `__name` becomes `_B__name` in the dictionary
- This creates unique keys for each class's private variables

##### Access Mechanism
- When accessing `self.__name` in class A → Python searches for `_A__name` in `__dict__`
- When accessing `self.__name` in class B → Python searches for `_B__name` in `__dict__`
- Same attribute name, different mangled keys, but accessed through the same class functions

#### Example:
```python
class AccessDemo:
    def __init__(self):
        self.public = "everyone can see"
        self._protected = "internal use"
        self.__private = "name mangled"

obj = AccessDemo()
print(obj.__dict__)
# {'public': 'everyone can see', '_protected': 'internal use', '_AccessDemo__private': 'name mangled'}

# Accessing:
print(obj.public)      # Works
print(obj._protected)  # Works (but shouldn't be used externally)
# print(obj.__private) # AttributeError
print(obj._AccessDemo__private)  # Works (name mangled form)
```

##### Dictionary Structure Example:
```python
instance.__dict__ = {
    'public_var': 'shared',           # Public - same key
    '_protected_var': 'shared',       # Protected - same key  
    '_A__private_var': 'base_value',  # Private in base class
    '_B__private_var': 'derived_value' # Private in derived class
}
```

### 12.5 Method Resolution Order (MRO) and Function Execution

#### Core Concepts:
- **MRO**: Python uses C3 linearization to determine method lookup order
- **Method Lookup**: When calling `self.method()`, Python searches MRO until it finds the method. It first searches function in 1st class of MRO if not found then second and so on
- **Attribute Access in Methods**: Even when executing parent method, `self` refers to the instance
- **Single `__dict__` Rule**: All methods modify the same instance `__dict__`

#### Single Inheritance Example:
```python
class A:
    def method(self):
        self.a_var = "from A"
        print(f"A.method, __dict__: {self.__dict__}")

class B(A):
    def method(self):
        self.b_var = "from B"
        print(f"B.method, __dict__: {self.__dict__}")
        super().method()  # Calls A.method

b = B()
print(f"MRO: {B.__mro__}")  # (<class 'B'>, <class 'A'>, <class 'object'>)

b.method()
# Output:
# B.method, __dict__: {'b_var': 'from B'}
# A.method, __dict__: {'b_var': 'from B', 'a_var': 'from A'}
```

#### Multiple Inheritance Example:
```python
class Parent1:
    def shared_method(self):
        self.p1_var = "from Parent1"

class Parent2:
    def shared_method(self):
        self.p2_var = "from Parent2"

    def unique_method(self):
        self.unique_var = "from Parent2 unique"

class Child(Parent1, Parent2):
    pass

c = Child()
print(f"MRO: {Child.__mro__}")
# (<class 'Child'>, <class 'Parent1'>, <class 'Parent2'>, <class 'object'>)

c.shared_method()  # Parent1.shared_method wins (MRO order)
print(c.__dict__)  # {'p1_var': 'from Parent1'}

c.unique_method()  # Parent2.unique_method (only one available)
print(c.__dict__)  # {'p1_var': 'from Parent1', 'unique_var': 'from Parent2 unique'}
```

### 12.6 Summary

#### Key Takeaways:
1. **One Instance = One `__dict__`**: All attributes stored in single dictionary
2. **Method Execution**: MRO determines which method runs, but `self` always refers to the instance
3. **All Methods Use Same Storage**: Every method operates on the same instance `__dict__`
4. **Name Mangling**: Private variables (`__var`) become `_ClassName__var` in `__dict__`
5. **MRO Rules**: Left-to-right, depth-first search with C3 linearization for complex hierarchies
6. **Variable Sharing**:
   - **Public/Protected**: Same key names, directly shareable
   - **Private**: Different mangled key names (`_ClassName__varname`), accessed through same class methods but stored separately
7. **Name Mangling Ensures**: Private variable isolation while using unified storage

The fundamental difference from C++ is that Python maintains a single object with one `__dict__` throughout the inheritance hierarchy, while all methods operate on this shared storage space. Name mangling ensures private variable isolation while maintaining the single dictionary approach.
The self parameter is a reference to the current instance of a class. It is used to access variables and methods that belong to that specific object. Additionally, self allows the creation of instance attributes, which are unique to each object of the class and can be accessed by any member function within the class.

**Example:**

```python
class Person:
    def __init__(self):
        self.name="Nirajan"
        self.age=20
        self.classes="Bachelor"

    def display(self):
        print(f"Name: {self.name}")  # Accessing attribute with self
        print(f"Age: {self.age}")    # Accessing attribute with self 
        print(f"Class: {self.classes}")  # Accessing attribute with self
        self.greet()  # Calling another method with self

    def greet(self):
        print("Hello, Welcome to the class")
```

### 5.3 Calling a Member Function

We can call a member function of a class using the dot operator (.) followed by the function name and parentheses.

**Syntax:**

```python
object_name.method_name(arguments)
```

**Example:**

```python
person1.display()  # Calls the display method for person1 object
```

### 5.4 Nested Member Function

We can call a member function from another member function of the same class using the self keyword.

**Syntax:**

```python
class ClassName:
    def method1(self):
        #code block
        self.method2()  # Calling method2 from method1

    def method2(self):
        #code block
```

**Example:**

```python
class Person:
    def __init__(self):
        self.name="Nirajan"
        self.age=20
        self.classes="Bachelor"

    def display(self):
        print(f"Name: {self.name}")
        print(f"Age: {self.age}")
        print(f"Class: {self.classes}")
        self.greet()  # Calling the greet method from display method

    def greet(self):
        print("Hello, Welcome to the class")
```

---

## 6. Constructor: Initializing Objects

A constructor is a special type of method (function) which is used to initialize the instance members of the class. It is called when an object of the class is created. This allows you to set up each object with its specific initial state.

### 6.1 Creating a Constructor

In Python, the constructor method is called `__init__`. It is a special method that is automatically called when an object is created.

**Syntax:**

```python
class ClassName:
    def __init__(self, parameters):
        #code block
```

**Example:**

```python
class Person:
    def __init__(self, name, age, classes):
        # Initialize instance attributes with provided values
        self.name = name        # Instance attribute
        self.age = age          # Instance attribute
        self.classes = classes  # Instance attribute

    def display(self):
        print(f"Name: {self.name}")
        print(f"Age: {self.age}")
        print(f"Class: {self.classes}")
```

### 6.2 Creating an Object with Constructor

When an object is created, the constructor method is automatically called with the arguments passed to the class.

**Syntax:**

```python
object_name = ClassName(arguments)
```

**Example:**

```python
# Creating a Person object with name, age, and classes values
person1 = Person("Nirajan", 20, "Bachelor")
```

In this example, the `__init__` method is called with "Nirajan", 20, and "Bachelor" as arguments, which initializes the object's attributes.

---





    



## 6. Access Specifiers in Python

Access specifiers control the visibility and accessibility of class members (attributes and methods). Understanding access control is important for implementing encapsulation - one of the four pillars of OOP.

### 6.1 Public Members

Public members are accessible from both inside and outside the class. By default, all members in Python are public unless specified otherwise.

**Example:**

```python
class Person:
    def __init__(self):
        self.name = "Alice"  # Public member
    def display(self):      #public method
        print(f"Name: {self.name}")

person1 = Person()
print(person1.name)  # Output: Alice
person1.display()    # Output: Name: Alice
```

### 4.2 Protected Members

Protected members are accessible within the class and its subclasses. They are denoted by a single underscore (_) before the member name.

**Example:**

```python
class Person:
    def __init__(self):
        self._name = "Alice"  # Protected member
    def _display(self):      # Protected method
        print(f"Name: {self._name}")

class Student(Person):
    def show(self):
        self._display()  # Accessing protected method from subclass
        print(f"Accessing protected member: {self._name}")

student1 = Student()
student1.show()
```

```bash
Name: Alice
Accessing protected member: Alice
```

Explaination: Here, the _name and _display() are protected members of Person class. They can be accessed within the Person class and also in the Student subclass. However, they should not be accessed directly from outside the class or subclass.

> Note: Accessing a protected member using object._member_name from outside the class is possible in Python but not recommended, as it violates the principle of encapsulation. However, it is perfectly valid to access protected members within the class itself or any of its subclasses.

### 4.3 Private Members

Private members are accessible only within the class. They cant be accessed from outside the class or even in subclasses. They are denoted by a double underscore (__) before the member name.

**Example:**

```python
class Person:
    __city = "New York"  # Private member

person1 = Person()
print(person1.__city)  # Error: 'Person' object has no attribute '__city'
```

**Example of private variable and method:**

```python
class Person:
    def __init__(self):
        self.__name = "Alice"  # Private member

    def __display(self):  # Private method
        print(f"Name: {self.__name}")
    
    def show(self):
        self.__display()  # Accessing private method within the class
        print(f"Accessing private member: {self.__name}") # Accessing private member within the class

person1 = Person()
print(person1.__name)  # Error: 'Person' object has no attribute '__name'
person1.__display()  # Error: 'Person' object has no attribute '__display'
person1.show()  # Correct way to access private members within the class as show() is public
```


**Table of Access Specifiers in Python:**


| Access Specifier | Accessible from class | Accessible from subclass | Accessible from outside class |
|------------------|----------------------|--------------------------|-------------------------------|
| Public           | Yes                  | Yes                      | Yes                           |
| Protected        | Yes                  | Yes                      | No                            |
| Private          | Yes                  | No                       | No                            |

---

## 5. Inheritance in Python

Inheritance is a mechanism in which one class acquires the properties and behavior of another class. The class which inherits the properties and behavior is known as the child class, and the class whose properties and behavior are inherited is known as the parent class.

### 5.1 Creating a Child Class

To create a child class that inherits from a parent class, we specify the parent class in parentheses after the child class name.

**Syntax:**

```python
class ChildClassName(ParentClassName):
    #code block
```

**Example:**
```python
class Employee:
    def set_name(self, name):
        self.name = name
    def display_name(self):
        print(f"Name: {self.name}")
class TypeEmployee(Employee):  # Child class inheriting from Employee
    def set_salary(self, salary):
        self.salary = salary
    def display_salary(self):
        print(f"Salary: {self.salary}")

type_emp1 = TypeEmployee()
type_emp1.set_name("Nirajan")  # Inherited method from Employee
type_emp1.set_salary(50000)     # Method from TypeEmployee
type_emp1.display_name()         # Inherited method from Employee
type_emp1.display_salary()       # Method from TypeEmployee
```

**Note:** Only public and protected members are inherited by the child class. Private members are not inherited by the child class.
> Note: In here the TypeEmployee class is the child class that inherits from the Employee class, which means all the public and protected members of the Employee class are available in the TypeEmployee class.such that new typpe class look like
```bash
Attribute:
    Name
    Salary
Methods:
    set_name()
    display_name()
    set_salary()
    display_salary()
```


### 5.2 Method Overriding

### 5.3 Method Overriding

Method overriding is a feature of object-oriented programming that allows a subclass to provide a specific implementation of a method that is already provided by its parent class. When a method in a subclass has the same name as a method in the parent class, the method in the subclass overrides the method in the parent class.

**Example:**

```python
class Person:
    def display(self):
        print("Person class")

class Student(Person):
    def display(self):  # This overrides Person's display method
        print("Student class")

student1 = Student()
student1.display()  # Calls Student's display method, not Person's

# Output: Student class
```

We can also call the parent class's method from the overridden method using the super() function:


### 5.2 The super() Method in Python
When both a parent class and a child class define a method with the same name, creating an object of the child class and calling that method will execute the version defined in the child class, not the one in the parent class.

To explicitly invoke the parent class’s version of the method, we use the super() function. This is especially useful when the child class method needs to extend or build upon the functionality of the parent class method.

**Syntax:**

```python
super().method_name()
```

**Example:**

```python
class Person:
    def display(self):
        print("Person class")
    
class Student(Person):
    def display(self):
        super().display()  # Calls the parent class's display method
        Person.display(self)  # Another way to call parent class method
        print("Student class")

student1 = Student()
student1.display()
```

```bash
# Output:
Person class
Student class
```


**Usecase:**

Consider the following implementation:
```python
class Employee:
    def __init__(self, name, age):
        self.name = name
        self.age = age
    def display_employee(self):
        print(f"Name: {self.name}, Age: {self.age}")
class TypeEmployee(Employee):
    def __init__(self, name, age, salary):
        self.age = age
        self.salary = salary
    def display_specific_employee(self):
        print(f"Salary: {self.salary}")

type_emp1 = TypeEmployee("Nirajan", 25, 50000)
type_emp1.display_employee()  # Error: 'TypeEmployee' object has no attribute 'display_employee'
```

In this example,the __init__ method of the TypeEmployee class overrides the __init__ method of the Employee class. So during the creation of TypeEmployee object, the __init__ method of Employee class is not called, and hence the name attribute is not initialized. When we try to call display_employee() method, it raises an error because the name attribute does not exist in the TypeEmployee object so inorder to fix this issue we have two solutions:

- Copying the code of parent class's __init__ method inside child class's __init__ method

```python
class TypeEmployee(Employee):
    def __init__(self, name, age, salary):
        self.name = name  # Initializing name attribute
        self.age = age
        self.salary = salary
```

- Using super() to call the parent class's __init__ method inside child class's __init__ method

```python
class TypeEmployee(Employee):
    def __init__(self, name, age, salary):
        super().__init__(name, age)  # Calling parent class's __init__ method
        self.salary = salary
```

**Note:** When we use super() it work based on MRO to resolve which class function to use but ClassName.functionName() explicitly tell which class function to use.



## 6. Method Resolution Order (MRO)

Method Resolution Order (MRO) is the order in which Python looks for a method or attribute in an inheritance hierarchy. It determines the sequence in which base classes are searched when executing a method. MRO is especially important in multiple inheritance scenarios.

**Example:**

```python
class C:
    f = "dirajan"

class A(C):
    f = "nirajan"

class B(C):
    f = "kirajan"

class D(A, B):
    def display(self):
        print(self.f)

d = D()
print(D.__mro__)  # Shows the method resolution order
d.display()
```

**Output:**

```bash
(<class '__main__.D'>, <class '__main__.A'>, <class '__main__.B'>, <class '__main__.C'>, <class 'object'>)
nirajan
```

It means Python will search for the attribute or method in the order of D->A->B->C->object. If the attribute/method is not found in D, it will search in A, and so on. But if the attribute/method is found in D, it will not search further in A, B, C, or object.

---

### 5.4 Types of Inheritance in Python
Inheritance is a mechanism in which one class acquires the properties and behavior of another class. There are different types of inheritance in Python:

#### 5.4.1 Single Inheritance

In single inheritance, a class inherits from only one parent class.

The pictorial representation of single inheritance is:

```
    A
    |
    B
```

**Syntax:**

```python
class ParentClass:
    #code block

class ChildClass(ParentClass):
    #code block
```

**Example:**

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age
    def display(self):
        return f"Name: {self.name}, Age: {self.age}"

class Student(Person):
    def __init__(self, name, age, id):
        super().__init__(name, age)  # or Person.__init__(self, name, age)
        self.id = id
    
    def display(self):
        print(f"ID: {self.id}", super().display())  # or Person.display(self)

student1 = Student("Alice", 30, 101)
student1.display()
```

```bash
# Output:
# ID: 101 Name: Alice, Age: 30
```

#### 5.4.2 Multiple Inheritance

In multiple inheritance, a class inherits from more than one parent class.

The pictorial representation of multiple inheritance is:

```
    A    B
     \  /
      C
```

**Syntax:**

```python
class ParentClass1:
    #code block

class ParentClass2:
    #code block

class ChildClass(ParentClass1, ParentClass2):
    #code block
```

**Example:**

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def display(self):
        return f"Name: {self.name}, Age: {self.age}"

class Address:
    def __init__(self, city, state):
        self.city = city
        self.state = state
    
    def display(self):
        return f"City: {self.city}, State: {self.state}"

class Student(Person, Address):
    def __init__(self, name, age, id, city, state):
        Person.__init__(self, name, age)
        Address.__init__(self, city, state)
        self.id = id
    
    def display(self):
        print(f"ID: {self.id}", Person.display(self), Address.display(self))

student1 = Student("Alice", 30, 101, "New York", "New York")
student1.display()
```

```bash
# Output:
# ID: 101 Name: Alice, Age: 30 City: New York, State: New York
```

#### 5.4.3 Multilevel Inheritance

In multilevel inheritance, a class inherits from a parent class, and another class inherits from the child class.

The pictorial representation of multilevel inheritance is:

```
    A
    |
    B
    |
    C
```

**Syntax:**

```python
class ParentClass:
    #code block

class ChildClass(ParentClass):
    #code block

class GrandChildClass(ChildClass):
    #code block
```

**Example:**

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def display(self):
        return f"Name: {self.name}, Age: {self.age}"

class Student(Person):
    def __init__(self, name, age, id):
        super().__init__(name, age)  # or Person.__init__(self, name, age)
        self.id = id
    
    def display(self):
        print(f"ID: {self.id}", super().display())  # or print(f"ID: {self.id}", Person.display(self))

class CollegeStudent(Student):
    def __init__(self, name, age, id, classes):
        super().__init__(name, age, id)  # or Student.__init__(self, name, age, id)
        self.classes = classes
    
    def display(self):
        print(f"Class: {self.classes}", super().display())  # or print(f"Class: {self.classes}", Student.display(self))


student1 = CollegeStudent("Alice", 30, 101, "Bachelor")
student1.display()
```

```bash
# Output:
# Class: Bachelor ID: 101 Name: Alice, Age: 30
```

#### 5.4.4 Hierarchical Inheritance

In hierarchical inheritance, more than one class inherits from a single parent class.

The pictorial representation of hierarchical inheritance is:

```
    A
   / \
  B   C
```

**Syntax:**

```python
class ParentClass:
    #code block

class ChildClass1(ParentClass):
    #code block

class ChildClass2(ParentClass):
    #code block
```

**Example:**

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def display(self):
        return f"Name: {self.name}, Age: {self.age}"

class Student(Person):
    def __init__(self, name, age, id):
        super().__init__(name, age)  # or Person.__init__(self, name, age)
        self.id = id
    
    def display(self):
        print(f"ID: {self.id}", super().display())  # or print(f"ID: {self.id}", Person.display(self))

class Employee(Person):
    def __init__(self, name, age, emp_id):
        super().__init__(name, age)  # or Person.__init__(self, name, age)
        self.emp_id = emp_id
    
    def display(self):
        print(f"Emp ID: {self.emp_id}", super().display())  # or print(f"Emp ID: {self.emp_id}", Person.display(self))

student1 = Student("Alice", 30, 101)
student1.display()

employee1 = Employee("Bob", 25, 201)
employee1.display()
```

```bash
# Output:
# ID: 101 Name: Alice, Age: 30
# Emp ID: 201 Name: Bob, Age: 25
```

**Note:** Hybrid inheritance is a combination of two or more types of inheritance.


---
> Note: For Multiple Inheritance and Multi level inheritance it uses MRO to resolve which class function to use when there is ambiguity.


## 7. Properties - Getters and Setters

Properties provide controlled access to class attributes. They allow you to implement getter and setter methods that act like attributes.

### 7.1 Getters

Getters are methods implemented using the @property decorator. They are specially used:
- To act as a value (data) instead of a method
- To access the value of a private attribute without directly accessing it

**Syntax:**

```python
class ClassName:
    @property
    def method_name(self):
        #code block
```

**Example:**

```python
class Person:
    def __init__(self, name, age):
        self._name = name  # Protected attribute
        self._age = age    # Protected attribute

    @property
    def display(self):
        return f"Name: {self._name}, Age: {self._age}"

person1 = Person("Nirajan", 20)
print(person1.display)  # Output: Name: Nirajan, Age: 20
# Note: No parentheses used - accessed like an attribute, not a method
```

### 7.2 Setters

Setters are methods implemented using the @method_name.setter decorator. They are specially used:
- To set the value of a private attribute without directly setting it
- To perform validation before setting the value of an attribute

**Syntax:**

```python
class ClassName:
    @method_name.setter
    def method_name(self, value):
        #code block
```

**Example:**

```python
class Person:
    def __init__(self, name, age):
        self._name = name
        self._age = age

    @property
    def display(self):
        return f"Name: {self._name}, Age: {self._age}"

    @display.setter
    def display(self, value):
        self._name, age_str = value.split(",")
        self._age = int(age_str)

person1 = Person("Nirajan", 20)
print(person1.display)  # Output: Name: Nirajan, Age: 20
person1.display = "Alice, 30"  # Using setter to modify attributes
print(person1.display)  # Output: Name: Alice, Age: 30
```

---

## 8. Special Methods (Magic/Dunder Methods)

Magic methods, also known as dunder methods (double underscore), are special methods that have double underscores at the beginning and end of their names. They are used to define the behavior of objects and are automatically called when certain operations are performed on objects.

Some of the commonly used magic methods are:

- `__init__`: Constructor method, called when an object is created
- `__str__`: Called by the str() built-in function to return a string representation of an object
- `__repr__`: Called by the repr() built-in function to return an unambiguous string representation of an object
- `__add__`: Called by the + operator to perform addition
- `__len__`: Called by the len() built-in function to return the length of an object
- `__call__`: Called when an object is called as a function

**Note:** All magic methods can be seen using `dir(object_name)` method, and we can override these methods in our class.

**Example of various magic methods:**

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def __str__(self):
        return f"Name: {self.name}, Age: {self.age}"

    def __repr__(self):
        return f"Person('{self.name}', {self.age})"

    def __add__(self, other):
        return self.age + other.age

    def __len__(self):
        return len(self.name)
    
    def __call__(self):
        return f"Name: {self.name}, Age: {self.age}"

person1 = Person("Alice", 30)
person2 = Person("Bob", 25)

print(person1)  # Output: Name: Alice, Age: 30 (uses __str__)

print(repr(person1))  # Output: Person('Alice', 30) (uses __repr__)

print(person1 + person2)  # Output: 55 (uses __add__)

print(len(person1))  # Output: 5 (uses __len__ to get length of name)

print(person1())  # Output: Name: Alice, Age: 30 (uses __call__)
```

---

## 9. Operator Overloading

Operator overloading is a feature of object-oriented programming that allows us to define the behavior of operators for user-defined objects. It lets us define how operators like +, -, *, /, ==, !=, etc., behave with objects of our class.

To overload an operator, we define the corresponding magic method in the class:

**Example:**

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __add__(self, other):
        # Define what happens when + is used with Point objects
        x = self.x + other.x
        y = self.y + other.y
        return Point(x, y)

    def __str__(self):
        return f"({self.x}, {self.y})"

point1 = Point(1, 2)
point2 = Point(3, 4)
point3 = point1 + point2  # Uses the __add__ method
print(point3)  # Output: (4, 6)
```

For unary operators like negation (-), we can overload the `__neg__` method:

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __neg__(self):
        # Define what happens when - is used with Point objects
        return Point(-self.x, -self.y)

    def __str__(self):
        return f"({self.x}, {self.y})"
point1 = Point(1, 2)
point2 = -point1  # Uses the __neg__ method
print(point2)  # Output: (-1, -2)
```

**Note:** We can only overload existing operators. We cannot create new operators.


---

## 10. Static Method

Static methods are methods that can be called without creating an object of the class. They are defined using the @staticmethod decorator and can be called using the class name.

**Syntax:**

```python
class ClassName:
    @staticmethod
    def method_name(parameters):
        #code block
```

**Example:**

```python
class Calculator:
    @staticmethod
    def add(a, b):
        return a + b

result = Calculator.add(5, 3)  # Calling static method directly
print(result)  # Output: 8

# Can also call using an object (though not typical)
calculator = Calculator()
result = calculator.add(5, 3)
print(result)  # Output: 8
```





## 11. Introspection with dir() and __dict__

### 11.1 The dir() Method

The dir() method returns a list of attributes and methods of any object. It provides a way to introspect objects at runtime.

**Syntax:**

```python
dir(object)
```

**Example:**

```python
class Person:
    def __init__(self):
        self.name = "Alice"
        self.age = 30

person1 = Person()
print(dir(person1))
# Output: ['__class__', '__delattr__', '__dict__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__getstate__', '__gt__', '__hash__', '__init__', '__init_subclass__', '__le__', '__lt__', '__module__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', '__weakref__', 'age', 'name']
```

**Example with list:**

```python
l = [1, 2, 3]
print(dir(l))
# Output: ['__add__', '__class__', '__class_getitem__', '__contains__', '__delattr__', '__delitem__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__getitem__', '__getstate__', '__gt__', '__hash__', '__iadd__', '__imul__', '__init__', '__init_subclass__', '__iter__', '__le__', '__len__', '__lt__', '__mul__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__reversed__', '__rmul__', '__setattr__', '__setitem__', '__sizeof__', '__str__', '__subclasshook__', 'append', 'clear', 'copy', 'count', 'extend', 'index', 'insert', 'pop', 'remove', 'reverse', 'sort']
```

### 11.2 The __dict__ Method

The __dict__ attribute contains a dictionary of an object's attributes. It provides another way to examine an object's state.

**Syntax:**

```python
object.__dict__
```

**Example:**

```python
class Person:
    def __init__(self):
        self.name = "Alice"
        self.age = 30
person1 = Person()
print(person1.__dict__)  #{'name': 'Alice', 'age': 30}


person1.name = "Bob"  # Set an instance attribute
print(person1.__dict__)  # {'name': 'Bob', 'age': 30}
```

---


## 14. How Python Works Internally

# Python Instance Variables and Method Resolution Guide

## Understanding Instance Variable Storage

### Key Concepts:
- **Single Object per Instance**: When you create a class instance, Python creates only ONE object
- **`__dict__` Storage**: All instance attributes are stored in the instance's `__dict__` dictionary
- **Dynamic Creation**: Attributes are created when first assigned, not when accessed
- **Direct Modification**: All attribute changes modify the same `__dict__`

### Basic Example:

```python
class Person:
    def __init__(self, name):
        self.name = name  # Creates "name" key in __dict__

p = Person("Alice")
print(p.__dict__)  # {'name': 'Alice'}

p.age = 25         # Creates "age" key in __dict__
print(p.__dict__)  # {'name': 'Alice', 'age': 25}

p.name = "Bob"     # Modifies existing "name" key
print(p.__dict__)  # {'name': 'Bob', 'age': 25}
```

## How Attribute Assignment and Access Works

### Core Rules:
- **Assignment (self.attr = value)**: Always creates/modifies key in instance's `__dict__`
- **Access (self.attr)**: Checks instance `__dict__` first, then class hierarchy
- **Missing Attribute**: Accessing non-existent attribute raises `AttributeError`
- **Creating Attribute**: Assigning to non-existent attribute creates new `__dict__` entry

### Example:

```python
class Demo:
    def __init__(self):
        self.x = 10

    def modify(self):
        self.x = self.x * 3    # Modifies existing key
        self.y = 100           # Creates new key

d = Demo()
print(d.__dict__)  # {'x': 10}

d.modify()
print(d.__dict__)  # {'x': 30, 'y': 100}

# This would raise AttributeError:
# print(d.z)  # AttributeError: 'Demo' object has no attribute 'z'

# This creates new attribute:
d.z = 50
print(d.__dict__)  # {'x': 30, 'y': 100, 'z': 50}
```

## Inheritance: One Instance, One __dict__

### Key Points:
- **Single `__dict__`**: Child class instance has only ONE `__dict__`
- **Shared Storage**: Parent and child methods modify the SAME `__dict__`
- **No Separate Variables**: Unlike C++, there's no separate storage for parent/child attributes

### Example:

```python
class Parent:
    def __init__(self):
        self.name = "parent"

    def set_parent_attr(self):
        self.parent_var = "from parent"

class Child(Parent):
    def __init__(self):
        super().__init__()
        self.name = "child"        # Overwrites parent's name

    def set_child_attr(self):
        self.child_var = "from child"

c = Child()
print(c.__dict__)  # {'name': 'child'}

c.set_parent_attr()  # Parent method modifies child's __dict__
print(c.__dict__)    # {'name': 'child', 'parent_var': 'from parent'}

c.set_child_attr()
print(c.__dict__)    # {'name': 'child', 'parent_var': 'from parent', 'child_var': 'from child'}
```

## Variable Access Levels: Public, Protected, Private

### Concepts:
- **Public (self.var)**: Accessible everywhere
- **Protected (self._var)**: Convention for internal use (still accessible)
- **Private (self.__var)**: Name mangling applied, creates `_ClassName__var`

### Public and Protected Variables
- **Public variables** (name) and **protected variables** (_name) are directly shareable between base and derived classes
- They are stored in the instance dictionary with their original names
- No name transformation occurs

### Private Variables - Name Mangling Behavior

#### Instance Variable Storage
- When creating **private instance variables** (`__name`) in base and derived classes, Python applies **name mangling**
- All variables are stored in the **same single `__dict__`** for the instance
- However, private variables get **different keys** due to name mangling

#### Name Mangling Process
- **Base class A**: `__name` becomes `_A__name` in the dictionary
- **Derived class B**: `__name` becomes `_B__name` in the dictionary
- This creates unique keys for each class's private variables

#### Access Mechanism
- When accessing `self.__name` in class A → Python searches for `_A__name` in `__dict__`
- When accessing `self.__name` in class B → Python searches for `_B__name` in `__dict__`
- Same attribute name, different mangled keys, but accessed through the same class functions

### Example:

```python
class AccessDemo:
    def __init__(self):
        self.public = "everyone can see"
        self._protected = "internal use"
        self.__private = "name mangled"

obj = AccessDemo()
print(obj.__dict__)
# {'public': 'everyone can see', '_protected': 'internal use', '_AccessDemo__private': 'name mangled'}

# Accessing:
print(obj.public)      # Works
print(obj._protected)  # Works (but shouldn't be used externally)
# print(obj.__private) # AttributeError
print(obj._AccessDemo__private)  # Works (name mangled form)
```

#### Dictionary Structure Example:

```python
instance.__dict__ = {
    'public_var': 'shared',           # Public - same key
    '_protected_var': 'shared',       # Protected - same key  
    '_A__private_var': 'base_value',  # Private in base class
    '_B__private_var': 'derived_value' # Private in derived class
}
```

## Method Resolution Order (MRO) and Function Execution

### Core Concepts:
- **MRO**: Python uses C3 linearization to determine method lookup order
- **Method Lookup**: When calling `self.method()`, Python searches MRO until it finds the method. It first searches function in 1st class of MRO if not found then second and so on
- **Attribute Access in Methods**: Even when executing parent method, `self` refers to the instance
- **Single `__dict__` Rule**: All methods modify the same instance `__dict__`

### Single Inheritance Example:

```python
class A:
    def method(self):
        self.a_var = "from A"
        print(f"A.method, __dict__: {self.__dict__}")

class B(A):
    def method(self):
        self.b_var = "from B"
        print(f"B.method, __dict__: {self.__dict__}")
        super().method()  # Calls A.method

b = B()
print(f"MRO: {B.__mro__}")  # (<class 'B'>, <class 'A'>, <class 'object'>)

b.method()
# Output:
# B.method, __dict__: {'b_var': 'from B'}
# A.method, __dict__: {'b_var': 'from B', 'a_var': 'from A'}
```

### Multiple Inheritance Example:

```python
class Parent1:
    def shared_method(self):
        self.p1_var = "from Parent1"

class Parent2:
    def shared_method(self):
        self.p2_var = "from Parent2"

    def unique_method(self):
        self.unique_var = "from Parent2 unique"

class Child(Parent1, Parent2):
    pass

c = Child()
print(f"MRO: {Child.__mro__}")
# (<class 'Child'>, <class 'Parent1'>, <class 'Parent2'>, <class 'object'>)

c.shared_method()  # Parent1.shared_method wins (MRO order)
print(c.__dict__)  # {'p1_var': 'from Parent1'}

c.unique_method()  # Parent2.unique_method (only one available)
print(c.__dict__)  # {'p1_var': 'from Parent1', 'unique_var': 'from Parent2 unique'}
```

## Summary

### Key Takeaways:
1. **One Instance = One `__dict__`**: All attributes stored in single dictionary
2. **Method Execution**: MRO determines which method runs, but `self` always refers to the instance
3. **All Methods Use Same Storage**: Every method operates on the same instance `__dict__`
4. **Name Mangling**: Private variables (`__var`) become `_ClassName__var` in `__dict__`
5. **MRO Rules**: Left-to-right, depth-first search with C3 linearization for complex hierarchies
6. **Variable Sharing**:
   - **Public/Protected**: Same key names, directly shareable
   - **Private**: Different mangled key names (`_ClassName__varname`), accessed through same class methods but stored separately
7. **Name Mangling Ensures**: Private variable isolation while using unified storage

The fundamental difference from C++ is that Python maintains a single object with one `__dict__` throughout the inheritance hierarchy, while all methods operate on this shared storage space. Name mangling ensures private variable isolation while maintaining the single dictionary approach.



## 13. Comparison with Other Languages (Not Supported in Python)

### 13.1 Types of Inheritance in C++

Inheritance is a mechanism in which one class acquires the properties and behavior of another class. While Python has a more straightforward inheritance model, other languages like C++ offer more granular control over inheritance:

- **Public Inheritance**: In public inheritance, the public members of the base class become public members of the derived class, and the protected members of the base class become protected members of the derived class.

- **Protected Inheritance**: In protected inheritance, the public members of the base class become protected members of the derived class, and the protected members of the base class become protected members of the derived class while the private members of the base class is not inherited.

- **Private Inheritance**: In private inheritance, the public members of the base class become private members of the derived class, and the protected members of the base class become private members of the derived class while the private members of the base class is not inherited.

Table of Inheritance in C++:
| Inheritance Type | Public Members | Protected Members | Private Members |
|------------------|----------------|-------------------|-----------------|
| Public Inheritance | Public          | Protected         | Not Inherited    |
| Protected Inheritance | Protected     | Protected         | Not Inherited    |
| Private Inheritance | Private        | Private          | Not Inherited    |

This comparison helps understand the differences in how various languages implement OOP concepts.