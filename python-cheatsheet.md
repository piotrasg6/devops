# Advanced Python Cheat Sheet

## Basic Syntax and Data Types

### Variables and Basic Types
```python
# Variable assignment
x = 5                   # integer
y = 3.14                # float
name = "Python"         # string
is_valid = True         # boolean

# Multiple assignment
a, b, c = 1, 2, 3

# Type checking
type(x)                 # <class 'int'>
isinstance(x, int)      # True

# Type conversion
str(123)                # "123"
int("123")              # 123
float("3.14")           # 3.14
bool(0)                 # False
list("abc")             # ['a', 'b', 'c']
```

### Strings
```python
# String creation
s1 = "Hello"
s2 = 'World'
multi_line = """This is a
multi-line string"""

# String formatting
name = "Alice"
f"Hello, {name}!"                       # f-strings (Python 3.6+): "Hello, Alice!"
"Hello, {}!".format(name)               # str.format(): "Hello, Alice!"
"Hello, %s!" % name                     # %-formatting: "Hello, Alice!"

# String methods
"hello".upper()                         # "HELLO"
"HELLO".lower()                         # "hello"
"hello world".title()                   # "Hello World"
"hello world".capitalize()              # "Hello world"
"hello".count('l')                      # 2
"hello world".replace("world", "Python") # "hello Python"
"  hello  ".strip()                     # "hello"
"hello world".split()                   # ["hello", "world"]
"hello world".split('o')                # ["hell", " w", "rld"]
" ".join(["hello", "world"])            # "hello world"
"hello".startswith("he")                # True
"hello".endswith("lo")                  # True
"hello world".find("world")             # 6 (index where substring starts)
"world" in "hello world"                # True

# Raw strings (no escape characters)
path = r"C:\Users\name\Documents"       # C:\Users\name\Documents

# String slicing
s = "Hello, World!"
s[0]                    # 'H'
s[-1]                   # '!'
s[0:5]                  # 'Hello'
s[:5]                   # 'Hello'
s[7:]                   # 'World!'
s[::2]                  # 'Hlo ol!'  (every 2nd character)
s[::-1]                 # '!dlroW ,olleH'  (reversed)
```

### Numbers
```python
# Integer operations
5 + 2                   # 7  (addition)
5 - 2                   # 3  (subtraction)
5 * 2                   # 10 (multiplication)
5 / 2                   # 2.5 (division, returns float)
5 // 2                  # 2 (floor division)
5 % 2                   # 1 (modulus/remainder)
5 ** 2                  # 25 (exponentiation)
-5                      # negation
abs(-5)                 # 5 (absolute value)
divmod(5, 2)            # (2, 1) (quotient and remainder)

# Float operations
3.14 + 2.0              # 5.14
round(3.14159, 2)       # 3.14
import math
math.floor(3.7)         # 3
math.ceil(3.2)          # 4
math.trunc(3.7)         # 3 (truncation towards zero)
math.sqrt(16)           # 4.0

# Complex numbers
z = 2 + 3j              # Complex number (2 + 3i)
z.real                  # 2.0
z.imag                  # 3.0
abs(z)                  # 3.605551275463989 (magnitude)
z.conjugate()           # (2-3j)

# Number systems
bin(10)                 # '0b1010' (binary)
oct(10)                 # '0o12' (octal)
hex(10)                 # '0xa' (hexadecimal)
int('1010', 2)          # 10 (binary to decimal)
int('12', 8)            # 10 (octal to decimal)
int('a', 16)            # 10 (hexadecimal to decimal)

# Number precision
import decimal
from decimal import Decimal
Decimal('0.1') + Decimal('0.2')  # Decimal('0.3') (exact)
```

### Lists
```python
# List creation
empty_list = []
numbers = [1, 2, 3, 4, 5]
mixed = [1, "hello", 3.14, True]
nested = [1, [2, 3], 4]
list_comp = [x**2 for x in range(5)]    # [0, 1, 4, 9, 16]
list_from_range = list(range(5))        # [0, 1, 2, 3, 4]

# List access and slicing
numbers[0]              # 1
numbers[-1]             # 5
numbers[1:3]            # [2, 3]
nested[1][0]            # 2

# List methods
numbers.append(6)       # [1, 2, 3, 4, 5, 6]
numbers.extend([7, 8])  # [1, 2, 3, 4, 5, 6, 7, 8]
numbers.insert(0, 0)    # [0, 1, 2, 3, 4, 5, 6, 7, 8]
numbers.remove(0)       # [1, 2, 3, 4, 5, 6, 7, 8]
numbers.pop()           # 8, numbers = [1, 2, 3, 4, 5, 6, 7]
numbers.pop(0)          # 1, numbers = [2, 3, 4, 5, 6, 7]
numbers.index(5)        # 3
numbers.count(5)        # 1
numbers.sort()          # [2, 3, 4, 5, 6, 7]
numbers.reverse()       # [7, 6, 5, 4, 3, 2]
numbers.clear()         # []
numbers_copy = numbers.copy()  # Create a shallow copy

# List operations
[1, 2] + [3, 4]         # [1, 2, 3, 4]
[1, 2] * 3              # [1, 2, 1, 2, 1, 2]
3 in [1, 2, 3]          # True
len([1, 2, 3])          # 3
min([1, 2, 3])          # 1
max([1, 2, 3])          # 3
sum([1, 2, 3])          # 6

# Sorting
sorted([3, 1, 2])                  # [1, 2, 3]
sorted([3, 1, 2], reverse=True)    # [3, 2, 1]
sorted(["apple", "banana", "cherry"], key=len)  # Sort by length: ["apple", "cherry", "banana"]

# List comprehensions
[x**2 for x in range(5)]                    # [0, 1, 4, 9, 16]
[x for x in range(10) if x % 2 == 0]        # [0, 2, 4, 6, 8]
[(x, y) for x in [1, 2] for y in [3, 4]]    # [(1, 3), (1, 4), (2, 3), (2, 4)]
```

### Tuples
```python
# Tuple creation (immutable)
empty_tuple = ()
single_item = (1,)              # Comma needed for single item
coordinates = (10, 20)
mixed = (1, "hello", 3.14)
nested = (1, (2, 3), 4)
tuple_from_list = tuple([1, 2, 3])

# Tuple packing and unpacking
coordinates = 10, 20            # Packing
x, y = coordinates              # Unpacking

# Tuple methods
coordinates.count(10)           # 1
coordinates.index(20)           # 1

# Named tuples
from collections import namedtuple
Point = namedtuple('Point', ['x', 'y'])
p = Point(10, 20)
p.x                             # 10
p.y                             # 20
```

### Dictionaries
```python
# Dictionary creation
empty_dict = {}
person = {"name": "Alice", "age": 30, "is_student": False}
dict_constructor = dict(name="Alice", age=30)
dict_from_tuples = dict([("name", "Alice"), ("age", 30)])
dict_comp = {x: x**2 for x in range(5)}  # {0: 0, 1: 1, 2: 4, 3: 9, 4: 16}

# Dictionary access and modification
person["name"]                 # "Alice"
person.get("name")             # "Alice"
person.get("address", "N/A")   # "N/A" (default if key doesn't exist)
person["address"] = "123 Main St"
person.update({"email": "alice@example.com", "phone": "555-1234"})
del person["phone"]
phone = person.pop("email")    # Removes and returns value

# Dictionary methods
person.keys()                  # dict_keys(['name', 'age', 'is_student', 'address'])
person.values()                # dict_values(['Alice', 30, False, '123 Main St'])
person.items()                 # dict_items([('name', 'Alice'), ('age', 30), ('is_student', False), ('address', '123 Main St')])
person.clear()                 # {}
person_copy = person.copy()    # Create a shallow copy

# Dictionary comprehensions
{k: v for k, v in [('a', 1), ('b', 2)]}  # {'a': 1, 'b': 2}
{x: x**3 for x in range(5)}               # {0: 0, 1: 1, 2: 8, 3: 27, 4: 64}

# Default dictionaries
from collections import defaultdict
dd = defaultdict(list)          # Default value is an empty list
dd['key'].append(1)             # No KeyError, creates an empty list first
dd                              # defaultdict(<class 'list'>, {'key': [1]})

# Counter dictionary
from collections import Counter
Counter("hello")                # Counter({'l': 2, 'h': 1, 'e': 1, 'o': 1})
Counter([1, 2, 2, 3, 3, 3])     # Counter({3: 3, 2: 2, 1: 1})
```

### Sets
```python
# Set creation (unordered, unique elements)
empty_set = set()               # Note: {} creates an empty dict, not a set
numbers = {1, 2, 3, 4, 5}
set_constructor = set([1, 2, 2, 3])  # {1, 2, 3}
set_comp = {x**2 for x in range(5)}  # {0, 1, 4, 9, 16}

# Set methods
numbers.add(6)                  # {1, 2, 3, 4, 5, 6}
numbers.remove(6)               # {1, 2, 3, 4, 5} (raises KeyError if not found)
numbers.discard(6)              # {1, 2, 3, 4, 5} (no error if not found)
numbers.pop()                   # Removes and returns an arbitrary element
numbers.clear()                 # set()

# Set operations
a = {1, 2, 3}
b = {3, 4, 5}
a | b                           # Union: {1, 2, 3, 4, 5}
a.union(b)                      # Union: {1, 2, 3, 4, 5}
a & b                           # Intersection: {3}
a.intersection(b)               # Intersection: {3}
a - b                           # Difference: {1, 2}
a.difference(b)                 # Difference: {1, 2}
a ^ b                           # Symmetric difference: {1, 2, 4, 5}
a.symmetric_difference(b)       # Symmetric difference: {1, 2, 4, 5}
a <= b                          # Subset check: False
a.issubset(b)                   # Subset check: False
a >= b                          # Superset check: False
a.issuperset(b)                 # Superset check: False
a.isdisjoint(b)                 # True if no common elements (False here)

# Frozen sets (immutable)
frozen = frozenset([1, 2, 3])
```

## Control Flow

### Conditionals
```python
# If statements
if x > 0:
    print("Positive")
elif x < 0:
    print("Negative")
else:
    print("Zero")

# Conditional expressions (ternary operator)
status = "Adult" if age >= 18 else "Minor"

# Truth value testing
# False values: None, False, 0, "", [], {}, set()
# Everything else is True
bool([])                         # False
bool([1, 2])                     # True

# Short-circuit evaluation
result = x and y                 # If x is False, returns x without evaluating y
result = x or y                  # If x is True, returns x without evaluating y

# Multiple conditions
if 0 <= x < 10:                  # Chained comparison
    print("Single digit")

# match statement (Python 3.10+)
match value:
    case 1:
        print("One")
    case 2:
        print("Two")
    case _:
        print("Other")

# match with patterns (Python 3.10+)
match point:
    case (0, 0):
        print("Origin")
    case (0, y):
        print(f"Y-axis at {y}")
    case (x, 0):
        print(f"X-axis at {x}")
    case (x, y):
        print(f"Point at ({x}, {y})")
    case _:
        print("Not a point")
```

### Loops
```python
# For loops
for i in range(5):
    print(i)                     # 0, 1, 2, 3, 4

# For with enumerate
for i, value in enumerate(["a", "b", "c"]):
    print(i, value)              # 0 a, 1 b, 2 c

# For with zip
for name, age in zip(["Alice", "Bob"], [25, 30]):
    print(name, age)             # Alice 25, Bob 30

# While loops
count = 0
while count < 5:
    print(count)
    count += 1

# Loop control
for i in range(10):
    if i == 3:
        continue                 # Skip the rest of the loop for this iteration
    if i == 8:
        break                    # Exit the loop
    print(i)

# Else clause with loops (executes when loop completes normally)
for i in range(5):
    print(i)
else:
    print("Loop completed")      # Prints only if the loop wasn't exited with break

# Infinite loop with break
while True:
    response = input("Continue? (y/n): ")
    if response.lower() != 'y':
        break
```

### Exception Handling
```python
# Basic try-except
try:
    result = 10 / 0
except ZeroDivisionError:
    print("Cannot divide by zero")

# Multiple exceptions
try:
    num = int(input("Enter a number: "))
    result = 10 / num
except ValueError:
    print("Invalid input, not a number")
except ZeroDivisionError:
    print("Cannot divide by zero")

# Catch multiple exceptions
try:
    # Some code
    pass
except (ValueError, TypeError) as e:
    print(f"Error: {e}")

# With else and finally
try:
    num = int(input("Enter a number: "))
except ValueError:
    print("Not a number")
else:
    print(f"You entered: {num}")  # Executes if no exception
finally:
    print("End of input")         # Always executes

# Raise exceptions
if x < 0:
    raise ValueError("x cannot be negative")

# Custom exceptions
class CustomError(Exception):
    def __init__(self, message, code):
        self.message = message
        self.code = code
        super().__init__(self.message)

# Context manager (with statement)
with open("file.txt", "r") as file:
    content = file.read()
# File is automatically closed after the block
```

## Functions and Modules

### Function Basics
```python
# Function definition
def greet(name):
    return f"Hello, {name}!"

# Function with default parameters
def greet(name="World"):
    return f"Hello, {name}!"

# Multiple parameters
def calculate_total(price, tax_rate, discount=0):
    return price * (1 + tax_rate) * (1 - discount)

# Variable-length arguments
def add(*args):                  # args is a tuple
    return sum(args)

# Variable-length keyword arguments
def person_info(**kwargs):       # kwargs is a dictionary
    return kwargs

# Both types combined
def config(*args, **kwargs):
    # Process positional arguments in args
    # Process keyword arguments in kwargs
    pass

# Unpacking arguments
def point(x, y):
    return (x, y)

coords = (3, 4)
point(*coords)                   # Unpacks tuple to x=3, y=4

params = {"x": 3, "y": 4}
point(**params)                  # Unpacks dict to x=3, y=4

# Function annotations (type hints)
def add(a: int, b: int) -> int:
    return a + b
```

### Advanced Function Features
```python
# Lambda functions (anonymous)
square = lambda x: x ** 2
square(5)                        # 25

# Sorting with lambda
sorted([(1, 'c'), (3, 'b'), (2, 'a')], key=lambda x: x[1])  # [(2, 'a'), (3, 'b'), (1, 'c')]

# Higher-order functions
def apply_twice(func, arg):
    return func(func(arg))

apply_twice(lambda x: x * 2, 3)  # 12 (3*2*2)

# Closures
def outer(x):
    def inner(y):
        return x + y             # inner has access to x from outer scope
    return inner

add_5 = outer(5)
add_5(3)                         # 8

# Function attributes
def func():
    pass

func.attribute = "value"         # Functions are objects with attributes

# Function documentation
def calc_area(radius):
    """
    Calculate the area of a circle.
    
    Parameters:
        radius (float): The radius of the circle
        
    Returns:
        float: The area of the circle
    """
    import math
    return math.pi * radius ** 2

help(calc_area)                  # Show documentation
```

### Decorators
```python
# Basic decorator
def simple_decorator(func):
    def wrapper():
        print("Before function call")
        func()
        print("After function call")
    return wrapper

@simple_decorator
def hello():
    print("Hello, world!")

# Equivalent to:
# hello = simple_decorator(hello)

# Decorator with arguments
def decorator_with_args(func):
    def wrapper(*args, **kwargs):
        print("Before function call")
        result = func(*args, **kwargs)
        print("After function call")
        return result
    return wrapper

@decorator_with_args
def add(a, b):
    return a + b

# Parametrized decorators
def repeat(n):
    def decorator(func):
        def wrapper(*args, **kwargs):
            result = None
            for _ in range(n):
                result = func(*args, **kwargs)
            return result
        return wrapper
    return decorator

@repeat(3)
def say_hello():
    print("Hello")

# Stacking decorators
@decorator1
@decorator2
def func():
    pass

# Maintaining function metadata
from functools import wraps

def my_decorator(func):
    @wraps(func)                  # Preserves func.__name__, func.__doc__, etc.
    def wrapper(*args, **kwargs):
        return func(*args, **kwargs)
    return wrapper
```

### Modules and Packages
```python
# Importing modules
import math
import math as m
from math import sqrt
from math import sqrt as sq
from math import *                # Not recommended, imports all names

# Creating modules
# In mymodule.py
def my_function():
    return "Hello from my module"

# In main script
import mymodule
mymodule.my_function()

# Module search path
import sys
sys.path                         # List of directories Python searches for modules

# Module information
dir(math)                        # List attributes in module
help(math)                       # Show module documentation

# Package structure
# mypackage/
#   __init__.py
#   module1.py
#   module2.py
#   subpackage/
#     __init__.py
#     module3.py

# Relative imports
# In mypackage/subpackage/module3.py
from .. import module1            # Import from parent package
from ..module2 import function    # Import specific item from parent package
from . import module              # Import from same package

# Package initialization
# In __init__.py
from .module1 import function1    # Makes function1 available directly from package
__all__ = ['function1', 'function2']  # Controls what is imported with from pkg import *
```

## Object-Oriented Programming

### Classes and Objects
```python
# Class definition
class Person:
    # Class variable (shared by all instances)
    species = "Human"
    
    # Constructor
    def __init__(self, name, age):
        # Instance variables (unique to each instance)
        self.name = name
        self.age = age
    
    # Instance method
    def greet(self):
        return f"Hello, my name is {self.name}"
    
    # Class method
    @classmethod
    def create_anonymous(cls):
        return cls("Anonymous", 0)
    
    # Static method
    @staticmethod
    def is_adult(age):
        return age >= 18

# Creating instances
person1 = Person("Alice", 30)
person2 = Person("Bob", 25)

# Accessing attributes and methods
person1.name                     # "Alice"
person1.greet()                  # "Hello, my name is Alice"
Person.species                   # "Human"
Person.is_adult(20)              # True
anonymous = Person.create_anonymous()

# Checking instance and class relationships
isinstance(person1, Person)      # True
issubclass(Person, object)       # True (all classes inherit from object)
```

### Inheritance and Polymorphism
```python
# Base class
class Animal:
    def __init__(self, name):
        self.name = name
    
    def speak(self):
        raise NotImplementedError("Subclasses must implement this method")

# Derived class
class Dog(Animal):
    def speak(self):
        return f"{self.name} says Woof!"

class Cat(Animal):
    def speak(self):
        return f"{self.name} says Meow!"

# Using the classes
dog = Dog("Rex")
cat = Cat("Whiskers")
dog.speak()                      # "Rex says Woof!"
cat.speak()                      # "Whiskers says Meow!"

# Multiple inheritance
class A:
    def method(self):
        return "A"

class B:
    def method(self):
        return "B"

class C(A, B):
    pass

c = C()
c.method()                       # "A" (Method Resolution Order: C -> A -> B)
C.__mro__                        # Method Resolution Order tuple

# Abstract base classes
from abc import ABC, abstractmethod

class AbstractAnimal(ABC):
    @abstractmethod
    def speak(self):
        pass                     # Must be implemented by concrete subclasses

# Super function for parent class methods
class ChildClass(ParentClass):
    def __init__(self, name, extra_param):
        super().__init__(name)   # Call parent constructor
        self.extra_param = extra_param
```

### Special Methods
```python
class Vector:
    def __init__(self, x, y):
        self.x = x
        self.y = y
    
    # String representation
    def __str__(self):
        return f"Vector({self.x}, {self.y})"
    
    # Developer-facing string representation
    def __repr__(self):
        return f"Vector({self.x}, {self.y})"
    
    # Equality
    def __eq__(self, other):
        if not isinstance(other, Vector):
            return NotImplemented
        return self.x == other.x and self.y == other.y
    
    # Addition
    def __add__(self, other):
        return Vector(self.x + other.x, self.y + other.y)
    
    # Subtraction
    def __sub__(self, other):
        return Vector(self.x - other.x, self.y - other.y)
    
    # Length (magnitude)
    def __abs__(self):
        return (self.x ** 2 + self.y ** 2) ** 0.5
    
    # Make object callable
    def __call__(self, scalar):
        return Vector(self.x * scalar, self.y * scalar)
    
    # Container methods
    def __len__(self):
        return 2  # A 2D vector has 2 components
    
    def __getitem__(self, index):
        if index == 0:
            return self.x
        elif index == 1:
            return self.y
        else:
            raise IndexError("Vector index out of range")

v1 = Vector(3, 4)
v2 = Vector(1, 2)
print(v1)                        # "Vector(3, 4)"
v1 + v2                          # Vector(4, 6)
abs(v1)                          # 5.0 (magnitude)
v1(2)                            # Vector(6, 8) (scaling)
len(v1)                          # 2
v1[0]                            # 3
```

### Properties and Descriptors
```python
# Property decorator
class Person:
    def __init__(self, name):
        self._name = name
    
    @property
    def name(self):
        """Name property docstring."""
        return self._name
    
    @name.setter
    def name(self, value):
        if not isinstance(value, str):
            raise TypeError("Name must be a string")
        self._name = value
    
    @name.deleter
    def name(self):
        del self._name

person = Person("Alice")
person.name                      # "Alice" (calls getter)
person.name = "Bob"              # Calls setter
# del person.name                # Calls deleter

# Property using property()
class Person:
    def __init__(self, name):
        self._name = name
    
    def get_name(self):
        return self._name
    
    def set_name(self, value):
        self._name = value
    
    def del_name(self):
        del self._name
    
    name = property(get_name, set_name, del_name, "Name property")

# Descriptor
class Validator:
    def __init__(self, name, validation_func):
        self.name = name
        self.validation_func = validation_func
    
    def __get__(self, instance, owner):
        if instance is None:
            return self
        return instance.__dict__[self.name]
    
    def __set__(self, instance, value):
        if not self.validation_func(value):
            raise ValueError(f"Invalid value for {self.name}")
        instance.__dict__[self.name] = value

class Person:
    age = Validator("age", lambda x: isinstance(x, int) and 0 <= x <= 120)
    
    def __init__(self, name, age):
        self.name = name
        self.age = age  # This will use the Validator.__set__ method
```

### Metaprogramming
```python
# Class decorators
def add_method(cls):
    def new_method(self):
        return "New method added"
    cls.new_method = new_method
    return cls

@add_method
class MyClass:
    pass

obj = MyClass()
obj.new_method()                 # "New method added"

# Metaclasses
class Meta(type):
    def __new__(mcs, name, bases, attrs):
        # Add a new method to the class
        attrs['added_method'] = lambda self: "Method added by metaclass"
        return super().__new__(mcs, name, bases, attrs)

class MyClass(metaclass=Meta):
    pass

obj = MyClass()
obj.added_method()               # "Method added by metaclass"

# __slots__ for memory efficiency
class Person:
    __slots__ = ('name', 'age')  # Restricts instance attributes to these
    
    def __init__(self, name, age):
        self.name = name
        self.age = age

# Dynamic attribute access
class DynamicAttrs:
    def __getattr__(self, name):
        # Called when attribute not found through normal methods
        return f"Attribute {name} not found"
    
    def __setattr__(self, name, value):
        # Called when an attribute is set
        print(f"Setting {name} to {value}")
        super().__setattr__(name, value)
    
    def __delattr__(self, name):
        # Called when an attribute is deleted
        print(f"Deleting {name}")
        super().__delattr__(name)
```

## Advanced Features

### Iterators and Generators
```python
# Iterators
class Countdown:
    def __init__(self, start):
        self.start = start
    
    def __iter__(self):
        return self
    
    def __next__(self):
        if self.start <= 0:
            raise StopIteration
        self.start -= 1
        return self.start + 1

# Usage
for num in Countdown(5):
    print(num)                   # 5, 4, 3, 2, 1

# Generators with yield
def countdown(start):
    while start > 0:
        yield start
        start -= 1

# Generator expressions
squares = (x**2 for x in range(10))  # Generator, not evaluated immediately
next(squares)                    # 0
next(squares)                    # 1

# Yield from
def gen1():
    yield from range(3)
    yield from ['a', 'b', 'c']

list(gen1())                     # [0, 1, 2, 'a', 'b', 'c']

# Coroutines with yield
def coroutine():
    x = yield
    print(f"Got {x}")
    y = yield
    print(f"Got {y}")

co = coroutine()
next(co)                         # Prime the coroutine
co.send("Hello")                 # Prints "Got Hello"
co.send("World")                 # Prints "Got World"

# Generator return values (Python 3.3+)
def gen_with_return():
    yield 1
    yield 2
    return "Done"

g = gen_with_return()
next(g)                          # 1
next(g)                          # 2
try:
    next(g)
except StopIteration as e:
    print(e.value)               # "Done"
```

### Context Managers
```python
# Using context managers
with open("file.txt", "r") as f:
    content = f.read()

# Custom context manager using class
class MyContext:
    def __init__(self, name):
        self.name = name
    
    def __enter__(self):
        print(f"Entering {self.name}")
        return self
    
    def __exit__(self, exc_type, exc_val, exc_tb):
        print(f"Exiting {self.name}")
        # Return True to suppress exceptions
        return False

# Custom context manager using decorator
from contextlib import contextmanager

@contextmanager
def my_context(name):
    print(f"Entering {name}")
    try:
        yield name  # Value to be bound to target in with statement
    finally:
        print(f"Exiting {name}")

# Using custom context managers
with MyContext("class-based") as ctx:
    print(f"Inside context: {ctx}")

with my_context("decorator-based") as name:
    print(f"Inside context: {name}")

# Multiple context managers
with open("input.txt") as in_file, open("output.txt", "w") as out_file:
    out_file.write(in_file.read())

# Other useful context managers
from contextlib import suppress
with suppress(FileNotFoundError):
    # Code that might raise FileNotFoundError
    pass

from contextlib import redirect_stdout
import io
f = io.StringIO()
with redirect_stdout(f):
    print("Hello, world!")
output = f.getvalue()            # "Hello, world!\n"
```

### Functional Programming
```python
# map, filter, reduce
numbers = [1, 2, 3, 4, 5]
doubled = list(map(lambda x: x * 2, numbers))  # [2, 4, 6, 8, 10]
evens = list(filter(lambda x: x % 2 == 0, numbers))  # [2, 4]

from functools import reduce
sum_all = reduce(lambda x, y: x + y, numbers)  # 15

# Function composition
from functools import partial
def add(x, y):
    return x + y

add_5 = partial(add, 5)          # Fix first argument to 5
add_5(10)                        # 15

# Memoization
from functools import lru_cache

@lru_cache(maxsize=None)
def fibonacci(n):
    if n <= 1:
        return n
    return fibonacci(n-1) + fibonacci(n-2)

fibonacci(50)                    # Fast computation with caching

# functools.wraps for preserving metadata
from functools import wraps

def decorator(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        return func(*args, **kwargs)
    return wrapper

# Sorting with key functions
names = ["alice", "bob", "Charlie", "Dave"]
sorted(names, key=str.lower)     # ["alice", "bob", "Charlie", "Dave"]

# itemgetter and attrgetter
from operator import itemgetter, attrgetter

people = [{"name": "Alice", "age": 30}, {"name": "Bob", "age": 25}]
sorted(people, key=itemgetter("age"))  # Sort by age

class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

people = [Person("Alice", 30), Person("Bob", 25)]
sorted(people, key=attrgetter("age"))  # Sort by age attribute
```

### Concurrency and Parallelism
```python
# Threading
import threading

def worker(num):
    print(f"Thread {num} starting")
    # Do work
    print(f"Thread {num} finished")

threads = []
for i in range(5):
    t = threading.Thread(target=worker, args=(i,))
    threads.append(t)
    t.start()

for t in threads:
    t.join()                     # Wait for all threads to complete

# Thread synchronization
lock = threading.Lock()
counter = 0

def increment():
    global counter
    with lock:                   # Acquire and release lock
        counter += 1

# Multiprocessing
import multiprocessing

def process_worker(num):
    print(f"Process {num} starting")
    # Do work
    print(f"Process {num} finished")

processes = []
for i in range(5):
    p = multiprocessing.Process(target=process_worker, args=(i,))
    processes.append(p)
    p.start()

for p in processes:
    p.join()                     # Wait for all processes to complete

# Process pool
with multiprocessing.Pool(processes=4) as pool:
    results = pool.map(func, data)

# Concurrent execution with futures
from concurrent.futures import ThreadPoolExecutor, ProcessPoolExecutor

def worker(x):
    return x * x

# With threads
with ThreadPoolExecutor(max_workers=4) as executor:
    results = list(executor.map(worker, range(10)))

# With processes
with ProcessPoolExecutor(max_workers=4) as executor:
    results = list(executor.map(worker, range(10)))
    future = executor.submit(worker, 5)
    result = future.result()

# Asynchronous I/O (asyncio)
import asyncio

async def say_after(delay, what):
    await asyncio.sleep(delay)
    print(what)

async def main():
    # Run sequentially
    await say_after(1, "hello")
    await say_after(2, "world")
    
    # Run concurrently
    task1 = asyncio.create_task(say_after(1, "hello"))
    task2 = asyncio.create_task(say_after(2, "world"))
    await task1
    await task2

asyncio.run(main())

# Async comprehensions
async def generate():
    for i in range(5):
        await asyncio.sleep(0.1)
        yield i

async def main():
    # Async list comprehension
    result = [i async for i in generate()]
    
    # Async with
    async with async_context_manager() as ctx:
        # Do async work
        pass

# Coroutines (asyncio)
async def fetch_data(url):
    # Simulate HTTP request
    await asyncio.sleep(1)
    return f"Data from {url}"

async def main():
    # Gather multiple coroutines
    results = await asyncio.gather(
        fetch_data("url1"),
        fetch_data("url2"),
        fetch_data("url3")
    )
    print(results)
```

### Type Annotations and Checking
```python
# Basic type annotations
def greet(name: str) -> str:
    return f"Hello, {name}"

# Variable annotations
age: int = 30
names: list[str] = ["Alice", "Bob"]  # Python 3.9+

# Type aliases
from typing import List, Dict, Tuple, Optional, Union, Any, Callable

Vector = List[float]
def scale(v: Vector, scalar: float) -> Vector:
    return [x * scalar for x in v]

# Complex types
def process(data: Dict[str, Union[int, str]]) -> None:
    pass

# Optional and default values
def greet(name: Optional[str] = None) -> str:
    if name is None:
        name = "World"
    return f"Hello, {name}"

# Function types
def apply(func: Callable[[int], int], value: int) -> int:
    return func(value)

# Generic types
from typing import TypeVar, Generic

T = TypeVar('T')

class Box(Generic[T]):
    def __init__(self, value: T):
        self.value = value
    
    def get(self) -> T:
        return self.value

# Protocol for structural typing
from typing import Protocol

class Drawable(Protocol):
    def draw(self) -> None:
        pass

def render(obj: Drawable) -> None:
    obj.draw()

# Type checking with mypy
# $ mypy script.py
```

### Debugging and Profiling
```python
# Debugging with pdb
import pdb
pdb.set_trace()                  # Breakpoint (Python 3.7 and earlier)
breakpoint()                     # Breakpoint (Python 3.7+)

# Common pdb commands:
# n (next) - Execute the current line
# s (step) - Step into function call
# c (continue) - Continue execution until next breakpoint
# p variable - Print value of variable
# q (quit) - Quit debugger
# l (list) - Show source code around current line
# w (where) - Show call stack

# Assertions
assert x > 0, "x must be positive"

# Logging
import logging
logging.basicConfig(level=logging.INFO,
                   format='%(asctime)s - %(name)s - %(levelname)s - %(message)s')
logger = logging.getLogger(__name__)
logger.info("Processing started")
logger.warning("Warning: resource low")
logger.error("Error occurred", exc_info=True)

# Timing code execution
import time
start_time = time.time()
# Code to time
end_time = time.time()
execution_time = end_time - start_time

# Using timeit for accurate benchmarking
import timeit
timeit.timeit('"-".join(str(n) for n in range(100))', number=10000)

# Profiling with cProfile
import cProfile
cProfile.run('function_to_profile()')

# Memory profiling
# pip install memory_profiler
from memory_profiler import profile

@profile
def my_function():
    # Function code
    pass

# Line profiler
# pip install line_profiler
@profile
def my_function():
    # Function code
    pass
# Run with: kernprof -l -v script.py
```

## Standard Library Highlights

### Working with Files and Directories
```python
# File I/O
with open("file.txt", "r") as f:
    content = f.read()             # Read entire file
    
with open("file.txt", "r") as f:
    for line in f:                  # Read line by line
        print(line.strip())

with open("file.txt", "w") as f:
    f.write("Hello, world!\n")      # Write to file
    
with open("file.txt", "a") as f:
    f.write("Appended text\n")      # Append to file

# Binary files
with open("file.bin", "rb") as f:
    binary_data = f.read()

with open("file.bin", "wb") as f:
    f.write(b"\x00\x01\x02")

# File operations
import os

os.rename("old.txt", "new.txt")     # Rename file
os.remove("file.txt")               # Delete file
os.path.exists("file.txt")          # Check if file exists
os.path.getsize("file.txt")         # Get file size
os.path.abspath("file.txt")         # Get absolute path
os.path.join("dir", "file.txt")     # Join paths properly

# Directory operations
os.mkdir("new_dir")                 # Create directory
os.makedirs("path/to/new_dir")      # Create directory with parents
os.rmdir("dir")                     # Remove empty directory
os.listdir("dir")                   # List directory contents
os.walk("dir")                      # Walk directory tree

# Safer file and directory handling
import shutil

shutil.copy("src.txt", "dst.txt")   # Copy file
shutil.copytree("src_dir", "dst_dir")  # Copy directory tree
shutil.rmtree("dir")                # Remove directory and contents
shutil.move("src", "dst")           # Move file or directory

# Path operations (pathlib)
from pathlib import Path

path = Path("file.txt")
path.exists()                       # Check if file exists
path.is_file()                      # Check if it's a file
path.is_dir()                       # Check if it's a directory
path.name                           # File name with extension
path.stem                           # File name without extension
path.suffix                         # File extension
path.parent                         # Parent directory
path.absolute()                     # Absolute path

# Reading/writing with pathlib
content = path.read_text()          # Read file text
path.write_text("Hello, world!")    # Write text to file
binary_data = path.read_bytes()     # Read binary data
path.write_bytes(b"\x00\x01\x02")   # Write binary data

# Directory operations with pathlib
path = Path("dir")
path.mkdir(exist_ok=True)           # Create directory
path.rmdir()                        # Remove directory
[p for p in path.iterdir()]         # List contents
[p for p in path.glob("*.txt")]     # Find files with pattern
```

### Data Processing
```python
# CSV files
import csv

# Reading CSV
with open("data.csv", "r", newline="") as file:
    reader = csv.reader(file)
    for row in reader:
        print(row)

# Reading CSV with headers
with open("data.csv", "r", newline="") as file:
    reader = csv.DictReader(file)
    for row in reader:
        print(row["column_name"])

# Writing CSV
with open("output.csv", "w", newline="") as file:
    writer = csv.writer(file)
    writer.writerow(["Name", "Age"])
    writer.writerow(["Alice", 30])

# Writing CSV with headers
with open("output.csv", "w", newline="") as file:
    fieldnames = ["Name", "Age"]
    writer = csv.DictWriter(file, fieldnames=fieldnames)
    writer.writeheader()
    writer.writerow({"Name": "Alice", "Age": 30})

# JSON
import json

# Parsing JSON
data = '{"name": "Alice", "age": 30}'
person = json.loads(data)
print(person["name"])

# Creating JSON
person = {"name": "Alice", "age": 30}
json_string = json.dumps(person)
print(json_string)

# Reading JSON file
with open("data.json", "r") as file:
    data = json.load(file)

# Writing JSON file
with open("output.json", "w") as file:
    json.dump(data, file, indent=4)

# XML
import xml.etree.ElementTree as ET

# Parsing XML
tree = ET.parse("data.xml")
root = tree.getroot()
for child in root:
    print(child.tag, child.attrib)

# XML from string
root = ET.fromstring("<data><item>text</item></data>")
print(root[0].text)

# Creating XML
root = ET.Element("data")
item = ET.SubElement(root, "item")
item.text = "text"
item.set("name", "value")
tree = ET.ElementTree(root)
tree.write("output.xml")

# Date and time
from datetime import datetime, date, time, timedelta

now = datetime.now()              # Current date and time
today = date.today()              # Current date
 
# Creating dates and times
dt = datetime(2023, 1, 15, 10, 30)  # Year, month, day, hour, minute
d = date(2023, 1, 15)              # Year, month, day
t = time(10, 30, 0)                # Hour, minute, second

# Formatting and parsing
formatted = now.strftime("%Y-%m-%d %H:%M:%S")
parsed = datetime.strptime("2023-01-15 10:30:00", "%Y-%m-%d %H:%M:%S")

# Date arithmetic
tomorrow = today + timedelta(days=1)
two_hours_later = now + timedelta(hours=2)
diff = date(2023, 2, 1) - date(2023, 1, 1)  # timedelta
```

### Networking and Web
```python
# HTTP requests with urllib
from urllib.request import urlopen, Request
from urllib.parse import urlencode

url = "https://api.example.com/data"
response = urlopen(url)
data = response.read().decode("utf-8")

# POST request
post_data = urlencode({"key": "value"}).encode("utf-8")
req = Request(url, data=post_data, method="POST")
response = urlopen(req)

# Better HTTP with requests (third-party)
import requests

# GET request
response = requests.get("https://api.example.com/data")
data = response.json()

# POST request
response = requests.post("https://api.example.com/data", 
                         json={"key": "value"},
                         headers={"Content-Type": "application/json"})

# Building web applications with Flask (third-party)
from flask import Flask, request, jsonify

app = Flask(__name__)

@app.route("/", methods=["GET"])
def hello():
    return "Hello, World!"

@app.route("/api/data", methods=["POST"])
def process_data():
    data = request.json
    # Process data
    return jsonify({"status": "success", "data": data})

if __name__ == "__main__":
    app.run(debug=True)

# Socket programming
import socket

# Server
server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
server_socket.bind(("localhost", 8080))
server_socket.listen(5)

client_socket, address = server_socket.accept()
data = client_socket.recv(1024).decode("utf-8")
client_socket.send("Received".encode("utf-8"))
client_socket.close()
server_socket.close()

# Client
client_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
client_socket.connect(("localhost", 8080))
client_socket.send("Hello".encode("utf-8"))
response = client_socket.recv(1024).decode("utf-8")
client_socket.close()

# Email
import smtplib
from email.mime.text import MIMEText
from email.mime.multipart import MIMEMultipart

msg = MIMEMultipart()
msg["From"] = "sender@example.com"
msg["To"] = "recipient@example.com"
msg["Subject"] = "Test Email"

body = "This is a test email."
msg.attach(MIMEText(body, "plain"))

server = smtplib.SMTP("smtp.example.com", 587)
server.starttls()
server.login("username", "password")
server.send_message(msg)
server.quit()
```

### System Programming
```python
# Command-line arguments
import sys

script_name = sys.argv[0]
arguments = sys.argv[1:]

# Better argument parsing
import argparse

parser = argparse.ArgumentParser(description="Process some integers.")
parser.add_argument("integers", metavar="N", type=int, nargs="+",
                    help="an integer for the accumulator")
parser.add_argument("--sum", dest="accumulate", action="store_const",
                    const=sum, default=max,
                    help="sum the integers (default: find the max)")
args = parser.parse_args()
print(args.accumulate(args.integers))

# Environment variables
import os

os.environ["MY_VAR"] = "value"
value = os.environ.get("MY_VAR", "default")

# Subprocess
import subprocess

# Run command and wait for it to complete
result = subprocess.run(["ls", "-l"], capture_output=True, text=True)
print(result.stdout)

# Check if command ran successfully
try:
    subprocess.run(["command"], check=True)
except subprocess.CalledProcessError as e:
    print(f"Command failed with exit code {e.returncode}")

# Communicate with a process
process = subprocess.Popen(["command"], 
                          stdin=subprocess.PIPE,
                          stdout=subprocess.PIPE,
                          text=True)
stdout, stderr = process.communicate(input="input data")

# Signal handling
import signal

def handler(signum, frame):
    print("Signal received:", signum)

signal.signal(signal.SIGINT, handler)
signal.signal(signal.SIGTERM, handler)

# Wait for signal
signal.pause()

# System information
import platform

platform.system()                 # 'Linux', 'Windows', 'Darwin'
platform.release()                # '5.4.0-42-generic'
platform.version()                # Details about OS version
platform.machine()                # 'x86_64'
platform.processor()              # 'x86_64'
platform.python_version()         # '3.9.5'
```

### Testing
```python
# Unit testing
import unittest

class TestMathFunctions(unittest.TestCase):
    def setUp(self):
        # Set up test environment
        pass
    
    def tearDown(self):
        # Clean up after test
        pass
    
    def test_addition(self):
        self.assertEqual(1 + 1, 2)
    
    def test_division(self):
        self.assertRaises(ZeroDivisionError, lambda: 1 / 0)
    
    @unittest.skip("demonstrating skipping")
    def test_skipped(self):
        self.fail("shouldn't run this")

if __name__ == "__main__":
    unittest.main()

# Assertions
self.assertEqual(a, b)            # a == b
self.assertNotEqual(a, b)         # a != b
self.assertTrue(x)                # bool(x) is True
self.assertFalse(x)               # bool(x) is False
self.assertIs(a, b)               # a is b
self.assertIsNot(a, b)            # a is not b
self.assertIsNone(x)              # x is None
self.assertIsNotNone(x)           # x is not None
self.assertIn(a, b)               # a in b
self.assertNotIn(a, b)            # a not in b
self.assertIsInstance(a, b)       # isinstance(a, b)
self.assertRaises(ExcType, func, *args, **kwargs)  # Checks for exception

# Pytest (third-party)
# pip install pytest
import pytest

def test_function():
    assert 1 + 1 == 2

def test_exception():
    with pytest.raises(ZeroDivisionError):
        1 / 0

# Fixtures in pytest
@pytest.fixture
def example_data():
    return {"key": "value"}

def test_with_data(example_data):
    assert example_data["key"] == "value"

# Parametrized tests
@pytest.mark.parametrize("input, expected", [
    (1, 1),
    (2, 4),
    (3, 9),
    (4, 16),
])
def test_square(input, expected):
    assert input ** 2 == expected

# Mocking
from unittest.mock import Mock, patch

def test_with_mock():
    mock = Mock()
    mock.method.return_value = "mocked"
    assert mock.method() == "mocked"

@patch("module.function")
def test_patched(mock_function):
    mock_function.return_value = "mocked"
    # The real function is replaced by mock_function
    from module import function
    assert function() == "mocked"
```

## Best Practices and Patterns

### Code Style and Documentation
```python
# PEP 8 style guide
# Use 4 spaces for indentation
# Maximum line length of 79 characters
# Two blank lines between top-level functions and classes
# One blank line between methods in a class
# Use lowercase with underscores for functions and variables: my_function
# Use CamelCase for classes: MyClass
# Constants in ALL_CAPS: MAX_VALUE

# Docstrings
def function(arg1, arg2):
    """
    Function description.
    
    Args:
        arg1: Description of arg1
        arg2: Description of arg2
        
    Returns:
        Description of return value
        
    Raises:
        ExceptionType: When and why exception is raised
    """
    pass

# Type hints in docstrings (for Python 2 or when not using annotations)
def function(name, age):
    """
    Args:
        name (str): Person's name
        age (int): Person's age
        
    Returns:
        bool: Whether the person is an adult
    """
    return age >= 18
```

### Design Patterns
```python
# Singleton pattern
class Singleton:
    _instance = None
    
    def __new__(cls, *args, **kwargs):
        if cls._instance is None:
            cls._instance = super().__new__(cls)
        return cls._instance

# Factory pattern
class Animal:
    def speak(self):
        pass

class Dog(Animal):
    def speak(self):
        return "Woof!"

class Cat(Animal):
    def speak(self):
        return "Meow!"

class AnimalFactory:
    def create_animal(self, animal_type):
        if animal_type == "dog":
            return Dog()
        elif animal_type == "cat":
            return Cat()
        else:
            raise ValueError(f"Unknown animal type: {animal_type}")

# Observer pattern
class Subject:
    def __init__(self):
        self._observers = []
    
    def register_observer(self, observer):
        self._observers.append(observer)
    
    def remove_observer(self, observer):
        self._observers.remove(observer)
    
    def notify_observers(self, data):
        for observer in self._observers:
            observer.update(data)

class Observer:
    def update(self, data):
        pass

# Decorator pattern (using classes)
class Component:
    def operation(self):
        pass

class ConcreteComponent(Component):
    def operation(self):
        return "ConcreteComponent"

class Decorator(Component):
    def __init__(self, component):
        self._component = component
    
    def operation(self):
        return self._component.operation()

class ConcreteDecorator(Decorator):
    def operation(self):
        return f"ConcreteDecorator({super().operation()})"

# Strategy pattern
class Strategy:
    def execute(self, data):
        pass

class ConcreteStrategyA(Strategy):
    def execute(self, data):
        return sorted(data)

class ConcreteStrategyB(Strategy):
    def execute(self, data):
        return sorted(data, reverse=True)

class Context:
    def __init__(self, strategy):
        self._strategy = strategy
    
    def set_strategy(self, strategy):
        self._strategy = strategy
    
    def execute_strategy(self, data):
        return self._strategy.execute(data)
```

### Project Structure
```
my_project/
│
├── README.md              # Project documentation
├── LICENSE                # License information
├── setup.py               # Package installation script
├── requirements.txt       # Dependencies
│
├── my_package/            # Main package
│   ├── __init__.py        # Makes directory a package
│   ├── module1.py         # Module file
│   ├── module2.py         # Module file
│   ├── subpackage/        # Nested package
│   │   ├── __init__.py
│   │   └── submodule.py
│   └── data/              # Data files
│
├── tests/                 # Unit tests
│   ├── __init__.py
│   ├── test_module1.py
│   └── test_module2.py
│
├── docs/                  # Documentation
│   ├── conf.py            # Sphinx configuration
│   └── index.rst          # Documentation index
│
└── examples/              # Example usage scripts
    └── example1.py
```

### Performance Optimization
```python
# Use built-in functions and types
# They're implemented in C and are much faster
sum([1, 2, 3, 4])             # Better than manual loop sum

# List comprehensions vs. loops
# List comprehensions are usually faster
squares = [x**2 for x in range(1000)]  # Faster than appending in a loop

# Generator expressions for large datasets
# Avoid loading everything into memory
sum(x**2 for x in range(1000000))  # Uses minimal memory

# Use proper data structures
# Lists for ordered collections with frequent modification
# Tuples for immutable sequences
# Sets for unique elements and fast membership tests
# Dictionaries for key-value lookup

# Joins are faster than concatenation for many strings
" ".join(["Hello", "world"])  # Better than "Hello" + " " + "world"

# Avoid global variables inside loops
x = 0
def func():
    global x  # Global lookups are slow
    x += 1

# Better approach: use local variables
def func(x):
    return x + 1

# Use local variables for module functions
import math
def calc_areas(radii):
    # Slow: global lookup for math.pi in every iteration
    areas = [math.pi * r**2 for r in radii]
    
    # Faster: local reference
    pi = math.pi
    areas = [pi * r**2 for r in radii]
    
    # Fastest: avoid function call
    areas = [3.141592653589793 * r**2 for r in radii]

# Avoid unnecessary attribute lookups in loops
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

def distance_slow(points):
    # Slow: attribute lookup in each iteration
    return [p.x**2 + p.y**2 for p in points]

def distance_fast(points):
    # Faster: method with local references
    result = []
    for p in points:
        x, y = p.x, p.y  # Local variables
        result.append(x**2 + y**2)
    return result

# Use slots for memory-efficient classes
class Point:
    __slots__ = ('x', 'y')  # Reduces memory usage
    
    def __init__(self, x, y):
        self.x = x
        self.y = y

# Caching with functools.lru_cache
from functools import lru_cache

@lru_cache(maxsize=None)
def fibonacci(n):
    if n < 2:
        return n
    return fibonacci(n-1) + fibonacci(n-2)
```

### Security Considerations
```python
# Input validation
def process_age(age_str):
    try:
        age = int(age_str)
        if age < 0 or age > 120:
            raise ValueError("Age must be between 0 and 120")
        return age
    except ValueError:
        raise ValueError("Invalid age format")

# SQL injection prevention
import sqlite3

# Vulnerable:
def get_user_unsafe(name):
    conn = sqlite3.connect("database.db")
    cursor = conn.cursor()
    cursor.execute(f"SELECT * FROM users WHERE name = '{name}'")  # Unsafe
    return cursor.fetchone()

# Safe (using parameterized queries):
def get_user_safe(name):
    conn = sqlite3.connect("database.db")
    cursor = conn.cursor()
    cursor.execute("SELECT * FROM users WHERE name = ?", (name,))  # Safe
    return cursor.fetchone()

# Command injection prevention
import subprocess

# Vulnerable:
def run_command_unsafe(command):
    return subprocess.check_output(command, shell=True)  # Unsafe

# Safe:
def run_command_safe(command, *args):
    return subprocess.check_output([command] + list(args), shell=False)  # Safe

# Secure password handling
import hashlib
import os

def hash_password(password):
    # Generate a random salt
    salt = os.urandom(32)
    # Hash the password with the salt
    key = hashlib.pbkdf2_hmac('sha256', password.encode('utf-8'), salt, 100000)
    # Store both the salt and key
    return salt + key

def verify_password(stored_password, provided_password):
    salt = stored_password[:32]  # Extract the salt
    stored_key = stored_password[32:]  # Extract the key
    # Hash the provided password with the same salt
    key = hashlib.pbkdf2_hmac('sha256', provided_password.encode('utf-8'), salt, 100000)
    # Compare the generated key with the stored key
    return key == stored_key

# Better password handling (with third-party library)
# pip install bcrypt
import bcrypt

def hash_password(password):
    # Generate a salt and hash the password
    salt = bcrypt.gensalt()
    hashed = bcrypt.hashpw(password.encode('utf-8'), salt)
    return hashed

def verify_password(stored_password, provided_password):
    # Check the password
    return bcrypt.checkpw(provided_password.encode('utf-8'), stored_password)

# Safer temporary files
import tempfile

with tempfile.NamedTemporaryFile() as temp:
    temp.write(b"Sensitive data")
    # File is automatically deleted when the context manager exits
```

### Package Management
```python
# Creating a package
# setup.py
from setuptools import setup, find_packages

setup(
    name="mypackage",
    version="0.1.0",
    packages=find_packages(),
    install_requires=[
        "numpy>=1.18.0",
        "pandas>=1.0.0",
    ],
    author="Your Name",
    author_email="your.email@example.com",
    description="A short description",
    long_description=open("README.md").read(),
    long_description_content_type="text/markdown",
    url="https://github.com/yourusername/mypackage",
    classifiers=[
        "Programming Language :: Python :: 3",
        "License :: OSI Approved :: MIT License",
        "Operating System :: OS Independent",
    ],
    python_requires=">=3.6",
)

# Installing package in development mode
# pip install -e .

# Creating a virtual environment
# Python 3.3+
# python -m venv myenv

# Activating virtual environment
# Windows: myenv\Scripts\activate
# Unix/MacOS: source myenv/bin/activate

# Installing packages
# pip install package_name
# pip install -r requirements.txt

# Generating requirements.txt
# pip freeze > requirements.txt

# Using pipenv (combines pip and virtualenv)
# pip install pipenv
# pipenv install package_name
# pipenv shell

# Using poetry (modern dependency management)
# pip install poetry
# poetry init
# poetry add package_name
# poetry shell
```

## Additional Resources
```python
# Standard library documentation: https://docs.python.org/3/library/
# PEP 8 style guide: https://www.python.org/dev/peps/pep-0008/
# Python Package Index (PyPI): https://pypi.org/
# Real Python tutorials: https://realpython.com/
# Python Documentation: https://docs.python.org/3/
```
