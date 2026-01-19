[Python - Basics.md](https://github.com/user-attachments/files/24721857/Python.-.Basics.md)
Hello visiter this is my full guide of python crash course that  i kept making it while preparing to KAUST AI learning path program in KSA.
And this file contains these courses:
- Python basics
- Python Function,files and dictionary
- Python OOP
- Python data processing and files
- Python introduction to Data 

>Note: this file is meant to be personal so u may see some issues so never mind when u see them

> Note: for better view and navigation between sections and headers , use obsidian or any `.md` app   
# What is python ?
Python is a high-level, interpreted, and general-purpose dynamic programming language that focuses on code readability. It generally has small programs when compared to [Java](https://www.geeksforgeeks.org/java/java/) and C.
## Advantages
1. ****Presence of third-party modules:**** Python has a rich ecosystem of third-party modules and libraries that extend its functionality for various tasks.
2. ****Extensive support libraries:**** Python boasts extensive support libraries like NumPy for numerical calculations and Pandas for data analytics, making it suitable for scientific and data-related applications. 
3. ****Open source and large active community base:**** Python is open source, and it has a large and active community that contributes to its development and provides support.
4. ****Versatile, easy to read, learn, and write:**** Python is known for its simplicity and readability, making it an excellent choice for both beginners and experienced programmers.
5. ****Dynamically typed language:**** Python is dynamically typed, meaning you don't need to declare data types explicitly, making it flexible but still reliable.
6. ****Object-Oriented and Procedural programming language:**** Python supports both object-oriented and procedural programming, providing versatility in coding styles.
7. ****Portable and interactive:**** Python is portable across operating systems and interactive, allowing real-time code execution and testing.

## Disadvantages
1. ****Performance:**** Python is an interpreted language, which means that it can be slower than compiled languages like C or Java. This can be an issue for performance-intensive tasks.
2. ****Global Interpreter Lock:**** The Global Interpreter Lock (GIL) is a mechanism in Python that prevents multiple threads from executing Python code at once. This can limit the parallelism and concurrency of some applications.
3. ****Memory consumption:**** Python can consume a lot of memory, especially when working with large datasets or running complex algorithms.
4. ****Dynamically typed:**** Python is a dynamically typed language, which means that the types of variables can change at runtime. This can make it more difficult to catch errors and can lead to bugs.
5. ****Packaging and versioning:**** Python has a large number of packages and libraries, which can sometimes lead to versioning issues and package conflicts.
6. ****Lack of strictness:**** Python's flexibility can sometimes be a double-edged sword. While it can be great for rapid development and prototyping, it can also lead to code that is difficult to read and maintain.
7. ****Not Ideal for System Programming / Embedded Systems / Mobile Development / Frontend:**** For real-time constraints, low-level systems programming (drivers, OS kernels, embedded microcontrollers), or very tight performance latency, Python typically isn’t used. There is comparatively weak support or adoption for mobile apps or browser-side code

# Input and output
## Define

Python's input() function is used to take user input. By default, it returns the user input in form of a string.

>Note: it takes string by default so u have to specify what u r taking by wrapping the input function with typecasting methods like
```python
x = input() # this takes string
y = int(input()) # this takes int
z = float(input()) # this takes float 
# and so on ....
```

****Example:****
```python
name = input("Enter your name: ") 
print("Hello,", name, "! Welcome!")
```
****Output****
```
 Enter your name: GeeksforGeeks  
 Hello, GeeksforGeeks ! Welcome!
```


## Error Types and how they looks
### SyntaxError
**What is it:** Error in the structure or grammar of your code. Python cannot understand what you wrote.

**Why it happens:** Missing colons, wrong indentation, unclosed brackets/quotes, typos in keywords.

**Example:**

python

```python
# ❌ Wrong - missing colon
if x > 5
    print("Greater")

# ✅ Correct
if x > 5:
    print("Greater")
```
### NameError

**What is it:** Trying to use a variable or function that doesn't exist or hasn't been defined yet.

**Why it happens:** Typo in variable name, forgot to define variable, or using variable before assignment.

**Example:**

python

```python
# ❌ Wrong - variable not defined
print(name)

# ✅ Correct
name = "Rawi"
print(name)
```
### TypeError

**What is it:** Operation or function applied to wrong data type.

**Why it happens:** Mixing incompatible types (like adding string to integer), calling non-function, wrong number of arguments.

**Example:**

python

```python
# ❌ Wrong - can't add string and int
result = "5" + 3

# ✅ Correct
result = int("5") + 3  # Convert string to int first
# OR
result = "5" + str(3)  # Convert int to string
```

### IndexError

**What is it:** Trying to access an index that doesn't exist in a list/string.

**Why it happens:** Index is too large, using negative index incorrectly, or accessing empty list.

**Example:**

python

```python
# ❌ Wrong - list only has 3 elements (index 0, 1, 2)
numbers = [10, 20, 30]
print(numbers[3])

# ✅ Correct
print(numbers[2])  # Last element
# OR
print(numbers[-1])  # Last element using negative index
```

### KeyError

**What is it:** Trying to access a dictionary key that doesn't exist.

**Why it happens:** Typo in key name, key was never added to dictionary, or key was deleted.

**Example:**

python

```python
# ❌ Wrong - 'age' key doesn't exist
person = {"name": "Rawi", "city": "Riyadh"}
print(person["age"])

# ✅ Correct
print(person.get("age", "Not specified"))  # Use .get() with default
# OR
person["age"] = 20
print(person["age"])
```

### ZeroDivisionError

**What is it:** Attempting to divide by zero.

**Why it happens:** Denominator is zero, or calculation results in zero before division.

**Example:**

python

```python
# ❌ Wrong - dividing by zero
result = 10 / 0

# ✅ Correct - check before dividing
denominator = 0
if denominator != 0:
    result = 10 / denominator
else:
    print("Cannot divide by zero")
```

### IndentationError

**What is it:** Incorrect spacing/indentation in your code.

**Why it happens:** Mixing tabs and spaces, wrong indentation level, inconsistent spacing.

**Example:**

python

```python
# ❌ Wrong - inconsistent indentation
def greet():
print("Hello")  # Should be indented
    print("World")  # Wrong indentation level

# ✅ Correct
def greet():
    print("Hello")
    print("World")
```

### AttributeError

**What is it:** Trying to access an attribute/method that doesn't exist for that object.

**Why it happens:** Typo in method name, using wrong method for data type, or object doesn't have that attribute.

**Example:**

python

```python
# ❌ Wrong - strings don't have .append() method
text = "hello"
text.append("!")

# ✅ Correct
text = "hello"
text = text + "!"  # Use concatenation for strings
# OR
my_list = ["hello"]
my_list.append("!")  
```
### ValueError
exception that occurs when a function receives an argument of the correct data type but an inappropriate value.
like these examples:
```python
int("hello")  # Raises ValueError: invalid literal for int() with base 10: 'hello'
```

```python
a, b = [1, 2, 3]  # Raises ValueError: too many values to unpack (expected 2)

# he's trying to fit 3 values in 2 variables which is 
# un-logical and wrong !!
```

```python
# input was 1.5
x = int(input())
print(x)

# cant show input cuz there's a conflict between type casting and input enterd 
#int() wont accept float 
```

## String formatting
### F-Strings
**How to use f-strings?***:
Simply prefix a string with f and place variables or expressions inside {}. This works like str.format(), but in a more concise and readable way.

code example:
```python
variable = "data"
print (f"text you want to type  {variable}")


val = 'Geeks'
print(f"{val}for{val} is a portal for {val}.")


name = 'Om'
age = 22
print(f"Hello, My name is {name} and I'm {age} years old.")
```


## Take Multiple Input in Python

We are taking multiple input from the user in a single line, splitting the values entered by the user into separate variables for each value using the [split() method](https://www.geeksforgeeks.org/python/python-string-split/). Then, it prints the values with corresponding labels, either two or three, based on the number of inputs provided by the user.

```python
# taking two inputs at a time 
x, y = input("Enter two values: ").split() 
print("Number of boys: ", x) 
print("Number of girls: ", y)

# taking three inputs at a time 
x, y, z = input("Enter three values: ").split() 
print("Total number of students: ", x)
print("Number of boys is : ", y) 
print("Number of girls is : ", z)`
```

****Output****
```
Enter two values: 5 10  
Number of boys: 5  
Number of girls: 10  
Enter three values: 5 10 15  
Total number of students: 5  
Number of boys is : 10  
Number of girls is : 15
```

### Printing Variables

We can use the print() function to print single and multiple variables. We can print multiple variables by separating them with commas. ****Example:****

Single variable

```python
s = "Bob"
print(s)
```

Multiple Variables

```python
s = "Alice"
age = 25
city = "New York"

print(s, age, city)
```
 output:
 ```
Bob
Alice 25 New York
 ```


## TypeCasting and DataTypes
By default input() function helps in taking user input as string. If any user wants to take input as int or float, we just need to [typecast](https://www.geeksforgeeks.org/python/type-casting-in-python/) it.

The code prompts the user to input an integer representing the number of roses, converts the input to an integer using typecasting and then prints the integer value.
```python
# Taking input as int 
# Typecasting to int 
n = int(input("How many roses?: "))
print(n)
```
****Output****
```
How many roses?: 88  
88
```
### Print Float/Decimal Number in Python

The code prompts the user to input the price of each rose as a floating-point number, converts the input to a float using typecasting and then prints the price.

```python
# Taking input as float
# Typecasting to float
price = float(input("Price of each rose?: ")) 
print(price)
```
****Output****
```
 Price of each rose?: 50.3050.3  
 50.3050.3
```

### Find DataType of Input in Python

In the given example, we are printing the type of variable x. We will determine the type of an object in Python.

```python
a = "Hello World"
b = 10
c = 11.22
d = ("Geeks", "for", "Geeks")
e = ["Geeks", "for", "Geeks"]
f = {"Geeks": 1, "for":2, "Geeks":3}

print(type(a))
print(type(b))
print(type(c))
print(type(d))
print(type(e))
print(type(f))
```
  
**Output**
```
<class 'str'>
<class 'int'>
<class 'float'>
<class 'tuple'>
<class 'list'>
<class 'dict'>
```

# Variables
the only special thing for me was :
```python
#declaring a variable
_number = 10 ## this is valid !
```
## Type Casting 
[Type casting](https://www.geeksforgeeks.org/python/type-casting-in-python/) refers to the process of converting the value of one data type into another. Python provides several built-in functions to facilitate casting, including int(), float() and str() among others.
Some casting functions :
- `int()` : converts values  to int
- `float()` : converts values  to float 
- `str()` : converts values  to strings

```python
s = "10"
n = int(s)
b = float(s)
a = str(n) 

print(s , type(s))
print(n , type(n))
print(b , type(b))
print(a , type(a))
```
output:
```
10 <class 'str'>
10 <class 'int'>
10.0 <class 'float'>
10 <class 'str'>
```

# Operators
goddamn u know them all !! am just listing what's special

## Arithmetic
```python
# Variables
a = 15
b = 4

# Addition
print("Addition:", a + b)  

# Subtraction
print("Subtraction:", a - b) 

# Multiplication
print("Multiplication:", a * b)  

# Division
print("Division:", a / b) 

# Floor Division
print("Floor Division:", a // b)  

# Modulus
print("Modulus:", a % b) 

# Exponentiation
print("Exponentiation:", a ** b)
```

## Comparison
```python
a = 13
b = 33

print(a > b)
print(a < b)
print(a == b)
print(a != b)
print(a >= b)
print(a <= b)
```

## Logical
```python
a = True
b = False
print(a and b)
print(a or b)
print(not a)
```

## Special about python operators

### Identity operators
In Python, ****is**** and ****is not**** are the [identity operators](https://www.geeksforgeeks.org/python/python-membership-identity-operators-not-not/) both are used to check if two values are located on the same part of the memory. Two variables that are equal do not imply that they are identical.

> ****is**** True if the operands are identical  
> ****is not**** True if the operands are not identical

```python
a = 10
b = 20
c = a

print(a is not b)
print(a is c)
```

**Output**
```
True
True
```

### Membership Operators

In Python, ****in**** and ****not in**** are the [membership operators](https://www.geeksforgeeks.org/python/python-membership-identity-operators-not-not/) that are used to test whether a value or variable is in a sequence.

> ****in**** True if value is found in the sequence  
> ****not in**** True if value is not found in the sequence

```python
x = 24
y = 20
list = [10, 20, 30, 40, 50]

if (x not in list):
    print("x is NOT present in given list")

else:
    print("x is present in given list")

if (y in list):
    print("y is present in given list")
    
else:
    print("y is NOT present in given list")
```
  
**Output**
```
x is NOT present in given list
y is present in given list
```


### Precedence and Associativity of Operators

In Python, [Operator precedence and associativity](https://www.geeksforgeeks.org/python/precedence-and-associativity-of-operators-in-python/) determine the priorities of the operator.

>**uses real operators priority and left-right !!**
### Operator Precedence

This is used in an expression with more than one operator with different precedence to determine which operation to perform first.

```python
expr = 10 + 20 * 30 #uses real operators priority and left-right !!
print(expr)
name = "Alex"
age = 0

if name == "Alex" or name == "John" and age >= 2:
    print("Hello! Welcome.")

else:
    print("Good Bye!!")
```
**Output**
```
610
Hello! Welcome.
```

### Operator Associativity

If an expression contains two or more operators with the same precedence then Operator Associativity is used to determine. It can either be Left to Right or from Right to Left.

```python
print(100 / 10 * 10)
print(5 - 2 + 3)
print(5 - (2 + 3))
print(2 ** 3 ** 2)
```

**Output**
```
100.0
6
0
512
```

# DataTypes
## Introduction
all data type that we in-sha-allah gonna cover in this section
![[Screenshot From 2025-11-11 17-55-41.png]]
The following are standard or built-in data types in Python:

- ****Numeric:**** [int](https://www.geeksforgeeks.org/python/python-int-function/), [float](https://www.geeksforgeeks.org/python/python-float-type-and-its-methods/), [complex](https://www.geeksforgeeks.org/python/python-complex-function/)
- ****Sequence Type:**** [string](https://www.geeksforgeeks.org/python/python-string/), [list](https://www.geeksforgeeks.org/python/python-lists/), [tuple](https://www.geeksforgeeks.org/python/python-tuples/)
- ****Mapping Type:**** [dict](https://www.geeksforgeeks.org/python/python-dictionary/)
- ****Boolean:**** [bool](https://www.geeksforgeeks.org/python/boolean-data-type-in-python/)
- ****Set Type:**** [set](https://www.geeksforgeeks.org/python/python-sets/), [frozenset](https://www.geeksforgeeks.org/python/frozenset-in-python/)
- ****Binary Types:**** [bytes](https://www.geeksforgeeks.org/python/python-bytes-method/), [bytearray](https://www.geeksforgeeks.org/python/python-bytearray-function/), [memoryview](https://www.geeksforgeeks.org/python/memoryview-in-python/)

quick example of  how they  look:
```python
x = 50  # int
x = 60.5  # float
x = "Hello World"  # string
x = ["geeks", "for", "geeks"]  # list 
x = ("geeks", "for", "geeks")  # tuple
```

## Numeric 
[Python numbers](https://www.geeksforgeeks.org/python/python-numbers/) represent data that has a numeric value. A numeric value can be an integer, a floating number or even a complex number.

- **Integers**: value represented by int class , have sign (negative , positive) , no fractions\float point
- **Float**: value represented by float class, have sign, have floating point
- **complex**: It is represented by a complex class.
   It is specified as (real part) + (imaginary part)j. For example - 2+3j
   
> Note: j is meant by  "i" in math which is $\sqrt{-1}$

code example:
```python
a = 5
print(type(a))

b = 5.0
print(type(b))

c = 2 + 4j
print(type(c))

'''
output:
<class 'int'>
<class 'float'>
<class 'complex'>
'''
```


## Sequence
A sequence is an ordered collection of items, which can be of similar or different data types. Sequences allow storing of multiple values in an organized and efficient fashion. 
There are several sequence data types of Python:

### String sequences
Python Strings are arrays of bytes representing Unicode characters. 
In Python, ==there is no character data type, a character is a string of length one==. It is represented by str class.
and u can access individual characters by indexs

code example:
```python
s = 'Welcome to the Geeks World'
print(s)

# check data type 
print(type(s))

# access string with index
print(s[1])
print(s[2])
print(s[-1])

'''
output

Welcome to the Geeks World
<class 'str'>
e
l
d
'''
```

### List sequences
Lists are similar to arrays found in other languages. They are an ordered and mutable collection of items. ==It is very flexible as items in a list do not need to be of the same type.==

code example:
```python
# Empty list
a = []

# list with int values
a = [1, 2, 3]
print(a)

# list with mixed values int and String
b = ["Geeks", "For", "Geeks", 4, 5]
print(b)

'''
output:

[1, 2, 3]
['Geeks', 'For', 'Geeks', 4, 5]
'''
```

**Accessing list items using negative indexes:**
In Python, negative sequence indexes represent positions from end of the array.

code Example:
```python
a = ["Geeks", "For", "peeks"]
print("Accessing element from the list")
print(a[0])
print(a[2])

print("Accessing element using negative indexing")
print(a[-1])
print(a[-3])

'''
output

Accessing element from the list
Geeks
peeks
Accessing element using negative indexing
peeks
geeks
'''
```

### Tuple Sequences
Tuple is an ordered collection of Python objects. The only difference between a tuple and a list is that tuples are immutable. ==Tuples cannot be modified after it is created.==

In Python, tuples are created by placing a sequence of values separated by a ‘comma’ with or without the use of parentheses for grouping data sequence. Tuples can contain any number of elements and of any datatype (like strings, integers, lists, etc.).

>****Note:**** Tuples can also be created with a single element, but it is a bit tricky. Having one element in the parentheses is not sufficient, there must be a trailing ****‘comma’**** to make it a tuple.

code Example:
```python
# initiate empty tuple
tup1 = ()

tup2 = ('Geeks', 'For')
print("\nTuple with the use of String: ", tup2)

'''
output

Tuple with the use of String: ('Geeks', 'For')
'''
```

for Accessing it has the same thing as lists

## Boolean
it's a  godamn true and false ! 

ill just discuss what's special
### bool() Function

In Python, the bool() function is used to convert a value or expression to its corresponding Boolean value (True or False). In the example below the variable res will store the boolean value of False after the equality comparison takes place.
```python
# Returns False as x is None
x = None
print(bool(x))

# Returns False as x is an empty sequence
x = ()
print(bool(x))

# Returns False as x is an empty mapping
x = {}
print(bool(x))

# Returns False as x is 0
x = 0.0
print(bool(x))

# Returns True as x is a non empty string
x = 'GeeksforGeeks'
print(bool(x))

#output
False
False
False
False
True
```

## Set
In Python Data Types, Set is an unordered collection of data types that is iterable, mutable, and has no duplicate elements. The order of elements in a set is undefined though it may consist of various elements.

```python
s1 = set()
s1 = set("GeeksForGeeks")
print("Set with the use of String: ", s1)
s2 = set(["Geeks", "For", "Geeks"])
print("Set with the use of List: ", s2)
  '''
**Output**

Set with the use of String:  {'s', 'o', 'F', 'G', 'e', 'k', 'r'}
Set with the use of List:  {'Geeks', 'For'}
'''
```

cannot be accessed via index , u have to loop them 
##  Dictionary
A [dictionary](https://www.geeksforgeeks.org/python/python-dictionary/) in Python is a collection of data values, used to store data values like a map, unlike other Python Data Types, a Dictionary holds a key: value pair. Key-value is provided in dictionary to make it more optimized. Each key-value pair in a Dictionary is separated by a colon : , whereas each key is separated by a ‘comma’.

> ****Note**** - Dictionary keys are case sensitive, the same name but different cases of Key will be treated distinctly.

```python
# initialize empty dictionary
d = {}
d = {1: 'Geeks', 2: 'For', 3: 'Geeks'}
print(d)

# creating dictionary using dict() constructor
d1 = dict({1: 'Geeks', 2: 'For', 3: 'Geeks'})
print(d1)
**Output**

{1: 'Geeks', 2: 'For', 3: 'Geeks'}
{1: 'Geeks', 2: 'For', 3: 'Geeks'}
```
accessing:
```python
d = {1: 'Geeks', 'name': 'For', 3: 'Geeks'}
# Accessing an element using key
print(d['name'])
# Accessing a element using get
print(d.get(3))

'''
For
Geeks
'''
```
# Method signature

## **`def` Keyword**

The word you use to create a function in Python
python
```python
def greet():
    print("Hello")
```
### **Return Value**
The result that a function gives back to you after it finishes
python
```python
def add(a, b):
    return a + b  # gives back the sum

result = add(5, 3)  # result = 8
```
### **`None`**
A special value meaning "nothing" or "no value"
- If your function doesn't use `return`, it automatically returns `None`
---

## Types of Arguments

### **Positional Arguments**
Arguments that must be in the correct order
python
```python
def introduce(name, age):
    print(f"My name is {name} and I'm {age}")

introduce("Rawi", 20)  # Order matters!
```

### **Keyword Arguments**
Arguments you specify by name (order doesn't matter)
python
```python
introduce(age=20, name="Rawi")  # Same result!
```

### **Default Arguments**
Arguments with a preset value (optional to provide)
python
```python
def greet(name="Guest"):
    print(f"Hello {name}")

greet()         # prints: Hello Guest
greet("Rawi")   # prints: Hello Rawi
```

### **Variable-Length Arguments**
**`*args`** - accepts unlimited positional arguments (as a tuple)
python

```python
def sum_all(*numbers):
    return sum(numbers)

sum_all(1, 2, 3, 4, 5)  # Can pass any amount of numbers
```

**`**kwargs`** - accepts unlimited keyword arguments (as a dictionary)
python
```python
def print_info(**info):
    for key, value in info.items():
        print(f"{key}: {value}")

print_info(name="Rawi", age=20, city="KAUST")
```

---
## Advanced Concepts
### **First-Class Objects**
Functions can be treated like any other variable:
python

```python
def say_hello():
    return "Hello"

# Assign function to variable
my_func = say_hello

# Pass function as argument
def use_function(func):
    print(func())

use_function(say_hello)  # prints: Hello
```

### **Scope**
Where variables can be accessed
**Local scope** = variables inside a function only exist inside that function
python
```python
def my_function():
    local_var = 10  # Only exists inside this function
    print(local_var)

my_function()  # Works: prints 10
print(local_var)  # ERROR! local_var doesn't exist here
```

**Global scope** = variables outside functions can be accessed everywhere
python
```python
global_var = 5

def my_function():
    print(global_var)  # Can access global_var here

my_function()  # Works: prints 5
```

# Aliasing and Copying
## Aliasing

Aliasing occurs when two or more variables point to the same object in memory. They share the same memory address, meaning any modification through one variable affects all aliases.
```python
# Creating an alias
original_list = [1, 2, 3, 4, 5]
alias_list = original_list

print(f"original_list: {original_list}, ID: {id(original_list)}")
print(f"alias_list: {alias_list}, ID: {id(alias_list)}")
print(f"Same object? {original_list is alias_list}")

# Modify through alias
alias_list.append(6)

# Both variables show the change
print(f"\nAfter alias_list.append(6):")
print(f"original_list: {original_list}")
print(f"alias_list: {alias_list}")

"""
Output:
original_list: [1, 2, 3, 4, 5], ID: 140234567890123
alias_list: [1, 2, 3, 4, 5], ID: 140234567890123
Same object? True

After alias_list.append(6):
original_list: [1, 2, 3, 4, 5, 6]
alias_list: [1, 2, 3, 4, 5, 6]
"""
```

## Copying
Copying creates a new, independent object with the same values as the original. The copy has a different memory address, so modifications to one object do not affect the other.

here is showing both alias and copy 
```python
# Demonstrating both concepts
numbers = [10, 20, 30]

# Aliasing
alias = numbers
print(f"Alias same address? {id(numbers) == id(alias)}")  # True

# Copying
copy = numbers.copy()
print(f"Copy same address? {id(numbers) == id(copy)}")  # False

# Modify original
numbers.append(40)

print(f"\nAfter numbers.append(40):")
print(f"numbers: {numbers}")  # [10, 20, 30, 40]
print(f"alias: {alias}")      # [10, 20, 30, 40] - Changed!
print(f"copy: {copy}")        # [10, 20, 30] - Unchanged!

"""
Output:
Alias same address? True
Copy same address? False

After numbers.append(40):
numbers: [10, 20, 30, 40]
alias: [10, 20, 30, 40]
copy: [10, 20, 30]
"""
```

# Sequences and iterations 
## Iterations
### While loop
```python
while(condition):
	body code
	
#example

while(x<10):
	print("i hate python")
	x = x+1
```

### Forloop
```python
for var in range(10):
	print("this will be printed 10  times")

```

### Loop  Statements (break , continue , pass)

 **1. `break`**
**Description:** Exits the loop immediately and stops all remaining iterations.
**Code Example:**
```python
for i in range(10):
    if i == 5:
        break  # Stop the loop when i is 5
    print(i)

# Output: 0 1 2 3 4
# (stops at 5, doesn't print 5 or continue)
```

**2. `continue`**
**Description:** Skips the rest of the current iteration and jumps to the next iteration.
**Code Example:**
```python
for i in range(10):
    if i == 5:
        continue  # Skip printing 5, but continue with 6, 7, 8, 9
    print(i)

# Output: 0 1 2 3 4 6 7 8 9
# (5 is skipped, but loop continues)
```
 
 **3. `pass`**
**Description:** Does nothing. Used as a placeholder when syntax requires a statement but you don't want any action.
**Code Example:**
```python
for i in range(10):
    if i == 5:
        pass  # Do nothing when i is 5, just continue normally
    print(i)

# Output: 0 1 2 3 4 5 6 7 8 9
# (all numbers printed, pass does nothing)
```


## Sequences

### Mutability (editing array elements)
**Mutability** = "Can I change this thing after I create it?"
- **Mutable** = "Yes, you can modify it!" (like a whiteboard - erase and rewrite)
- **Immutable** = "No, once created, it's frozen!" (like a tattoo - permanent)

 **What "Immutable" Means:**
**Immutable objects cannot be changed after creation.** If you want a "different" version, Python creates a **new object** instead of modifying the original.

 **Mutable vs Immutable Types:**

| **Mutable** (Can Change) | **Immutable** (Cannot Change) |
| ------------------------ | ----------------------------- |
| Lists `[]`               | Strings `""`                  |
| Dictionaries `{}`        | Tuples `()`                   |
| Sets `{}`                | Numbers (int, float)          |
|                          | Booleans (True, False)        |
 **Code Examples:**
```python
# Lists are MUTABLE - can be changed
my_list = [10, 20, 30, 40, 50]
print(f"Original: {my_list}")
print(f"ID before: {id(my_list)}")  # Memory address

# Replace element at index 2
my_list[2] = 999
print(f"After replacing: {my_list}")
print(f"ID after: {id(my_list)}")  # Same memory address!

"""
Output:
Original: [10, 20, 30, 40, 50]
ID before: 140234567890123
After replacing: [10, 20, 999, 40, 50]
ID after: 140234567890123
"""
```

### **Strings**

#### Define
**Description:** Immutable sequence of characters. Once created, cannot be changed.

**How and Why to Use:**
- Store and manipulate text
- Immutable = safe to use as dictionary keys
- Use when you need text that won't change

**Code Example:**
```python
# Creating strings
name = "Rawi"
message = 'Hello World'
multi_line = """This is
a multi-line
string"""

# Operations
print(name[0])           # Access by index
print(name + " Ahmad")   # Concatenation
print(name.upper())      # Methods
print(len(name))         # Length

"""
Output:
R
Rawi Ahmad
RAWI
4
"""
```
### **Lists**
#### Define

**Description:** Mutable sequence that can hold any data types. Can be modified after creation.

**How and Why to Use:**
- Store collections of items that need to change
- Can add, remove, or modify elements
- Most flexible sequence type
- Use when you need to modify data

**Code Example:**

```python
# Creating lists
numbers = [1, 2, 3, 4, 5]
mixed = [1, "hello", 3.14, True]
empty = []

# Operations
numbers.append(6)        # Add element
numbers[0] = 10          # Modify element
numbers.remove(3)        # Remove element
print(numbers[1])        # Access by index
print(len(numbers))      # Length

"""
Output:
2
5
"""

print(numbers)

"""
Output:
[10, 2, 4, 5, 6]
"""
```
#### Cloning List
Cloning (or copying) a list creates a new, independent list with the same elements as the original. This is essential when you want to work with a duplicate of a list without affecting the original. Python provides several methods to clone lists, each creating a separate object in memory.
```python
# Original list
original = [10, 20, 30, 40, 50]
print(f"Original list: {original}, ID: {id(original)}")

# Method 1: Using .copy() method
clone1 = original.copy()
print(f"clone1 (using .copy()): {clone1}, ID: {id(clone1)}")

# Method 2: Using slice notation [:]
clone2 = original[:]
print(f"clone2 (using [:]): {clone2}, ID: {id(clone2)}")

# Method 3: Using list() constructor
clone3 = list(original)
print(f"clone3 (using list()): {clone3}, ID: {id(clone3)}")

# All clones are independent objects
print(f"\nAre they the same object as original?")
print(f"clone1 is original: {clone1 is original}")  # False
print(f"clone2 is original: {clone2 is original}")  # False
print(f"clone3 is original: {clone3 is original}")  # False

# But all have the same values
print(f"\nDo they have the same values?")
print(f"clone1 == original: {clone1 == original}")  # True
print(f"clone2 == original: {clone2 == original}")  # True
print(f"clone3 == original: {clone3 == original}")  # True

# Modifying a clone doesn't affect the original
clone1.append(60)
clone2[0] = 999
clone3.remove(30)

print(f"\nAfter modifying clones:")
print(f"original: {original}")  # [10, 20, 30, 40, 50] - Unchanged!
print(f"clone1: {clone1}")      # [10, 20, 30, 40, 50, 60]
print(f"clone2: {clone2}")      # [999, 20, 30, 40, 50]
print(f"clone3: {clone3}")      # [10, 20, 40, 50]

"""
Output:
Original list: [10, 20, 30, 40, 50], ID: 140234567890123
clone1 (using .copy()): [10, 20, 30, 40, 50], ID: 140234567890456
clone2 (using [:]): [10, 20, 30, 40, 50], ID: 140234567890789
clone3 (using list()): [10, 20, 30, 40, 50], ID: 140234567891012

Are they the same object as original?
clone1 is original: False
clone2 is original: False
clone3 is original: False

Do they have the same values?
clone1 == original: True
clone2 == original: True
clone3 == original: True

After modifying clones:
original: [10, 20, 30, 40, 50]
clone1: [10, 20, 30, 40, 50, 60]
clone2: [999, 20, 30, 40, 50]
clone3: [10, 20, 40, 50]
"""
```
### **Tuples**
#### Define
**Description:** Immutable sequence like lists but cannot be changed after creation. Uses parentheses `()`.

**How and Why to Use:**
- Store data that should NOT change
- Faster than lists (performance)
- Can be used as dictionary keys (unlike lists)
- Use for fixed collections (coordinates, RGB colors, etc.)

**Code Example:**
```python
# Creating tuples
coordinates = (10, 20)
rgb_color = (255, 0, 128)
single = (42,)           # Single element needs comma
empty = ()

# Operations
print(coordinates[0])    # Access by index
print(len(rgb_color))    # Length
x, y = coordinates       # Unpacking

# coordinates[0] = 15    # ❌ ERROR! Tuples are immutable

"""
Output:
10
3
"""

print(x, y)

"""
Output:
10 20
"""
```

##### **What is meant by "Can be used as dictionary keys (unlike lists)"**

so normal dictionaries have key-pair elements just like this example:
```python
dict1 = {"a":1 , "b":2 , "c":3 , "z":26}
```
but what is meant there that is a tuple can be used as key for values like in this example:
```python
distance_map = {
    ("Riyadh", "Jeddah"): 950,    # km
    ("Riyadh", "Dammam"): 395,
    ("Jeddah", "Mecca"): 79
}

# Find distance between Riyadh and Jeddah
dist = distance_map[("Riyadh", "Jeddah")]
print(f"Distance: {dist} km")  # 950 km
```
so the tuple or "key-pairs" must used as ==COMPLETE KEYS and not multi-keys==

#### Creating tuple
A tuple is created by placing all the items inside parentheses (), separated by commas. A tuple can have any number of items and they can be of different [data types](https://www.geeksforgeeks.org/python/python-data-types/).
```python
tup = ()
print(tup)

# Using String
tup = ('Geeks', 'For')
print(tup)

# Using List
li = [1, 2, 4, 5, 6]
print(tuple(li)) # type caste list-to-tuple 

# Using Built-in Function
tup = tuple('Geeks')
print(tup)

"""
()
('Geeks', 'For')
(1, 2, 4, 5, 6)
('G', 'e', 'e', 'k', 's')
"""
```

Creating tuple with mixed datatypes:
```python
tup = (5, 'Welcome', 7, 'Geeks')
print(tup)

# Creating a Tuple with nested tuples
tup1 = (0, 1, 2, 3)
tup2 = ('python', 'geek')
tup3 = (tup1, tup2)
print(tup3)

# Creating a Tuple with repetition
tup1 = ('Geeks',) * 3
print(tup1)

# Creating a Tuple with the use of loop
tup = ('Geeks')
n = 5
for i in range(int(n)):
    tup = (tup,)
    print(tup)
```

#### Operations on tuples
##### Accessing operations
We can access the elements of a tuple by using indexing and [slicing](https://www.geeksforgeeks.org/python/tuple-slicing-python/), similar to how we access elements in a list. Indexing starts at 0 for the first element and goes up to n-1, where n is the number of elements in the tuple. Negative indexing starts from -1 for the last element and goes backward.
```python
# Accessing Tuple with Indexing
tup = tuple("Geeks")
print(tup[0])

# Accessing a range of elements using slicing
print(tup[1:4])  
print(tup[:3])

# Tuple unpacking
tup = ("Geeks", "For", "Geeks")

# This line unpack values of Tuple1
a, b, c = tup
print(a)
print(b)
print(c)
"""
outputs:

G
('e', 'e', 'k')
('G', 'e', 'e')
Geeks
For
Geeks
"""
```

##### Concatenation of Tuples
Tuples can be concatenated using the + operator. This operation combines two or more tuples to create a new tuple.

>Note: Only the same datatypes can be combined with concatenation, an error arises if a list and a tuple are combined.

```python
tup1 = (0, 1, 2, 3)
tup2 = ('Geeks', 'For', 'Geeks')

tup3 = tup1 + tup2
print(tup3)
```

##### Deleting a tuple
```python
tup = (0, 1, 2, 3, 4)
del tup

print(tup)
```

##### Tuple Unpacking with astrick
In Python, the ****" * "**** operator can be used in tuple unpacking to grab multiple items into a list. This is useful when you want to extract just a few specific elements and collect the rest together.
```python
tup = (1, 2, 3, 4, 5)

a, *b, c = tup

print(a) 
print(b) 
print(c)

"""
1
[2, 3, 4]
5
"""
```
Explanation:
- ****a**** gets the first item.
- ****c**** gets the last item.
- ***b*** collects everything in between into a list.

##### Tuple packing

## **Key Differences Example:**

```python
# String - immutable
text = "hello"
# text[0] = "H"  # ❌ ERROR!
text = "Hello"   # ✅ Create new string

# List - mutable
my_list = [1, 2, 3]
my_list[0] = 10  # ✅ Can modify
my_list.append(4) # ✅ Can add

# Tuple - immutable
my_tuple = (1, 2, 3)
# my_tuple[0] = 10  # ❌ ERROR!
# my_tuple.append(4) # ❌ ERROR!

"""
Output:
[10, 2, 3, 4]
"""
print(my_list)
```

# Conditionals

### **1. if-statements (if, elif, else)**
**Description:** Execute code based on conditions. `elif` means "else if".
**Code Example:**
```python
age = 20

if age < 18:
    print("You are a minor")
elif age < 65:
    print("You are an adult")
else:
    print("You are a senior")

# Output: You are an adult
```

### **2. Switch Cases (match-case in Python)**
**Description:** Python 3.10+ has `match-case` (like switch in Java). For older Python, use if-elif chains.
**Code Example (Python 3.10+):**
```python
day = 3

match day:
    case 1:
        print("Monday")
    case 2:
        print("Tuesday")
    case 3:
        print("Wednesday")
    case _:  # default case (like 'default' in Java)
        print("Other day")

# Output: Wednesday
```
**Alternative (older Python):**
```python
day = 3

if day == 1:
    print("Monday")
elif day == 2:
    print("Tuesday")
elif day == 3:
    print("Wednesday")
else:
    print("Other day")
```
### **3. Ternary Operator**

**Description:** Short one-line if-else statement.
**Syntax:** `value_if_true if condition else value_if_false`
**Code Example:**
```python
age = 20

# Ternary operator
status = "adult" if age >= 18 else "minor"
print(status)

# Output: adult

# Same as:
# if age >= 18:
#     status = "adult"
# else:
#     status = "minor"
```
### **4. Nested if-statements**
**Description:** if-statements inside other if-statements.
**Code Example:**

```python
age = 20
has_license = True

if age >= 18:
    if has_license:
        print("You can drive")
    else:
        print("You need a license")
else:
    print("You are too young to drive")

# Output: You can drive
```


## **5. Exception Handling (try-except)**
**Description:** Handle errors/exceptions without crashing the program. `except` is Python's version of `catch` in Java.
**Code Example:**
```python
try:
    number = int(input("Enter a number: "))
    result = 10 / number
    print(f"Result: {result}")
except ValueError:
    print("That's not a valid number!")
except ZeroDivisionError:
    print("Cannot divide by zero!")
except Exception as e:
    print(f"An error occurred: {e}")
else:
    print("No errors occurred")
finally:
    print("This always runs, error or not")

# Example run (user enters 0):
# Output: Cannot divide by zero!
#         This always runs, error or not
```
 **Java vs Python Comparison:**

| Java                      | Python                         |
| ------------------------- | ------------------------------ |
| `try { }`                 | `try:`                         |
| `catch (Exception e) { }` | `except Exception as e:`       |
| `finally { }`             | `finally:`                     |
| No equivalent             | `else:` (runs if no exception) |

## **Simple Example:**

```python
try:
    result = 10 / 0  # This will cause error
except:
    print("An error happened!")

# Output: An error happened!
```

| Statement          | Syntax                                      |
| ------------------ | ------------------------------------------- |
| **if-elif-else**   | `if condition:` `elif condition:` `else:`   |
| **switch (match)** | `match variable:` `case value:` `case _:`   |
| **ternary**        | `result = value1 if condition else value2`  |
| **nested if**      | `if condition:` then `if condition:` inside |
# Functions (library and define)
## Defining
you know what functions is so am skipping 

how to write a function:
```python
def func_name (parameters):
	bodycode 
	return things
	
#example
def addtion(num1 , num2):
	result = num1 + num2
	return result
```

### **Arguments**
- **default  arguments:** if nothing sent to function, it will be assigned with hardcoded data
```python
def fun (x , y=50):
	some work
```

- `*args and  **kwargs`
simply: 
```
* accepts numbers in parameter with no limits 
  
** accepts strings with no limits
``` 

**`*args`** = Allows a function to accept **any number of positional arguments** (as a tuple)

 `*args` (Positional Arguments)**
Collects all extra positional arguments into a **tuple**.
Basic Example
```python
def print_numbers(*args):
    print(args)
    print(type(args))

print_numbers(1, 2, 3, 4, 5)

"""
Output:
(1, 2, 3, 4, 5)
<class 'tuple'>
"""
```

**`**kwargs`** = Allows a function to accept **any number of keyword arguments** (as a dictionary).
Collects all extra keyword arguments into a **dictionary**.

Basic Example:

```python
def print_info(**kwargs):
    print(kwargs)
    print(type(kwargs))

print_info(name="Rawi", age=20, city="Riyadh")

"""
Output:
{'name': 'Rawi', 'age': 20, 'city': 'Riyadh'}
<class 'dict'>
"""
```

## Boolean functions

### .any(array) 
**Description:** `any()` is a **built-in Python function** that checks if **at least one** element in an iterable is `True`.
- Returns:
	- True : if any element is true , at least one
	- false : if no true at all 
code Example:
```python
print(any([True, False, False]))   # True (at least one True)
print(any([False, False, True]))   # True (at least one True)
print(any([False, False, False]))  # False (all are False)
print(any([True, True, True]))     # True (at least one True)

"""
Output:
True
True
False
True
"""
```
Real Practice example:
```python
grades = [45, 52, 38, 61]
has_passing_grade = any(grade >= 60 for grade in grades)
print(has_passing_grade)  # True (61 is >= 60)

"""
Output:
True
"""
```
## List functions

### slice (Slicing)
**Description:** Extract a portion of a sequence using `[start:end:step]`. Works on strings, lists, and tuples.
**Code Example:**
```python
# String slicing
text = "Hello World"
print(text[0:5])      # From index 0 to 4 (5 is excluded)
print(text[6:])       # From index 6 to end
print(text[:5])       # From start to index 4
print(text[::2])      # Every 2nd character
print(text[::-1])     # Reverse the string

"""
Output:
Hello
World
Hello
HloWrd
dlroW olleH
"""

# List slicing
numbers = [10, 20, 30, 40, 50, 60]
print(numbers[1:4])   # Get elements from index 1 to 3
print(numbers[-2:])   # Last 2 elements

"""
Output:
[20, 30, 40]
[50, 60]
"""
```

###  join()
**Description:** Joins elements of a list into a single string with a separator. Opposite of split().
**Code Example:**
```python
# Join with space
words = ["Hello", "World", "Python"]
sentence = " ".join(words)
print(sentence)

"""
Output:
Hello World Python
"""

# Join with custom separator
fruits = ["apple", "banana", "orange"]
result = ", ".join(fruits)
print(result)

"""
Output:
apple, banana, orange
"""

# Join with no separator
letters = ["P", "y", "t", "h", "o", "n"]
word = "".join(letters)
print(word)

"""
Output:
Python
"""
```

### append()
Adds an element to the end of the list.
python
```python
fruits = ['apple', 'banana']
fruits.append('cherry')
print(fruits)

"""
Output:
['apple', 'banana', 'cherry']
"""
```

### copy()
Returns a shallow copy of the list.
python
```python
original = [1, 2, 3]
duplicate = original.copy()
duplicate.append(4)
print(f"original: {original}")
print(f"duplicate: {duplicate}")

"""
Output:
original: [1, 2, 3]
duplicate: [1, 2, 3, 4]
"""
```

### clear()
Removes all elements from the list.
python
```python
numbers = [1, 2, 3, 4, 5]
numbers.clear()
print(numbers)

"""
Output:
[]
"""
```

### count()
Returns the number of times a specified element appears in the list.
python
```python
numbers = [1, 2, 2, 3, 2, 4]
count_of_twos = numbers.count(2)
print(count_of_twos)

"""
Output:
3
"""
```

### extend()
Adds elements from another list to the end of the current list.
python
```python
list1 = [1, 2, 3]
list2 = [4, 5, 6]
list1.extend(list2)
print(list1)

"""
Output:
[1, 2, 3, 4, 5, 6]
"""
```

### index()

Returns the index of the first occurrence of a specified element.

python

```python
fruits = ['apple', 'banana', 'cherry', 'banana']
position = fruits.index('banana')
print(position)

"""
Output:
1
"""
```

### insert()

Inserts an element at a specified position.

python

```python
numbers = [1, 2, 4, 5]
numbers.insert(2, 3)  # Insert 3 at index 2
print(numbers)

"""
Output:
[1, 2, 3, 4, 5]
"""
```

### pop()
Removes and returns the element at the specified position (or the last element if no index is specified).

python

```python
fruits = ['apple', 'banana', 'cherry']
removed = fruits.pop()  # Remove last element
print(f"Removed: {removed}")
print(f"List: {fruits}")

removed_at_index = fruits.pop(0)  # Remove element at index 0
print(f"Removed: {removed_at_index}")
print(f"List: {fruits}")

"""
Output:
Removed: cherry
List: ['apple', 'banana']
Removed: apple
List: ['banana']
"""
```

### remove()

Removes the first occurrence of a specified element.

python

```python
numbers = [1, 2, 3, 2, 4]
numbers.remove(2)  # Removes first 2
print(numbers)

"""
Output:
[1, 3, 2, 4]
"""
```

### reverse()

Reverses the order of the elements in the list.

python

```python
numbers = [1, 2, 3, 4, 5]
numbers.reverse()
print(numbers)

"""
Output:
[5, 4, 3, 2, 1]
"""
```

### sort()

Sorts the list in ascending order (by default).

python

```python
numbers = [3, 1, 4, 1, 5, 9, 2]
numbers.sort()
print(numbers)

# Sort in descending order
numbers.sort(reverse=True)
print(numbers)

"""
Output:
[1, 1, 2, 3, 4, 5, 9]
[9, 5, 4, 3, 2, 1, 1]
"""
```

## String functions

### **. split()**
**Description:** Splits a string into a list based on a delimiter (separator). Default delimiter is space.
**Code Example:**
```python
# Split by space (default)
sentence = "Hello World Python"
words = sentence.split()
print(words)

"""
Output:
['Hello', 'World', 'Python']
"""

# Split by custom delimiter
email = "rawi@kaust.edu.sa"
parts = email.split("@")
print(parts)

"""
Output:
['rawi', 'kaust.edu.sa']
"""

# Split with limit
text = "apple,banana,orange,grape"
fruits = text.split(",", 2)  # Split only first 2
print(fruits)

"""
Output:
['apple', 'banana', 'orange,grape']
"""
```

### .replace() 
**Description:** Replaces all occurrences of a substring with another substring. Returns a new string (strings are immutable).
**Code Example:**

```python
# Basic replace
text = "Hello World"
new_text = text.replace("World", "Python")
print(new_text)
print(text)  # Original unchanged (immutable!)

"""
Output:
Hello Python
Hello World
"""

# Replace with count limit
message = "I love cats. Cats are great. Cats!"
new_message = message.replace("Cats", "Dogs", 2)  # Replace first 2 only
print(new_message)

"""
Output:
I love Dogs. Dogs are great. Cats!
"""

# Remove characters (replace with empty string)
phone = "123-456-7890"
clean_phone = phone.replace("-", "")
print(clean_phone)

"""
Output:
1234567890
"""
```

### **.strip() / lstrip() / rstrip() **
**Description:** Removes whitespace (or specified characters) from the beginning and/or end of a string.
**Code Example:**

```python
# strip() - removes from both ends
text = "   Hello World   "
clean = text.strip()
print(f"'{clean}'")

"""
Output:
'Hello World'
"""

# lstrip() - removes from left (start)
text = "###Hello"
clean = text.lstrip("#")
print(clean)

"""
Output:
Hello
"""

# rstrip() - removes from right (end)
text = "Hello!!!"
clean = text.rstrip("!")
print(clean)

"""
Output:
Hello
"""

# Common use: clean user input
user_input = "  rawi@kaust.edu.sa  \n"
email = user_input.strip()
print(f"'{email}'")

"""
Output:
'rawi@kaust.edu.sa'
"""
```


# Object Reference
**Object Referencing:** A variable holds a reference (like an address/pointer) to an object in memory, not the actual object itself. When you assign one variable to another, both point to the same object.

What's special ? 
you can use `del` function which deletes a variable as example :
```python
s = 10
print(s)

del s
print(s)
```
outputs:
```bash
10 

    print(s)
          ^
NameError: name 's' is not defined
```

Object referencing example:
```python
#am using lists here and showing that i can manuplate data using another variable

list1 = [1, 2, 3]
list2 = list1  # list2 now points to the SAME list, not a copy
list2.append(4)
print(list1)  # Output: [1, 2, 3, 4] - both changed!
```


# (Optional) Turtle

here's some turtle GUI methods 
![[Screenshot From 2025-11-12 18-16-30.png]]

explaining using code for quick reference:
```python
import turtle

# 1. Create screen object (the window)
screen = turtle.Screen()
screen.bgcolor("black")
screen.title("Turtle Demo")

# 2. Create turtle object (turtle.Turtle is the class)
my_turtle = turtle.Turtle()

# Built-in attributes/methods that Turtle class provides:
my_turtle.color("cyan")
my_turtle.speed(2)
my_turtle.shape("turtle")

# 3. Create NEW custom attributes (what instructor means)
my_turtle.player_name = "Rawi"  # Add custom attribute
my_turtle.lives = 3  # Add another custom attribute
my_turtle.points = 0  # Add another custom attribute

# Use the custom attributes
print(f"Player: {my_turtle.player_name}")
print(f"Lives: {my_turtle.lives}")

# Draw something
my_turtle.forward(100)
my_turtle.left(90)
my_turtle.forward(100)

# Update custom attribute
my_turtle.points += 50
print(f"Points: {my_turtle.points}")

# Keep window open
screen.mainloop()
```

# Indentations
In Python, [Indentation](https://www.geeksforgeeks.org/python/indentation-in-python/) is used to define blocks of code. It tells the Python interpreter that a group of statements belongs to a specific block. All statements with the same level of indentation are considered part of the same block. Indentation is achieved using whitespace (spaces or tabs) at the beginning of each line.
example:
```python
def sayHello(name):

	print(f'hello mr {name}') #this line belongs to the function
	
print(f'hello mr {name}')# while this dont because of indentations
```


# Working with files
## Define
File handling refers to the process of performing operations on a file, such as creating, opening, reading, writing and closing it through a programming interface. It involves managing the data flow between the program and the file system on the storage device, ensuring that data is handled safely and efficiently

### Opening and Closing a file
to open a file we have to use `.open()` which requires two arguments: file name with domain ".txt , .csv .pdf etc..."

**Closing:** The file.close() method closes the file and releases the system resources. If the file was opened in write or append mode, closing ensures that all changes are properly saved.
```python
file = open("folder/filename.txt" , "mode")
# mode is reading "r" by default 

# example
file = open("filename.txt" , "r")
file.close() #closes operations on file
```

### File properties
Once the file is open, we can check some of its properties:
- **f.name:*** Returns the name of the file that was opened (in this case, "geek.txt").
- **f.mode:*** Tells us the mode in which the file was opened. Here, it’s 'r' which means read mode.
- **f.closed:*** Returns a boolean value- False when file is currently open otherwise True.
```python
f = open("geek.txt", "r")

print("Filename:", f.name)
print("Mode:", f.mode)
print("Is Closed?", f.closed)

f.close()
print("Is Closed?", f.closed)

"""
Filename: geek.txt  
Mode: r  
Is Closed? False  
Is Closed? True
"""
```

### Reading a file 
achieved by ****file.read()**** which reads the entire content of the file. After reading, it’s good practice to close the file to free up system resources.
```python
file = open("geek.txt", "r")
content = file.read()
print(content)
file.close()

'''
Hello world  
GeeksforGeeks  
123 456
'''
```

### Writing a file 
In Python, [writing to a file](https://www.geeksforgeeks.org/python/writing-to-file-in-python/) is done using the mode "w". This creates a new file if it doesn’t exist, or overwrites the existing file if it does. The write() method is used to add content. After writing, make sure to close the file.

****Example:**** Writing to a file (overwrites if file exists)
```python
with open("geek.txt", "w") as file:

    file.write("Hello, Python!\n")

    file.write("File handling is easy with Python.")

​
print("File written successfully")

****Output:****

> Hello, Python!  
> File handling is easy with Python.
```

****Explanation:****
- "w" mode opens the file for writing (overwrites existing content if the file already exists).
- write() method adds new text to the file.
- When using with, the file closes automatically at the end of the block.

### Using "with" Statement
Instead of manually opening and closing the file, you can use the [with statement](https://www.geeksforgeeks.org/python/with-statement-in-python/), which automatically handles closing. This reduces the risk of file corruption and resource leakage.
```python
with open("geek.txt", "r") as file:
    content = file.read()
    print(content)

'''
Hello, World!
'''
```

## Handling Exceptions When Closing a File
It's important to [handle exceptions](https://www.geeksforgeeks.org/python/python-exception-handling/) to ensure that files are closed properly, even if an error occurs during file operations. Here, the finally block ensures the file is closed even if an error occurs.
```python
try:
    file = open("geek.txt", "r")
    content = file.read()
    print(content)
finally:
    file.close()
 
****Output:****

> Hello, World!
```
****Explanation:****
- ****try:**** Starts the block to handle code that might raise an error.
- ****open():**** Opens the file in read mode.
- ****read():**** Reads the content of the file.
- ****finally:**** Ensures the code inside it runs no matter what.

# Dictionaries

## Define
Python dictionary is a data structure that stores the value in key: value pairs. Values in a dictionary can be of any data type and can be duplicated, whereas keys can't be repeated and must be immutable.

- Keys are case sensitive which means same name but different cases of Key will be treated distinctly.
- Keys must be immutable which means keys can be strings, numbers or tuples but not lists.
- Duplicate keys are not allowed and any duplicate key will overwrite the previous value.
- Internally uses hashing. Hence, operations like search, insert, delete can be performed in Constant Time.
- From Python 3.7 Version onward, Python dictionary are Ordered.

## How to operate Dictionaries

### Creating
```python
d1 = {1:"blue" , 2:"yellow" , 3:"black"}

#or using dict() constructor
d2 = dict(a = "balls" , m = "for" , r = "rawi")
```

### Accessing them
```python
d1 = {1:"blue" , 2:"yellow" , 3:"black"}

# by key
print(d1[2]) # outputs: "yellow"

#by using get
print(d1.get("3")) # outputs: "black"
```

### Adding and updating
adding a key that isn't exist will make a new pair, while adding a key that exist will update the value of that key.
```python
d1 = {1:"blue" , 2:"yellow" , 3:"black"}

# Adding a new key-value pair 
d[4]="whiite"

# Updating an exist value
d[3] = "tourqouise"

print(d1)

#outputs:
#{1:"blue" , 2:"yellow" , 3:"tourqouise" , 4:"whiite"}
```

### Removing Dictionaries
We can remove items from dictionary using the following methods:
- [****del****](https://www.geeksforgeeks.org/python/python-del-to-delete-objects/): Removes an item by key.
- [****pop()****](https://www.geeksforgeeks.org/python/python-dictionary-pop-method/)****:**** Removes an item by key and returns its value.
- [****clear()****](https://www.geeksforgeeks.org/python/python-dictionary-clear/)****:**** Empties the dictionary.
- [****popitem()****](https://www.geeksforgeeks.org/python/python-dictionary-popitem-method/)****:**** Removes and returns the last key-value pair
```python
d = {1: 'Geeks', 2: 'For', 3: 'Geeks', 'age':22}

# Using del to remove an item
del d["age"]
print(d)

# Using pop() to remove an item and return the value
val = d.pop(1)
print(val)

# Using popitem to removes and returns
# the last key-value pair.
key, val = d.popitem()
print(f"Key: {key}, Value: {val}")

# Clear all items from the dictionary
d.clear()
print(d)

#output:
#{1: 'Geeks', 2: 'For', 3: 'Geeks'}
#Geeks
#Key: 3, Value: Geeks
#{}
```

### Iterating Through a Dictionary
we can iterate over keys using ` keys() ` method, values using ` values() ` or both using ` item() ` with a for loop 

```python
d = {1: 'Geeks', 2: 'For', 'age':22}

# Iterate over keys
for key in d:
    print(key)

# Iterate over values
for value in d.values():
    print(value)

# Iterate over key-value pairs
for key, value in d.items():
    print(f"{key}: {value}")
```

### Nested Dictionaries
you can put Dictionaries inside of Dictionaries and make  a nested Dictionaries

```python
d = {1: 'Geeks', 2: 'For',
        3: {'A': 'Welcome', 'B': 'To', 'C': 'Geeks'}}

print(d)
```


### Accumulations
# Lambda expression
Lambda Functions are anonymous functions means that the function is without a name. As we already know def keyword is used to define a normal function in Python. Similarly, lambda keyword is used to define an anonymous function in [Python](https://www.geeksforgeeks.org/python/python-programming-language-tutorial/).

****Explanation:**** s2 is a lambda function that takes a string and returns it in uppercase. Applying it to 'GeeksforGeeks' gives the result.

Syntax
`lambda arguments : expression`
- ****lambda:**** The keyword to define the function.
- ****arguments:**** A comma-separated list of input parameters like in a regular function.
- ****expression:**** A single expression that is evaluated and returned.

Syntax examples :
```python
s1 = "hello guys"
s2 = lambda func: func.upper()
print(s2(s1))

#output
hello guys
```
example 2 :
```python
n = lambda x: "Positive" if x > 0 else "Negative" if x < 0 else "Zero"
#             ↑          ↑          ↑  ↑          ↑          ↑
#             value1     condition1    value2     condition2  value3
```
**Reading it step by step:**
1. If `x > 0` → return `"Positive"`
2. Else, if `x < 0` → return `"Negative"`
3. Else → return `"Zero"`


>Note: ternary operator here is writing value before condition unlike java and node.js 

# Sorting
## Define
Sorting is defined as an arrangement of data in a certain order. Sorting techniques are used to arrange data(mostly numerical) in an ascending or descending order. It is a method used for the representation of data in a more comprehensible format.

- Sorting a large amount of data can take a substantial amount of computing resources and time if we use an inefficient algorithm to sort.
- The efficiency of the algorithm is proportional to the number of items to be sorted.
- For a small amount of data, a complex sorting method may be more trouble than it is worth.
- On the other hand, for larger amounts of data, we want to increase the efficiency and speed as far as possible.

****The different types of order are:****

**Increasing (Strict Going Up)**
```
Floor: 1 → 2 → 3 → 4 → 5
Rule: MUST go up, no stopping at same floor
```

**Non-Increasing (Going Down, Can Stop)**
```
Floor: 5 → 4 → 3 → 2 → 2 → 1
Rule: Go down OR stay at same floor
```

 **Decreasing (Strict Going Down)**
```
Floor: 5 → 4 → 3 → 2 → 1
Rule: MUST go down, no stopping at same floor
```

**Non-Decreasing (Going Up, Can Stop)**
```
Floor: 1 → 2 → 2 → 3 → 4 → 5
Rule: Go up OR stay at same floor
```


## Built in techniques
python have built in techniques  which are:
- `.sort()` :  modifies the original list , so the return is the actual original modified list
- `.sorted()`: takes a copy of the original list and modifies it , so the original is copy not original

### reversed parameter
in case if u want the list get ordered reversed you just have to add a parameter giving it true like here 
```python
list1 = [3,6,2,7]
listSorted= sorted(list1 , reverse = True)
listSorted1= sorted(list1 , reverse = Fasle)

print(listSorted)
print(listSorted1)

"""
[2,3,6,7]
[7,6,3,2]
"""
```
## Sorting Techniques
there several techniques of sorting (bubble , merge , insertion , selection , quick , heap)
### Bubble Sort

**Bubble Sort** is the simplest sorting algorithm that works by repeatedly stepping through the list, comparing adjacent elements and swapping them if they are in the wrong order. The algorithm gets its name because smaller elements "bubble" to the top of the list with each pass through the data.

**How it works:** Compare each pair of adjacent items and swap them if they're in the wrong order. Repeat this process until no more swaps are needed.

```python
def bubble_sort(arr):
    n = len(arr)
    for i in range(n):
        for j in range(0, n-i-1):
            if arr[j] > arr[j+1]:
                arr[j], arr[j+1] = arr[j+1], arr[j]
    return arr
```

#### Detailed Explanation

**Complete Code:**

```python
def bubble_sort(arr):
    n = len(arr)
    for i in range(n):
        swapped = False
        for j in range(0, n-i-1):
            if arr[j] > arr[j+1]:
                arr[j], arr[j+1] = arr[j+1], arr[j]
                swapped = True
        if not swapped:
            break
    return arr
```

**Step-by-step breakdown:**

**Line 1-2:** Calculate the length of the array

```
arr = [64, 34, 25, 12, 22]
n = 5
```

**Line 3:** Outer loop runs _n_ times (one pass for each element)

```
Pass 1: i = 0
Pass 2: i = 1
...and so on
```

**Line 4:** Create a flag to track if any swaps occurred

```
swapped = False (at start of each pass)
```

**Line 5:** Inner loop compares adjacent elements

```
When i=0, j goes from 0 to 3 (n-i-1 = 5-0-1 = 4)
Comparisons: arr[0] vs arr[1], arr[1] vs arr[2], arr[2] vs arr[3], arr[3] vs arr[4]
```

**Line 6-8:** Compare and swap if needed

```
Initial: [64, 34, 25, 12, 22]

j=0: Compare 64 and 34 → Swap → [34, 64, 25, 12, 22]
j=1: Compare 64 and 25 → Swap → [34, 25, 64, 12, 22]
j=2: Compare 64 and 12 → Swap → [34, 25, 12, 64, 22]
j=3: Compare 64 and 22 → Swap → [34, 25, 12, 22, 64] ← Largest element bubbled to end!
```

**Line 9-10:** Early termination if no swaps occurred

```
If swapped is still False after inner loop → Array is sorted → Break
```

**Visual representation of complete sorting:**

```
Pass 1: [64, 34, 25, 12, 22] → [34, 25, 12, 22, 64]
Pass 2: [34, 25, 12, 22, 64] → [25, 12, 22, 34, 64]
Pass 3: [25, 12, 22, 34, 64] → [12, 22, 25, 34, 64]
Pass 4: [12, 22, 25, 34, 64] → [12, 22, 25, 34, 64] ← No swaps, sorted!
```

---

### Selection Sort

**Selection Sort** divides the input list into two parts: a sorted portion at the left end and an unsorted portion at the right end. Initially, the sorted portion is empty and the unsorted portion is the entire list. The algorithm repeatedly selects the smallest element from the unsorted portion and moves it to the end of the sorted portion.

**How it works:** Find the minimum element in the unsorted portion, swap it with the first unsorted element, then move the boundary between sorted and unsorted portions one position to the right.

```python
def selection_sort(arr):
    for i in range(len(arr)):
        min_idx = i
        for j in range(i+1, len(arr)):
            if arr[j] < arr[min_idx]:
                min_idx = j
        arr[i], arr[min_idx] = arr[min_idx], arr[i]
    return arr
```

#### Detailed Explanation

**Complete Code:**

```python
def selection_sort(arr):
    n = len(arr)
    for i in range(n):
        min_idx = i
        for j in range(i+1, n):
            if arr[j] < arr[min_idx]:
                min_idx = j
        arr[i], arr[min_idx] = arr[min_idx], arr[i]
    return arr
```

**Step-by-step breakdown:**

**Line 1-2:** Initialize array length

```
arr = [64, 25, 12, 22, 11]
n = 5
```

**Line 3:** Outer loop moves boundary of sorted/unsorted portions

```
i = 0 → Sorted: [] | Unsorted: [64, 25, 12, 22, 11]
i = 1 → Sorted: [11] | Unsorted: [64, 25, 12, 22]
i = 2 → Sorted: [11, 12] | Unsorted: [64, 25, 22]
...and so on
```

**Line 4:** Assume first unsorted element is minimum

```
When i=0: min_idx = 0 → arr[0] = 64 is assumed minimum
```

**Line 5-7:** Search for actual minimum in unsorted portion

```
Initial: [64, 25, 12, 22, 11]
         ↑ min_idx = 0

j=1: arr[1]=25 < arr[0]=64 → min_idx = 1
     [64, 25, 12, 22, 11]
          ↑ min_idx = 1

j=2: arr[2]=12 < arr[1]=25 → min_idx = 2
     [64, 25, 12, 22, 11]
              ↑ min_idx = 2

j=3: arr[3]=22 NOT < arr[2]=12 → min_idx stays 2

j=4: arr[4]=11 < arr[2]=12 → min_idx = 4
     [64, 25, 12, 22, 11]
                      ↑ min_idx = 4 (found minimum!)
```

**Line 8:** Swap minimum with first unsorted element

```
Swap arr[0] with arr[4]: [64, 25, 12, 22, 11] → [11, 25, 12, 22, 64]
                          ↑                  ↑      ↑
                       position i         min_idx   sorted!
```

**Visual representation of complete sorting:**

```
Pass 1: [64, 25, 12, 22, 11] → Find min=11 → [11, 25, 12, 22, 64]
        Sorted: [11]

Pass 2: [11, 25, 12, 22, 64] → Find min=12 → [11, 12, 25, 22, 64]
        Sorted: [11, 12]

Pass 3: [11, 12, 25, 22, 64] → Find min=22 → [11, 12, 22, 25, 64]
        Sorted: [11, 12, 22]

Pass 4: [11, 12, 22, 25, 64] → Find min=25 → [11, 12, 22, 25, 64]
        Sorted: [11, 12, 22, 25, 64] ← Done!
```

---

### Insertion Sort

**Insertion Sort** builds the final sorted array one item at a time by repeatedly taking the next element from the unsorted portion and inserting it into its correct position in the sorted portion. It works similarly to how you might sort playing cards in your hands.

**How it works:** Start with the second element, compare it with elements in the sorted portion (to its left), and shift larger elements to the right until you find the correct position to insert it.

```python
def insertion_sort(arr):
    for i in range(1, len(arr)):
        key = arr[i]
        j = i - 1
        while j >= 0 and arr[j] > key:
            arr[j+1] = arr[j]
            j -= 1
        arr[j+1] = key
    return arr
```

#### Detailed Explanation

**Complete Code:**

```python
def insertion_sort(arr):
    for i in range(1, len(arr)):
        key = arr[i]
        j = i - 1
        while j >= 0 and arr[j] > key:
            arr[j+1] = arr[j]
            j -= 1
        arr[j+1] = key
    return arr
```

**Step-by-step breakdown:**

**Line 1-2:** Start from second element (index 1)

```
arr = [12, 11, 13, 5, 6]
i starts at 1 (we assume arr[0] is already sorted)
```

**Line 3:** Store current element to insert

```
When i=1: key = arr[1] = 11
         [12, 11, 13, 5, 6]
              ↑ This needs to be inserted into sorted portion
```

**Line 4:** Start comparing from previous element

```
j = i - 1 = 0
j points to last element of sorted portion: arr[0] = 12
```

**Line 5-7:** Shift larger elements to the right

```
Initial: [12, 11, 13, 5, 6], key=11, j=0
         ↑sorted  ↑key

Check: arr[0]=12 > key=11? YES
Shift: arr[1] = arr[0] → [12, 12, 13, 5, 6]
       [12, 12, 13, 5, 6]
        ↑   ↑ 12 moved right
j becomes -1
```

**Line 8:** Insert key at correct position

```
arr[j+1] = arr[0] = key
[11, 12, 13, 5, 6]
 ↑ key inserted!
```

**Visual representation of complete sorting:**

```
Start: [12, 11, 13, 5, 6]
       ↑sorted portion

Pass 1 (i=1, key=11):
[12, 11, 13, 5, 6] → Compare 11 with 12 → Shift 12 right
[12, 12, 13, 5, 6] → Insert 11 at position 0
[11, 12, 13, 5, 6]
↑---↑sorted portion

Pass 2 (i=2, key=13):
[11, 12, 13, 5, 6] → Compare 13 with 12 → No shift needed (13 > 12)
[11, 12, 13, 5, 6]
↑------↑sorted portion

Pass 3 (i=3, key=5):
[11, 12, 13, 5, 6] → Compare 5 with 13 → Shift 13
[11, 12, 13, 13, 6] → Compare 5 with 12 → Shift 12
[11, 12, 12, 13, 6] → Compare 5 with 11 → Shift 11
[11, 11, 12, 13, 6] → Insert 5 at position 0
[5, 11, 12, 13, 6]
↑----------↑sorted portion

Pass 4 (i=4, key=6):
[5, 11, 12, 13, 6] → Compare 6 with 13 → Shift 13
[5, 11, 12, 13, 13] → Compare 6 with 12 → Shift 12
[5, 11, 12, 12, 13] → Compare 6 with 11 → Shift 11
[5, 11, 11, 12, 13] → Compare 6 with 5 → Stop (6 > 5)
[5, 6, 11, 12, 13] ← Sorted!
```

---

### Merge Sort

**Merge Sort** is a divide-and-conquer algorithm that divides the input array into two halves, recursively sorts them, and then merges the two sorted halves back together. It's one of the most efficient sorting algorithms with guaranteed O(n log n) time complexity.

**How it works:** Split the array in half repeatedly until you have single elements, then merge pairs of elements back together in sorted order, combining them into larger and larger sorted subarrays.

```python
def merge_sort(arr):
    if len(arr) <= 1:
        return arr
    mid = len(arr) // 2
    left = merge_sort(arr[:mid])
    right = merge_sort(arr[mid:])
    return merge(left, right)
```

#### Detailed Explanation

**Complete Code:**

```python
def merge_sort(arr):
    if len(arr) <= 1:
        return arr
    
    mid = len(arr) // 2
    left = merge_sort(arr[:mid])
    right = merge_sort(arr[mid:])
    
    return merge(left, right)

def merge(left, right):
    result = []
    i = j = 0
    
    while i < len(left) and j < len(right):
        if left[i] <= right[j]:
            result.append(left[i])
            i += 1
        else:
            result.append(right[j])
            j += 1
    
    result.extend(left[i:])
    result.extend(right[j:])
    return result
```

**Step-by-step breakdown:**

**Line 1-3:** Base case - array with 0 or 1 element is already sorted

```
If arr = [5], return [5] (already sorted)
```

**Line 5:** Find middle point to divide array

```
arr = [38, 27, 43, 3, 9, 82, 10]
mid = 7 // 2 = 3
```

**Line 6-7:** Recursively sort left and right halves

```
Original: [38, 27, 43, 3, 9, 82, 10]
          ↓ Split at index 3
Left half: arr[:3] = [38, 27, 43]
Right half: arr[3:] = [3, 9, 82, 10]
```

**Recursive splitting visualization:**

```
Level 0: [38, 27, 43, 3, 9, 82, 10]
         ↓
Level 1: [38, 27, 43]           [3, 9, 82, 10]
         ↓                       ↓
Level 2: [38, 27]  [43]         [3, 9]  [82, 10]
         ↓          ↓            ↓        ↓
Level 3: [38] [27] [43]         [3] [9]  [82] [10]
         ↑     ↑    ↑            ↑   ↑    ↑    ↑
         (All single elements - base case reached!)
```

**Line 9:** Merge sorted halves back together

```
Merging starts from bottom up (Level 3 → Level 0)
```

**Merge function detailed:**

**Line 11-13:** Initialize result array and pointers

```
left = [27, 38], right = [43]
result = []
i = 0 (points to left[0])
j = 0 (points to right[0])
```

**Line 15-21:** Compare elements and add smaller one

```
Step 1: Compare left[0]=27 with right[0]=43
        27 <= 43, so append 27
        result = [27], i=1

Step 2: Compare left[1]=38 with right[0]=43
        38 <= 43, so append 38
        result = [27, 38], i=2

Step 3: i=2 >= len(left), exit loop
```

**Line 23-24:** Add remaining elements

```
result.extend(right[0:]) → result = [27, 38, 43]
```

**Complete merging visualization:**

```
Level 3 (merge pairs):
[38] + [27] → [27, 38]
[3] + [9] → [3, 9]
[82] + [10] → [10, 82]

Level 2 (merge larger groups):
[27, 38] + [43] → [27, 38, 43]
[3, 9] + [10, 82] → [3, 9, 10, 82]

Level 1 (final merge):
[27, 38, 43] + [3, 9, 10, 82]
↓
Compare 27 and 3 → [3]
Compare 27 and 9 → [3, 9]
Compare 27 and 10 → [3, 9, 10]
Compare 27 and 82 → [3, 9, 10, 27]
Compare 38 and 82 → [3, 9, 10, 27, 38]
Compare 43 and 82 → [3, 9, 10, 27, 38, 43]
Remaining: 82 → [3, 9, 10, 27, 38, 43, 82] ← Sorted!
```

---

### Quick Sort

**Quick Sort** is another divide-and-conquer algorithm that picks an element as a pivot and partitions the array around the pivot, placing smaller elements to the left and larger elements to the right. It then recursively sorts the subarrays on either side of the pivot.

**How it works:** Choose a pivot element, rearrange the array so all elements smaller than the pivot come before it and all larger elements come after it, then recursively apply the same process to the left and right subarrays.

```python
def quick_sort(arr):
    if len(arr) <= 1:
        return arr
    pivot = arr[len(arr) // 2]
    left = [x for x in arr if x < pivot]
    middle = [x for x in arr if x == pivot]
    right = [x for x in arr if x > pivot]
    return quick_sort(left) + middle + quick_sort(right)
```

#### Detailed Explanation

**Complete Code:**

```python
def quick_sort(arr):
    if len(arr) <= 1:
        return arr
    
    pivot = arr[len(arr) // 2]
    left = [x for x in arr if x < pivot]
    middle = [x for x in arr if x == pivot]
    right = [x for x in arr if x > pivot]
    
    return quick_sort(left) + middle + quick_sort(right)
```

**Step-by-step breakdown:**

**Line 1-3:** Base case - array with 0 or 1 element

```
If arr = [5], return [5]
```

**Line 5:** Choose pivot (middle element)

```
arr = [10, 7, 8, 9, 1, 5]
pivot = arr[3] = 9

[10, 7, 8, 9, 1, 5]
         ↑ pivot = 9
```

**Line 6-8:** Partition array into three parts

```
left = elements < pivot = [7, 8, 1, 5]
middle = elements == pivot = [9]
right = elements > pivot = [10]

Visual partition:
[10, 7, 8, 9, 1, 5] with pivot=9
↓
[7, 8, 1, 5]  [9]  [10]
   ↑left      mid   ↑right
```

**Line 10:** Recursively sort and combine

```
quick_sort(left) + middle + quick_sort(right)
```

**Complete sorting visualization:**

```
Initial: [10, 7, 8, 9, 1, 5]

Recursion Level 0:
pivot = 9
[10, 7, 8, 9, 1, 5] → [7, 8, 1, 5] | [9] | [10]
                       ↓recurse        ↓done  ↓recurse

Recursion Level 1 (left subarray [7, 8, 1, 5]):
pivot = 8
[7, 8, 1, 5] → [7, 1, 5] | [8] | []
                ↓recurse    ↓done  ↓empty

Recursion Level 2 (left subarray [7, 1, 5]):
pivot = 1
[7, 1, 5] → [] | [1] | [7, 5]
             ↓empty ↓done ↓recurse

Recursion Level 3 (right subarray [7, 5]):
pivot = 5
[7, 5] → [] | [5] | [7]
          ↓empty ↓done ↓done

Now merge back up:
Level 3: [] + [5] + [7] = [5, 7]
Level 2: [] + [1] + [5, 7] = [1, 5, 7]
Level 1: [1, 5, 7] + [8] + [] = [1, 5, 7, 8]

Recursion Level 1 (right subarray [10]):
[10] (base case, already sorted)

Level 0 final merge:
[1, 5, 7, 8] + [9] + [10] = [1, 5, 7, 8, 9, 10] ← Sorted!
```

**Detailed partitioning example:**

```
Pass with arr = [3, 6, 8, 10, 1, 2, 1]
pivot = arr[3] = 10

Compare each element with pivot=10:
3 < 10 → goes to left
6 < 10 → goes to left
8 < 10 → goes to left
10 == 10 → goes to middle
1 < 10 → goes to left
2 < 10 → goes to left
1 < 10 → goes to left

Result:
left = [3, 6, 8, 1, 2, 1]
middle = [10]
right = []

Partition visualization:
[3, 6, 8, 10, 1, 2, 1]
         ↑pivot
↓
[3, 6, 8, 1, 2, 1] | [10] | []
```

---

### Heap Sort

**Heap Sort** uses a binary heap data structure to sort elements. It first builds a max heap from the input data, then repeatedly extracts the maximum element from the heap and reconstructs the heap until all elements are sorted.

**How it works:** Convert the array into a max heap where parent nodes are larger than their children, then swap the root (largest element) with the last element, reduce heap size by one, and restore the heap property. Repeat until sorted.

```python
def heap_sort(arr):
    n = len(arr)
    for i in range(n // 2 - 1, -1, -1):
        heapify(arr, n, i)
    for i in range(n - 1, 0, -1):
        arr[0], arr[i] = arr[i], arr[0]
        heapify(arr, i, 0)
    return arr
```

#### Detailed Explanation

**Complete Code:**

```python
def heap_sort(arr):
    n = len(arr)
    
    # Build max heap
    for i in range(n // 2 - 1, -1, -1):
        heapify(arr, n, i)
    
    # Extract elements one by one
    for i in range(n - 1, 0, -1):
        arr[0], arr[i] = arr[i], arr[0]
        heapify(arr, i, 0)
    
    return arr

def heapify(arr, n, i):
    largest = i
    left = 2 * i + 1
    right = 2 * i + 2
    
    if left < n and arr[left] > arr[largest]:
        largest = left
    
    if right < n and arr[right] > arr[largest]:
        largest = right
    
    if largest != i:
        arr[i], arr[largest] = arr[largest], arr[i]
        heapify(arr, n, largest)
```

**Step-by-step breakdown:**

**Understanding heap structure:**

```
Array: [4, 10, 3, 5, 1]
As binary tree:
       4
      / \
    10   3
   / \
  5   1

Parent index formula: (i-1) // 2
Left child: 2*i + 1
Right child: 2*i + 2
```

**Line 2:** Get array length

```
arr = [4, 10, 3, 5, 1]
n = 5
```

**Line 5:** Build max heap from bottom up

```
n // 2 - 1 = 5 // 2 - 1 = 1
Start from last non-leaf node (index 1)

Range: i = 1, 0 (bottom-up approach)
```

**Heapify function explained:**

**Line 16-18:** Calculate child indices

```
When i=1 (node with value 10):
left = 2*1 + 1 = 3 (node with value 5)
right = 2*1 + 2 = 4 (node with value 1)

Tree visualization:
       4
      / \
    10   3     ← Processing node 10 (index 1)
   / \
  5   1
```

**Line 20-24:** Find largest among parent and children

```
largest = 1 (initially parent)
arr[3]=5 < arr[1]=10 → largest stays 1
arr[4]=1 < arr[1]=10 → largest stays 1
Node 10 is already largest, no swap needed
```

**Building max heap visualization:**

```
Initial: [4, 10, 3, 5, 1]
            4
           / \
         10   3
        / \
       5   1

Step 1: Heapify node at index 1 (value 10)
Children: 5, 1
10 > 5 and 10 > 1 → No change
[4, 10, 3, 5, 1]

Step 2: Heapify node at index 0 (value 4)
Children: 10, 3
10 > 4 → Swap 4 and 10
[10, 4, 3, 5, 1]
           10
          / \
         4   3
        / \
       5   1

After swap, heapify node at index 1 (value 4)
Children: 5, 1
5 > 4 → Swap 4 and 5
[10, 5, 3, 4, 1]
           10         ← Max heap built!
          / \
         5   3
        / \
       4   1
```

**Line 9-11:** Extract maximum and rebuild heap

```
Iteration 1:
Swap arr[0] with arr[4]: [10, 5, 3, 4, 1] → [1, 5, 3, 4, 10]
                          ↑            ↑      ↑           ↑sorted
Heapify reduced array [1, 5, 3, 4] with i=0:
           1              5
          / \    →       / \
         5   3          1   3
        /              /
       4              4
Result: [5, 4, 3, 1, 10]

Iteration 2:
Swap arr[0] with arr[3]: [5, 4, 3, 1, 10] → [1, 4, 3, 5, 10]
                          ↑         ↑        ↑        ↑sorted
Heapify [1, 4, 3]:
         1           4
        / \    →    / \
       4   3       1   3
Result: [4, 1, 3, 5, 10]

Iteration 3:
Swap arr[0] with arr[2]: [4, 1, 3, 5, 10] → [3, 1, 4, 5, 10]
                          ↑      ↑           ↑     ↑sorted
Heapify [3, 1]:
       3
      /    (already max heap)
     1
Result: [3, 1, 4, 5, 10]

Iteration 4:
Swap arr[0] with arr[1]: [3, 1, 4, 5, 10] → [1, 3, 4, 5, 10]
                          ↑  ↑               ↑sorted
Final: [1, 3, 4, 5, 10] ← Sorted!
```

**Complete sorting process:**

```
Original: [4, 10, 3, 5, 1]

Phase 1 - Build Max Heap:
[4, 10, 3, 5, 1] → [10, 5, 3, 4, 1]

Phase 2 - Extract and Sort:
[10, 5, 3, 4, 1] → [1, 5, 3, 4] | [10]
[5, 4, 3, 1] | [10] → [1, 4, 3] | [5, 10]
[4, 1, 3] | [5, 10] → [3, 1] | [4, 5, 10]
[3, 1] | [4, 5, 10] → [1] | [3, 4, 5, 10]
[1, 3, 4, 5, 10] ← Sorted!
```

---

**Possible remaining topics:**

- Time and space complexity comparison of all algorithms
- Best/worst/average case scenarios for each algorithm
- When to use which sorting algorithm in practice
- Stable vs unstable sorting algorithms

---

**Tokens left:** 117,900 **Tokens consumed for this message:** 16,200


# Data Collection
JSON is a **format for storing and transferring data** as text. Even though it has "JavaScript" in the name, it's used by many programming languages, including Python.

example:
```python
import json

# Python dictionary
student = {
    "name": "Rawi",
    "age": 21,
    "university": "KAUST"
}

# Convert Python dict to JSON string
json_string = json.dumps(student)
print(json_string)
# Output: {"name": "Rawi", "age": 21, "university": "KAUST"}

# Convert JSON string back to Python dict
python_dict = json.loads(json_string)
print(python_dict["name"])
# Output: Rawi
```

## JSON core methods in python

`json.load`: Reads JSON from a **file** and converts to Python object
`json.loads`: Converts JSON **string** to Python object
`json.dump`:Writes Python object to a **file** as JSON
`json.dumps`:Converts Python object to JSON **string**

| Method    | Works With | Direction     |
| --------- | ---------- | ------------- |
| `loads()` | **String** | JSON → Python |
| `dumps()` | **String** | Python → JSON |
| `load()`  | **File**   | JSON → Python |
| `dump()`  | **File**   | Python → JSON |

**The "s" at the end means "string"!**
## Shallow and Deep copy

-   **shallow copy**: creates a new object and then inserts ==references== to the original object's _elements_ into the new object.
- **deep copy** creates a new object and then recursively inserts _copies_ of the original object's elements into the new object.

python has methods for these type of copies which is  `copy.deepcopy()`  and `copy.copy()`

example using deepcopy:
```python
import copy

a = [[1, 2, 3], [4, 5, 6]]

# Creating a deep copy of the nested list 'a'
b = copy.deepcopy(a)

# Modifying an element in the deep-copied list
b[0][0] = 99 
print(b)
print(a)

"""
[[99, 2, 3], [4, 5, 6]]
[[1, 2, 3], [4, 5, 6]]
"""
```

example on shallow copy:
```python
import copy  

a = [[1, 2, 3], [4, 5, 6]]

# Creating a shallow copy of the nested list 'original'
b = copy.copy(a)

# Modifying an element in the shallow-copied list
b[0][0] = 99

# Printing the original and shallow-copied lists  
print(b)
print(a)
"""
[[99, 2, 3], [4, 5, 6]]
[[99, 2, 3], [4, 5, 6]]
"""
```

## Map and Filter and Zip 
 `map` edits the given sequence
  `reduce` gathers them up to a single value 
  `filter` returns a new list but the passed function has to be boolean 
### Map
[map() function](https://www.geeksforgeeks.org/python/python-map-function/) returns a map ****object(which is an iterator)**** of the results after applying the given function to each item of a given iterable ([list](https://www.geeksforgeeks.org/python/python-lists/), [tuple](https://www.geeksforgeeks.org/python/python-tuples/), etc.).

****Syntax****:
` map(fun, iter)`
****Parameters:****
- ****fun:**** It is a function to which map passes each element of given iterable.
- ****iter:**** iterable object to be mapped.
****Example:**** The following code doubles each element in a list using map().
```python
def double(n):

    return n * 2


n = [5, 6, 7, 8]
res = map(double, n)
print(list(res))

"""
**Output**

[10, 12, 14, 16]
"""
```

### reduce
The [reduce](https://www.geeksforgeeks.org/python/reduce-in-python/) function is used to apply a function passed in its argument to all of the list elements mentioned in the sequence passed along and ==the result will be a single value.==This function is defined in "functools" module.

syntax:
`reduce(func , iterable)`

parameters:
- `fun`: the function will be executed on the given iterable 
- `iter`: it is the iterable to be reduced 

example:
```python
import functools

n = [1, 2, 3, 4]

prod = functools.reduce(lambda x, y: x * y, n)
print(prod)
"""

24

"""
```

### filter
The [filter() method](https://www.geeksforgeeks.org/python/filter-in-python/) filters the given sequence with the help of a function that tests each element in the sequence to be true or not.
==Here you have to pass a function that returns boolean results==

syntax:
`filter(boolFunc , sequence)`
- `boolFunc`: a function that returns boolean
- `sequence`: list,dict what ever thing u have

example:
```python
def is_even(n):
    return n % 2 == 0

n = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

en = filter(is_even, n)
print(list(en))

"""
[2, 4, 6, 8, 10]
"""
```

## Zip
The zip() function in Python is used to combine two or more iterables (like lists, tuples, strings, dictionaries, etc.) into a single iterator of tuples. Each tuple contains elements that share the same index across the input iterables.

syntax:
`zip(*iterables)`
or like this
`zip(list1, list2, list3 ....)`

example:
```python
# input
a = ["Liam", "Emma", "Noah"], b = [90, 85, 88] 

zip(a,b)

# output  
[("Liam", 90), ("Emma", 85), ("Noah", 88)]
```

Key point:
- If no parameters are passed, zip() returns an empty iterator.
- If only one iterable is passed, the result will be a series of single-element tuples.
- If multiple iterables are passed, each tuple will contain one element from each iterable.
# List comprehension
it's an approach to create new lists using inline-expressions (same style as ternary and lambda)

syntax:
```python
result = [expression for item in iterable if condition]
```

- **`expression`** -operation applied to each item (what goes into the new list)
- **`item`** - The loop variable (accumulator) that takes each value from the iterable
- **`iterable`** - The existing sequence (list, tuple, range, etc.) you're iterating over
- **`if condition`** - Optional filter that determines which items are included (can be omitted)

examples:
```python
# no condition
squares = [x**2 for x in [1, 2, 3, 4, 5]]
# [1, 4, 9, 16, 25]

#with condition
evens = [x for x in [1, 2, 3, 4, 5, 6] if x % 2 == 0]
# [2, 4, 6]

#with transformation and condition
doubled_evens = [x*2 for x in [1, 2, 3, 4, 5, 6] if x % 2 == 0]
# [4, 8, 12]
```

# Simple look at HTTP in python
## Define
**API** stands for "Application Programming Interface." In simple terms, it's a set of rules and protocols that allow how different software applications can communicate and interact with each other

in this section we gonna use `requests` library which gonna learn how to use HTTP operations and interacting with API in python

**Requests Library** is a simple and powerful tool to send ****HTTP**** requests and interact with web resources. It allows you to easily send ****GET****, ****POST****, ****PUT****, ****DELETE****, ****PATCH****, ****HEAD**** requests to web servers, handle responses, and work with REST APIs and web scraping tasks.

 Why do we need Requests Library
- ****Simplifies HTTP Requests:**** Makes sending GET, POST, PUT, DELETE, etc., as easy as calling a function.
- ****Manages Sessions & Auth:**** Handles cookies, sessions, headers, and various authentication methods.
- ****Ideal for REST APIs:**** Perfect for consuming or testing RESTful services.
- ****Supports All HTTP Methods:**** Full support for common and custom HTTP verbs.
- ****Built-in SSL & Error Handling:**** Automatically manages SSL (Secure Socket Layer) verification and error handling.

Syntax to make a GET requestL:
`requests.get(url, params={key: value}, **kwargs)`
- ****url:**** The URL you want to send the request to. (Required)
- ****params:**** Dictionary or bytes to be sent in the query string for GET requests. (Optional)
- ******kwargs:**** Additional optional arguments such as headers, cookies, authentication, timeout, proxies, verify (SSL), stream, etc.
Returns : response object

example:
```python
import requests
response = requests.get("https://www.geeksforgeeks.org/")
print(response.status_code)

# outputs; 200
```

another example but with sending parameters:
```python
import requests
response = requests.get("https://api.github.com/users/naveenkrnl")
print(response.status_code)
print(response.content)

"""
output 200

some alot of contents
"""
```


# OOP 
## Define
OOP is a way of organizing code that uses objects and classes to represent real-world entities and their behavior. In OOP, object has attributes thing that has specific data and can perform certain actions using methods.
 
 **Key Features of OOP in Python:**
- Organizes code into classes and objects
- Supports encapsulation to group data and methods together
- Enables inheritance for reusability and hierarchy
- Allows polymorphism for flexible method implementation
- Improves modularity, scalability, and maintainability

OOP principles:
- **class**: Blue-Print for object
- **object**: instance (copy) of a class where it starts to hold data and implementations
- **Encapsulation**: hiding and restricting access to some outer components
- **Polymorphism**: can be described with this sentence `same operation name, diffrent behaviour` 
- **Abstraction**: hides the internal implementation details while exposing only the necessary functionality.
- **Inheritance**: is inheriting a class to another child class, that holds the super class info adding some special info for the child class 

## OOP Principle: Class
classes have three components:
- attributes which holds data
- methods which holds behavior 
- Constructor: which takes parameters when a new object is initialized

>Note: from now on i'll start to use same example on coming explanations, so class "person" will be ur friend :) 

Creating a class:
```python
class person:
	name = "rawi"
	age = 0
	
	def __init__(self, name, age):
		self.name = name
		self.age = age
```
- `class person`: define a class called person
- `name , age`: attributes shared all along objects
- `def __init__(self, name, age)`: constructor that takes parameters on first initializing 

## OOP Principle: Object
objects consists of :
- State: the data or variables inside the object 
- Behavior: shows methods action and their reflection on object's data(attributes)
- Identity: the unique name given to the object like `car1` , `dog3` that enables you to control a specific object
Creating an Object:
```python
class person:
	name : str
	age : int
	
	def __init__(self, name, age):
		self.name = name
		self.age = age
		
### v object area v ###

person1 = person("rawi" , 20)

print(person1.name)
print(person1.age)

"""
outputts:

rawi
20
"""
```

## OOP Principle: Inheritance
allows a class (child class) to acquire properties and methods of another class (parent class). It supports hierarchical classification and promotes code reuse.
![[Screenshot From 2025-11-27 14-08-59.png| 500]]
Types of inheritance:
1. ****Single Inheritance:**** A child class inherits from a single parent class.
2. ****Multiple Inheritance:**** A child class inherits from more than one parent class.
3. ****Multilevel Inheritance:**** A child class inherits from a parent class, which in turn inherits from another class.
4. ****Hierarchical Inheritance:**** Multiple child classes inherit from a single parent class (like the image above).
5. ****Hybrid Inheritance:**** A combination of two or more types of inheritance.

Example:
in this example i will try to apply most of the inheritance types in one example but first i'll show the single type then i'll show some combinations:
Single inheritance:
```python

## Super Class
class person:
	Name = None
	Age = None
	
	def identify(self):
		print(f"my name is: {self.Name} and am {self.Age} old")
	
	def __init__(self, Name, Age):
		self.Name=Name
		self.Age = Age
		

# Child class
class Child (person): # To actually inherit u pass the class in parameter 
	FavGame = None # some attributes for child class 
	FavCandy = None
	
	def identify(): # applied polmorphism by accident, i'll talk about it later 
		print(f"my name is: {self.Name} and am {self.Age} old and my favourite game is {self.FavGame} and i like to eat {self.FavCandy}.")
	
	def __init__(self, Name, Age, FavGame, FavCandy):
		self.Name=Name
		self.Age = Age
		self.FavGame = FavGame
		self.FavCandy = FavCandy
	
```

(Single + Multilevel + Multi Inheritance(multiple parents)) example
```python
# Single Inheritance
class Person:
    def __init__(self, Name, Age):
        self.Name = Name
        self.Age = Age

    def identify(self):
        print(f"my name is: {self.Name} and am {self.Age} old")

class Child(Person):  # Single Inheritance
    def __init__(self, Name, Age, FavGame, FavCandy):
        self.Name = Name
        self.Age = Age
        self.FavGame = FavGame
        self.FavCandy = FavCandy
    
    def identify(self):
        print(f"my name is: {self.Name} and am {self.Age} old, I love {self.FavGame} and {self.FavCandy}")
    
    def play(self):
        print(f"{self.Name} plays")

####################### Multilevel Inheritance######################
class Toddler(Child):  # Multilevel Inheritance | toddler > child > person
    def __init__(self, Name, Age, FavGame, FavCandy, FavToy, NapTime):
        self.Name = Name
        self.Age = Age
        self.FavGame = FavGame
        self.FavCandy = FavCandy
        self.FavToy = FavToy
        self.NapTime = NapTime
    
    def identify(self):
        print(f"my name is: {self.Name} and am {self.Age} old, I love {self.FavGame}, {self.FavCandy}, my toy is {self.FavToy}, nap at {self.NapTime}")
    
    def crawl(self):
        print(f"{self.Name} crawls around!")

############# Multiple Inheritance ###############
class Friendly: # addtional parent
    def greet(self):
        print("Friendly!")

class Teen(Person, Friendly):  # Multiple Inheritance | Teen > Person + Friendly
    def __init__(self, Name, Age, FavSport, FavSubject):
        self.Name = Name
        self.Age = Age
        self.FavSport = FavSport
        self.FavSubject = FavSubject
    
    def identify(self):
        print(f"my name is: {self.Name} and am {self.Age} old, I like {self.FavSport} and {self.FavSubject}")
    
    def play(self):
        print(f"{self.Name} plays video games")

# Example Usage
child = Child("Hedla", 2, "Lego", "IceCreams")
child.identify()
child.play()

toddler = Toddler("Baby", 1, "Peek-a-boo", "Gummies", "Teddy Bear", "2 PM")
toddler.identify()
toddler.crawl()

teen = Teen("Rawi", 21, "Football", "Math")
teen.identify()
teen.greet()
teen.play()
```

## OOP Principle: Polymorphism
Polymorphism in Python means "same operation, different behavior." It allows functions or methods with the same name to work differently depending on the type of object they are acting upon.

Types of Polymorphism:
- Compile Time Polymorphism
	- Method overloading
- RunTime polymorphism
	- method overloading
	- Duck typing
	- Operator overloading

**Method Overriding (Runtime Polymorphism)**
Child class redefines a method from parent class.
```python
class Parent:
    def speak(self):
        print("Parent speaks")

class Child(Parent):
    def speak(self):  # Override
        print("Child speaks")

obj = Child()
obj.speak()  # Output: Child speaks
```

**Method Overloading (Compile Time Polymorphism)**
Python doesn't support true method overloading like Java. Only the last definition survives.
```python
class Math:
    def add(self, a, b):
        return a + b
    
    def add(self, a, b, c):  # This replaces previous add()
        return a + b + c

obj = Math()
# obj.add(2, 3)  # Error: missing 1 required argument
obj.add(2, 3, 4)  # Works: 9
```
Instead: use default parameters
```python
class Math:
    def add(self, a, b, c=0):
        return a + b + c

obj = Math()
obj.add(2, 3)      # 5
obj.add(2, 3, 4)   # 9
```

**Duck Typing (Runtime Polymorphism)**
"If it walks like a duck and quacks like a duck, it's a duck."
Objects are used based on methods they have, not their class type.
```python
class Dog:
    def speak(self):
        print("Woof")

class Cat:
    def speak(self):
        print("Meow")

def make_speak(animal):  # Accepts any object with speak()
    animal.speak()

make_speak(Dog())  # Woof
make_speak(Cat())  # Meow
```

**Operator Overloading (Runtime Polymorphism)**
Redefine how operators work with custom objects.
```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y
    
    def __add__(self, other):  # Override + operator
        return Point(self.x + other.x, self.y + other.y)

p1 = Point(1, 2)
p2 = Point(3, 4)
p3 = p1 + p2  # Uses __add__
print(p3.x, p3.y)  # Output: 4 6
```

## OOP Principle: Encapsulation
bundling of data (attributes) and methods (functions) within a class, restricting access to some components to control interactions.

**Types of encapsulation:**
- **Public Members ( + )**: accessible from anywhere 
- **Private Members ( - )**: only accessible within class only
- **Protected Members ( # )**: only accessible within the class and sub classes

Example:
```python
class Dog:
    def __init__(self, name, breed, age):
        self.name = name  # Public attribute
        self._breed = breed  # Protected attribute
        self.__age = age  # Private attribute

    # Public method
    def get_info(self):
        return f"Name: {self.name}, Breed: {self._breed}, Age: {self.__age}"

    # Getter and Setter for private attribute
    def get_age(self):
        return self.__age

    def set_age(self, age):
        if age > 0:
            self.__age = age
        else:
            print("Invalid age!")

# Example Usage
dog = Dog("Buddy", "Labrador", 3)

# Accessing public member
print(dog.name)  # Accessible

# Accessing protected member
print(dog._breed)  # Accessible but discouraged outside the class

# Accessing private member using getter
print(dog.get_age())

# Modifying private member using setter
dog.set_age(5)
print(dog.get_info())
```
****Explanation:****

- ****Public Members:**** Easily accessible, such as name.
- ****Protected Members****: Used with a single underscore "_", such as` _breed`. 
  Access is discouraged but allowed in subclasses.
- ****Private Members:**** Used with double underscore such as  `__ age`. 
  Access requires getter and setter methods

## OOP Principle: Abstraction
hides the internal implementation details while exposing only the necessary functionality. It helps focus on "what to do" rather than "how to do it."

Types of abstraction:
- **Partial:** Abstract class contains both abstract and concrete methods.
- **Full:** Abstract class contains only abstract methods (like interfaces)


## Extra Tools in OOP for python "important to see"
### Wrapping and Decorators "@"
**Wrapping:**
Function that takes another function and extends its behavior without modifying it.
```python
def uppercase_decorator(func):
    def wrapper():
        result = func()
        return result.upper()
    return wrapper

@uppercase_decorator  # Applies decorator
def greet():
    return "hello"

print(greet())  # Output: HELLO

```

**Function Decorator "@"**
Same as wrapping, uses `@` syntax to apply function modifications.
```python
def repeat(func):
    def wrapper(*args, **kwargs):
        func(*args, **kwargs)
        func(*args, **kwargs)  # Executes twice
    return wrapper

@repeat
def say_hello(name):
    print(f"Hello {name}")

say_hello("Rawi")
# Output:
# Hello Rawi
# Hello Rawi
```

Difference between wrapping and decorating is decorating is much cleaner way to extend the function (which is the mission of both types) 
like in here:

**Wrapping = Manual application:**
```python
def greet():
    return "hello"

greet = uppercase_decorator(greet)  # Manual wrapping
```

**Decorator = Syntactic sugar (shortcut):**
```python
@uppercase_decorator  # Automatic wrapping
def greet():
    return "hello"
```

Both do: `greet = uppercase_decorator(greet)`
Decorator syntax (`@`) is just cleaner way to write wrapping.

#### Example on how decorators work
**What is a Decorator**

A function that wraps another function to add extra behavior without changing the original function's code.

so am gonna use this example to explain how decorators work:
```python
import functools
import time

def performance(func):
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        start_time = time.perf_counter()
        result = func(*args, **kwargs)
        end_time = time.perf_counter()
        elapsed_time = end_time - start_time
        return result, elapsed_time
    return wrapper

```

**Breaking Down the Code**
```python
def performance(func):  # Takes original function as input
```
`performance` receives the function you want to wrap (e.g., `add`)

```python
    @functools.wraps(func)
    def wrapper(*args, **kwargs):  # New function that replaces original
```
Creates a new function called `wrapper` that will run instead of the original. `*args, **kwargs` accepts any arguments the original function needs.

```python
        start_time = time.perf_counter()  # Before running
```
Records time before executing original function.

```python
        result = func(*args, **kwargs)  # Run original function
```
Executes the actual function you decorated and saves its return value.

```python
        end_time = time.perf_counter()  # After running
        elapsed_time = end_time - start_time  # Calculate duration
```
Records time after execution and calculates how long it took.

```python
        return result, elapsed_time  # Return modified output
```
Returns both the original result AND the time taken (modified behavior).

```python
    return wrapper  # Return the new function
```
Returns `wrapper` function which replaces the original.

**How It Works in Practice**
```python
@performance
def add(a, b):
    return a + b
```
**This is equivalent to:**
```python
def add(a, b):
    return a + b

add = performance(add)  # Replaces add with wrapped version
```

**When you call:**
```python
result, time_taken = add(1, 2)```

**What actually happens:**

1. Calls `wrapper(1, 2)` (not original `add`)
2. `wrapper` records start time
3. `wrapper` calls original `add(1, 2)` → returns 3
4. `wrapper` records end time
5. `wrapper` calculates elapsed time
6. `wrapper` returns (3, 0.00001)

**Simple Analogy**

Original function = Gift Decorator = Gift wrapper that adds ribbon and card You still get the gift, but with extras

---

**Tokens left:** 66,400 **Tokens consumed for this message:** 530
### Dunderscore ( _ _underscore methods_ _)

Special methods with double underscores. Also called magic methods.
but keep an eye on how they called cuz it's kinda tricky (uses print method)

**Types:**
- `__init__`: Constructor
- `__str__` and `__repr__` : String representation
- `__add__`: Addition operator
- `__len__`: Length
- `__eq__`: Equality comparison
  
>Note: `__repr__` also shows string but shows the constructor with it which helps developers to see what parameters passed 
```python
class Book:
    def __init__(self, title, pages):
        self.title = title
        self.pages = pages
    
    def __str__(self):  # String representation
        return f"{self.title} ({self.pages} pages)"
    
    def __add__(self, other):  # Addition operator
        return Book(f"{self.title} & {other.title}", self.pages + other.pages)
    
    def __len__(self):  # Length
        return self.pages
    
    def __eq__(self, other):  # Equality
        return self.pages == other.pages

book1 = Book("Python", 200)
book2 = Book("Java", 300)

print(book1)  # Python (200 pages) | called __str__
print(book1 + book2)  # Python & Java (500 pages) | called __add__
print(len(book1))  # 200 | called __len__
print(book1 == book2)  # False | called __eq__
```

**Overriding vs Overloading**
**Overriding:** Child class redefines parent method (same name, replaces parent version)
```python
class Parent:
    def speak(self):
        print("Parent speaks")

class Child(Parent):
    def speak(self):  # OVERRIDE: Same name as parent
        print("Child speaks")

obj = Child()
obj.speak()  # Child speaks (parent version replaced)
```

**Overloading:** Multiple methods with same name but different parameters (Python doesn't support true overloading)
```python
class Math:
    def add(self, a, b, c=0):  # OVERLOAD workaround: default parameter
        return a + b + c

obj = Math()
print(obj.add(2, 3))      # 5
print(obj.add(2, 3, 4))   # 9
# Same method name, different number of arguments
```

**How to tell difference:**
- **Overriding:** Parent + Child classes, same method name
- **Overloading:** One class, same method name, different parameters

**@property Decorator**
Makes a method accessible like an attribute (getter). Allows controlled access.
```python
class Person:
    def __init__(self, name, age):
        self._name = name  # Private attribute
        self._age = age
    
    @property  # Getter
    def name(self):
        return self._name.upper()
    
    @property
    def age(self):
        return self._age
    
    @age.setter  # Setter
    def age(self, value):
        if value < 0:
            print("Age cannot be negative")
        else:
            self._age = value

person = Person("rawi", 21)
print(person.name)  # RAWI (accessed like attribute, not person.name())
print(person.age)   # 21

person.age = 22  # Uses setter
print(person.age)  # 22

person.age = -5  # Age cannot be negative
```




# Try Except 
## Try Except
errors and exceptions can interrupt the execution of program. Python provides ****try and except**** blocks to handle situations like this. In case an error occurs in try-block, Python stops executing try block and jumps to exception block. These blocks let you handle the errors without crashing the program.
```python
try:
	x= 3/0
	print(x)
except:
	print("an exception occuerd")
	
#outputs: an exception occuerd
```

Why do we need try-except
- Prevents crashes caused by runtime errors.
- Handles specific exceptions like division by zero, file not found, etc.
- Improves code reliability and error tolerance.
- Allows custom error messages and fallback logic.
- Essential for robust, debuggable applications.

How try() works? 
- First, the ****try**** clause is executed i.e. the code between ****try.****
- If there is no exception, then only the ****try**** clause will run, ****except**** clause is finished.
- If any exception occurs, the ****try**** clause will be skipped and ****except**** clause will run.
- If any exception occurs, but the ****except**** clause within the code doesn't handle it, it is passed on to the outer ****try**** statements. If the exception is left unhandled, then the execution stops.
- A ****try**** statement can have more than one ****except**** clause

**Some of the common Exception Errors are :***
- ****IOError:**** if the file can't be opened
- ****KeyboardInterrupt:**** when an unrequired key is pressed by the user
- ****ValueError:**** when the built-in function receives a wrong argument
- ****EOFError:**** if End-Of-File is hit without reading any data
- ****ImportError:**** if it is unable to find the module

## Try Except using specific error
another way of writing excpetion, is only accepting excpetions that you're meant to catch or check what error is occuring like in this example 
```python 
def div (x,y):
	try:
		result = x // y
		print("Division done" , result)
		
	except Excpetion as e :#<<
	
	print("Error: ",e)
div(3,"r")
div(3,0)
div(3,3)

```

## Else Clause
you can also use the else clause on the try-except block which must be present after all the except clauses. *The code enters the else block only if the try clause does not raise an exception.*
Syntax:
```
 try:  
 # Some Code  
 except:  
 # Executed if error in the  
 # try block  
 else:  
 # execute if no exception
```

example:
```python
# Program to depict else clause with try-except
 
# Function which returns a/b
def AbyB(a , b):
    try:
        c = ((a+b) // (a-b))
    except ZeroDivisionError:
        print ("a/b result in 0")
    else:
        print (c)
 
# Driver program to test above function
AbyB(2.0, 3.0)
AbyB(3.0, 3.0)
```

```
-5.0
a/b result in 0
```

## Finally Keyword
Python provides a keyword finally, **which is always executed after the try and except blocks**. The final block always executes after the normal termination of the try block or after the try block terminates due to some exceptions.
Syntax:
```
	try:  
		# Some Code  
	except:  
		# Executed if error in the  
		# try block  
	else:  
		# execute if no exception  
	finally:  
		# Some code .....(always executed)
```
example:
```python
# Python program to demonstrate finally 
# No exception Exception raised in try block 
try: 
    k = 5//0 # raises divide by zero exception. 
    print(k) 
   
# handles zerodivision exception     
except ZeroDivisionError:    
    print("Can't divide by zero") 
       
finally: 
    # this block is always executed  
    # regardless of exception generation. 
    print('This is always executed')
```

# NumPy Arrays
stands for Numerical Python and is used for handling large, multi-dimensional arrays and matrices. Unlike Python's built-in lists NumPy arrays provide efficient storage and faster processing for numerical and scientific computations. It offers functions for linear algebra and random number generation making it important for data science and machine learning.

before we go , you gonna see in the syntax like this 
```python 
import numpy as np

a=[1,2,3,4,5]
arr = np.array(a)
```
what happened here that he's returning an object (array followed with some functions), even if you check it's type you'll see that it's numpy class  
## Types of Arrays
### One-dimensional Array
it's simply a linear array

Syntax and example:
```python 
import numoy as np

a = [1,2,3,4,5] #normal python list
arr = np.array(a) # changing normal list to numpy array
print(a)
print(arr)

print(type(a))
print(type(arr))
```

```
[1,2,3,4,5]
[1 2 3 4 5]

<<class 'list'>>
<<class 'numpy.ndarray'>>
```

### Multi-Dimensional Array
A Multi-Dimensional Array is an array that can store data in more than one dimension such as rows and columns. In simple terms it is a array of arrays.

example:
```python
import numpy as np

list_1 = [1, 2, 3, 4]
list_2 = [5, 6, 7, 8]
list_3 = [9, 10, 11, 12]

sample_array = np.array([list_1, list_2, list_3])

print("Numpy multi dimensional array in python\n",sample_array) 
```

```
Numpy multi dimensional array in python
 [[ 1  2  3  4]
 [ 5  6  7  8]
 [ 9 10 11 12]]
```

## Parameters 
when you make your numpy array there's some functions you can make on your array and here's some 

Parameters:
- `.array(list)` : returns numpy array
  example:
  ```python
	import numpy as np

	arr = np.array([3,4,5,5])

	print("Array :",arr)
  ```

```
Array : [3 4 5 5]
```

- `.formiter()`: function create a new one-dimensional array from an iterable object.
	example:
	```python
	import numpy as np

var = "Geekforgeeks"

arr = np.fromiter(var, dtype = 'U2')

print("fromiter() array :",arr)
	```

```
fromiter() array : ['G' 'e' 'e' 'k' 'f' 'o' 'r' 'g' 'e' 'e' 'k' 's']
```

- `.arange()`: This is an inbuilt NumPy function that returns evenly spaced values within a given interval.
  more explaining , 
  syntax: `np.arange(start,stop,space,dtype)`
example:
```python
import numpy as np

np.arange(1, 20 , 2, dtype = np.float32)
arr2 = np.arange(1,10,2,dtype=np.float32)
```

```
array([ 1.,  3.,  5.,  7.,  9., 11., 13., 15., 17., 19.], dtype=float32)
array([1. 3. 5. 7. 9.])
```

- `linspace()`: This function returns evenly spaced numbers over a specified between two limits.
example:
```python
import numpy as np

np.linspace(3.5, 10, 3, dtype = np.int32)
```

```
array([ 3, 6, 10], dtype=int32)
```

- `.empty()`:This function create a new array of given shape and type without initializing value.
```python 
import numpy as np

print(np.empty([4, 3],
		dtype = np.int32,
		order = 'f'))
```

```
[[871062243         0         0]
 [        0         0         0]
 [        0         0         0]
 [        0         0         0]]
```
>Note: the 8 Billion thing appeared that because `.empty()` allocates memory but not cleans it , so this long number was there before even memory is allocated, so i guess u have to clean it before interacting with it  

- `.zeros()`: This function is used to get a new array of given shape and type filled with zeros (0).
```python
import numpy as np

print(np.empty([4, 3],
		dtype = np.int32,
		order = 'f'))
```

```
[[0 0 0]
 [0 0 0]
 [0 0 0]
 [0 0 0]]
```


# RegEx

## RegEx Define
A Regular Expression or RegEx is a special sequence of characters that uses a search pattern to find a string or set of strings.

It can detect the presence or absence of a text by matching it with a particular pattern and also can split a pattern into one or more sub-patterns.

**Regex Module in Python**
Python has a built-in module named "****re"**** that is used for regular expressions in Python. We can import this module by using [import statement](https://www.geeksforgeeks.org/python/import-module-python/).

Importing re module in Python using following command:
`import re`

**How to use it ?**
This Python code uses regular expressions to search for the word ****"portal"**** in the given string and then prints the start and end indices of the matched word within the string.
```python
import re

s = 'GeeksforGeeks: A computer science portal for geeks'
match = re.search(r'portal', s)

print('Start Index:', match.start())
print('End Index:', match.end())
```

```
Start Index: 34
End Index: 40
```
>****Note:**** Here r character (r’portal’) stands for raw, not regex. The raw string is slightly different from a regular string, it won’t interpret the \ character as an escape character. This is because the regular expression engine uses \ character for its own escaping purpose.

## RegEx Functions 

### `re.findall()`: finds and returns all matching 
Returns all non-overlapping matches of a pattern in the string as a list. It scans the string from left to right.

****Example:**** This code uses regular expression ****\d+**** to find all sequences of one or more ==digits== in the given string.
```python
import re
string = """Hello my Number is 123456789 and
            my friend's number is 987654321"""
            
regex = '\d+'
match = re.findall(regex, string)
print(match)
```

```
['123456789', '987654321']
```
--- 

### `re.compile()`:
Compiles a regex into a pattern object, which can be reused for matching or substitutions.

****Example 1:**** This pattern ****[a-e]**** matches all lowercase letters between '****a****' and '****e****', in the input string ****"Aye, said Mr. Gibenson Stark".**** The output should be ****['e', 'a', 'd', 'b', 'e']****, which are matching characters.
```python
import re
p = re.compile('[a-e]')
print(p.findall("Aye, said Mr. Gibenson Stark"))
```

```
['e', 'a', 'd', 'b', 'e', 'a']
```
****Explanation:****
- First occurrence is 'e' in "Aye" and not 'A', as it is Case Sensitive.
- Next Occurrence is 'a' in "said", then 'd' in "said", followed by 'b' and 'e' in "Gibenson", the Last 'a' matches with "Stark".
- Metacharacter backslash '\' has a very important role as it signals various sequences. If the backslash is to be used without its special meaning as metacharacter, use'\\'

****Example 2:**** The code uses regular expressions to find and list all single digits and sequences of digits in the given input strings. It finds single digits with ****\d**** and sequences of digits with ****\d+****.
```python
import re
p = re.compile('\d')
print(p.findall("I went to him at 11 A.M. on 4th July 1886"))

p = re.compile('\d+')
print(p.findall("I went to him at 11 A.M. on 4th July 1886"))
```

```
['1', '1', '4', '1', '8', '8', '6']
['11', '4', '1886']
```


****Example 3:**** Word and non-word characters

- ****\w**** matches a single word character.
- ****\w+**** matches a group of word characters.
- ****\W**** matches non-word characters.

```python
import re

p = re.compile('\w')
print(p.findall("He said * in some_lang."))

p = re.compile('\w+')
print(p.findall("I went to him at 11 A.M., he \
said *** in some_language."))

p = re.compile('\W')
print(p.findall("he said *** in some_language."))
```

```
['H', 'e', 's', 'a', 'i', 'd', 'i', 'n', 's', 'o', 'm', 'e', '_', 'l', 'a', 'n', 'g']

['I', 'went', 'to', 'him', 'at', '11', 'A', 'M', 'he', 'said', 'in', 'some_language']

[' ', ' ', '*', '*', '*', ' ', ' ', '.']
```


****Example 4:**** The regular expression pattern '****ab*****' to find and list all occurrences of '****ab****' followed by zero or more '****b****' characters. In the input string "ababbaabbb". It returns the following list of matches: ['ab', 'abb', 'abbb'].
```python
import re
p = re.compile('ab*')
print(p.findall("ababbaabbb"))
```

```
['ab', 'abb', 'a', 'abbb']
```

****Explanation:****
- Output 'ab', is valid because of single 'a' accompanied by single 'b'.
- Output 'abb', is valid because of single 'a' accompanied by 2 'b'.
- Output 'a', is valid because of single 'a' accompanied by 0 'b'.
- Output 'abbb', is valid because of single 'a' accompanied by 3 'b'.
--- 
### `re.split()`
Splits a string wherever the pattern matches. The remaining characters are returned as list elements.
syntax:`re.split(pattern, string, maxsplit=0, flags=0)`
- ****pattern:**** Regular expression to match split points.
- ****string:**** The input string to split.
- ****maxsplit (optional):**** Limits the number of splits. Default is 0 (no limit).
- ****flags (optional):**** Apply regex flags like re.IGNORECASE.

****Example 1: Splitting by non-word characters or digits****
This example demonstrates how to split a string using different patterns like non-word characters (\W+), apostrophes, and digits (\d+).
```python
from re import split

print(split('\W+', 'Words, words , Words'))
print(split('\W+', "Word's words Words"))
print(split('\W+', 'On 12th Jan 2016, at 11:02 AM'))
print(split('\d+', 'On 12th Jan 2016, at 11:02 AM'))
```

```
['Words', 'words', 'Words']
['Word', 's', 'words', 'Words']
['On', '12th', 'Jan', '2016', 'at', '11', '02', 'AM']
['On ', 'th Jan ', ', at ', ':', ' AM']
```




# Pandas Define
Pandas (stands for Python Data Analysis) is an open-source software library designed for ****data manipulation**** and ****analysis****.

- Revolves around two primary Data structures: **Series (1D)** and **DataFrame (2D)**
- Built on top of NumPy, efficiently manages large datasets, offering tools for data cleaning, transformation, and analysis.
- Tools for working with time series data, including date range generation and frequency conversion. For example, we can convert date or time columns into pandas’ datetime type using `pd.to_datetime()`, or specify `parse_dates=True` during CSV loading.
- Seamlessly integrates with other Python libraries like NumPy, Matplotlib, and scikit-learn.
- Provides methods like `.dropna()` and `.fillna()` to handle missing values seamlessly

****Important Facts to Know :****
- ****DataFrames:**** It is a two-dimensional data structure constructed with rows and columns, which is more similar to Excel spreadsheet.
- ****pandas:**** This name is derived for the term "panel data" which is [econometrics](https://www.geeksforgeeks.org/school-resources/econometrics-meaning-examples-theory-and-methods/) terms of data sets.

What is Pandas Used for?
- Reading and writing data from various file formats like CSV, Excel and SQL databases.
- Cleaning and preparing data (handling missing values, filtering, removing duplicates).
- Merging, joining, and reshaping datasets.
- Performing statistical analysis and descriptive statistics.
- Visualizing data quickly

## Data structures
### Series
A [Pandas Series](https://www.geeksforgeeks.org/python/python-pandas-series/) is one-dimensional labeled array capable of holding data of any type (integer, string, float, Python objects etc.). The axis labels are collectively called indexes.
example:
```python
import pandas as pd
import numpy as np

s=pd.Series() #made a new empty series
Data = np.array([11,22,33,44,55]) # you can use regular lists
s = pd.Series(Data) # saving data
print(s)

s_Indx = pd.Series(Data,index=["a","b","c","d"]) # Adding index to data
print(s_indx)
```

```
0    11
1    22
2    33
3    44
4    55
dtype: int64
a    11
b    22
c    33
d    44
e    55
dtype: int64

```

#### Appending Series
appending is simple you just gonna use `.append` :)


Nothing actually to talk about , all the work is in Dataframes

### DataFrame
A Pandas DataFrame is a two-dimensional table-like structure in Python where data is arranged in rows and columns.

The main parts of a DataFrame are:
- ****Data****: Actual values in the table.
- ****Rows****: Labels that identify each row.
- ****Columns***: Labels that define each data category.
  ![[Screenshot From 2025-12-10 21-21-52.png]]

#### **Creating Dataframe with lists** 

you can make Dataframes out of lists just like in this example 
```python 
import pandas as pd 
lst=["fuck","python","100","times"]

df = pd.DataFrame(lst)
print(df)
```

```
0    fuck
1  python
2     100
3   times
```

#### Creating Dataframe from dict of lists
We can create a DataFrame from a dictionary where the keys are column names and the values are lists or arrays.

- All arrays/lists must have the same length.
- If an index is provided, it must match the length of the arrays.
- If no index is provided, Pandas will use a default range index (0, 1, 2, …).

```python
import pandas as pd

data = {"name":["rawi","bahia","nick","krish"],
"age":[20,20,16,19]}

df = pd.DataFrame(data)
print(df)
```

```
    name  age
0   rawi   20
1  bahia   20
2   nick   16
3  krish   19
```

#### Working with Dataframes

##### Selection 
###### Colmun Selection
In Order to [select a column](https://www.geeksforgeeks.org/python/how-to-select-multiple-columns-in-a-pandas-dataframe/) in Pandas DataFrame, we can either access the columns by calling them by their columns name.
```python
import pandas as pd
 
data = {'Name':['Jai', 'Princi', 'Gaurav', 'Anuj'],
        'Age':[27, 24, 22, 32],
        'Address':['Delhi', 'Kanpur', 'Allahabad', 'Kannauj'],
        'Qualification':['Msc', 'MA', 'MCA', 'Phd']}
 
df = pd.DataFrame(data)
 
print(df[['Name', 'Age']])
```

```
     Name  Age
0     Jai   27
1  Princi   24
2  Gaurav   22
3    Anuj   32
```

###### Row Selection
Pandas provide unique methods for selecting rows from a Data frame. [DataFrame.loc[]](https://www.geeksforgeeks.org/python/python-pandas-extracting-rows-using-loc/) method is used for label-based selection
```python
import pandas as pd

data = pd.read_csv("nba.csv",index_col="Name")

first = data.loc["Avery Bradley"]
second = data.loc["R.J. Hunter"]

print(first, "\n\n\n" , secoond)
```

```

Team        Boston Celtics
Number                 0.0
Position                PG
Age                   25.0
Height                 6-2
Weight               180.0
College              Texas
Salary           7730337.0
Name: Avery Bradley, dtype: object 


 Team        Boston Celtics
Number                28.0
Position                SG
Age                   22.0
Height                 6-5
Weight               185.0
College      Georgia State
Salary           1148640.0
Name: R.J. Hunter, dtype: object
```

important point to clarify:
when python makes a dataframe of some let's say NBA players , the indexes will be numbers by default like this 
![[Screenshot From 2025-12-11 15-28-03.png]]
but when you add this argument to set index
```python
data = pd.read_csv("nba.csv", index_col="Name")
# index_col="Name"
```
This will make the dataframe "Label based" which means your index from now on will be the column you defined "Names". 
So accessing row will be using Names and not number indexes
Example on output of label based dataframes:
![[Screenshot From 2025-12-11 15-31-30.png]]
and accessing rows will be:
```python
row = df["Avery"]
```



##### Indexing and selecting Data in DataFrame
###### Difference between `.loc` and `.iloc`

`.loc` : access by label (index name)
With index_col="Name":
```python
row = data.loc["Avery Bradley"]
```

With default numeric index:
row = data.loc[0]

`.iloc` access by position (always numbers)
Works with ANY index type:
row = data.iloc[0]  # First row
row = data.iloc[5]  # Sixth row

**Key difference:**
`.loc` uses the index value (can be text or number)
`.iloc` uses the position (always 0, 1, 2, 3...)

Example:
```python
data = pd.read_csv("nba.csv", index_col="Name")
data.loc["Avery Bradley"]  # Access by name ✓
data.iloc[0]               # Access first row ✓
```

When to use:
Use .loc when you know the index label
Use .iloc when you know the position number

###### Full example using some tasks 
```python
import pandas as pd

data = pd.read_csv("nba.csv", index_col="Name")

# 1. Print first 5 rows
print(data.head())

# 2. Access "Avery Bradley" row
print(data.loc["Avery Bradley"])

# 3. Access the 3rd row
print(data.iloc[2])

# 4. Select only 'Team' and 'Age' columns
print(data[["Team", "Age"]])

# 5. Filter players where Age > 25
print(data[data["Age"] > 25])

# 6. Count how many unique teams exist
print(data["Team"].nunique())

# 7. Get all salary values as array (no index)
print(data["Salary"].values)

# 8. Find players from "Celtics" team
print(data[data["Team"] == "Celtics"])

# 9. Sort DataFrame by 'Age' in descending order
print(data.sort_values("Age", ascending=False))

# 10. Check if there are any missing values
print(data.isnull().sum())

# 11. Get the mean of 'Age' column
print(data["Age"].mean())
```

##### Working with missing data
Missing Data can occur when no information is available for one or more items or for an entire row/column. In Pandas missing data is represented as NaN (Not a Number). Missing data can be problematic in real-world datasets where data is incomplete. Pandas provides several methods to handle such missing data effectively

**Checking for Missing Values using isnull() and notnull()**
To check for missing values (NaN) we can use two useful functions:
1. ****isnull()****: It returns True for NaN (missing) values and False otherwise.
2. ****notnull()****: It returns the opposite, True for non-missing values and False for NaN values

```python
import pandas as pd
import numpy as np
 
dict = {'First Score':[100, 90, np.nan, 95],
        'Second Score': [30, 45, 56, np.nan],
        'Third Score':[np.nan, 40, 80, 98]}

df = pd.DataFrame(dict)
 
print(df.isnull())
```
![[Screenshot From 2025-12-11 16-14-01.png]]


###### Filling Missing Data using  `fillna()`, `replace()`
In order to fill null values in a datasets, we use `fillna()`, `replace()` and `interpolate()` function these function replace NaN values with some value of their own. All these function help in filling a null values in datasets of a DataFrame. Interpolate() function is used to fill NA values in the dataframe but it uses various interpolation technique to fill the missing values rather than hard-coding the value.
```python
import pandas as pd
 
import numpy as np
 
dict = {'First Score':[100, 90, np.nan, 95],
        'Second Score': [30, 45, 56, np.nan],
        'Third Score':[np.nan, 40, 80, 98]}
df = pd.DataFrame(dict)
 
print(df.fillna(0)) # Set NaN to zeros
```
![[Screenshot From 2025-12-11 16-20-21.png]]

###### Dropping missing values `dropna()`
If we want to remove rows or columns with missing data we can use the `dropna()`method. This method is flexible which allows us to drop rows or columns depending on the configuration.
```python
import pandas as pd
 
import numpy as np
 
dict = {'First Score':[100, 90, np.nan, 95],
        'Second Score': [30, np.nan, 45, 56],
        'Third Score':[52, 40, 80, 98],
        'Fourth Score':[np.nan, np.nan, np.nan, 65]}
 
df = pd.DataFrame(dict)
   
print(df.dropna())
```
before dropping NaN
![[Screenshot From 2025-12-11 19-20-39.png]]
After dropping NaN
![[Screenshot From 2025-12-11 19-20-32.png]]

##### Iterating over rows and colmuns
Iteration refers to the process of accessing each item one at a time. In Pandas, it means iterating through rows or columns in a DataFrame to access or manipulate the data. We can iterate over rows and columns to extract values or perform operations on each item.

###### Iterating Over Rows
There are several ways to iterate over the rows of a Pandas DataFrame and three common methods are:
1. iteritems()
2. iterrows()
3. itertuples()


1-  `iteritems()` - iterate over columns (NOT rows)
```python
for column_name, column_data in data.iteritems():
    print(column_name)  # 'Name', 'Age', 'Team'...
    print(column_data)  # Series of that column
```

**2. `iterrows()` - iterate over rows (returns index + Series)**
```python
for index, row in data.iterrows():
    print(index)       # Row label
    print(row['Age'])  # Access column value
```

**3. `itertuples()` - iterate over rows (returns named tuple, faster)**
```python
for row in data.itertuples():
    print(row.Index)   # Row label
    print(row.Age)     # Access column value
```
**Key Difference:**
- `iteritems()` = loop through columns
- `iterrows()` = loop through rows (slower, returns Series)
- `itertuples()` = loop through rows (faster, returns tuple)

##### Pandas idioms
**What are idioms?** Efficient, pythonic ways to write pandas code that are faster and cleaner.

**1. Vectorization over loops**
**Bad (slow):**
```python
for i in range(len(data)):
    data.loc[i, 'New'] = data.loc[i, 'Age'] * 2
```

**Good (fast):**
```python
data['New'] = data['Age'] * 2
```

**2. Method chaining**
**Bad:**
```python
data = data.dropna()
data = data[data['Age'] > 25]
data = data.sort_values('Age')
```

**Good:**
```python
data = (data
    .dropna()
    .query('Age > 25')
    .sort_values('Age')
)
```

**3. Use `.query()` for readability**
**Bad:**
```python
result = data[(data['Age'] > 25) & (data['Team'] == 'Celtics')]
```

**Good:**
```python
result = data.query('Age > 25 and Team == "Celtics"')
```

**4. Use `.loc` and `.iloc` explicitly**
**Bad:**
```python
data[0]           # Ambiguous
data['Name'][0]   # Chained indexing (can cause issues)
```

**Good:**
```python
data.iloc[0]           # Clear: first row
data.loc[0, 'Name']    # Clear: specific cell
```

**5. Use `.assign()` for new columns**

**Bad:**
```python
data['Adult'] = data['Age'] >= 18
data['Double'] = data['Age'] * 2
```

**Good:**
```python
data = data.assign(
    Adult=lambda df: df['Age'] >= 18,
    Double=lambda df: df['Age'] * 2
)
```

##### Group by
**groupby():** Split data into groups based on column values.
```python
grouped = data.groupby('Team')  # Group by Team
print(grouped['Age'].mean())    # Average age per team
```

**np.average():** Calculate weighted or regular average.
```python
avg = np.average(data['Age'])  # Simple average
```

**set_index():** Set a column as the DataFrame index.
```python
data = data.set_index('Name')  # Name becomes index
```

**where():** Keep values that meet condition, replace others with NaN.
```python
result = data.where(data['Age'] > 25)  # Keep Age>25, rest become NaN
```

**Pivot Table:** Reshape data like Excel pivot table - summarize by grouping.
```python
pivot = data.pivot_table(
    values='Salary',     # What to calculate
    index='Team',        # Rows
    columns='Position',  # Columns
    aggfunc='mean'       # How to aggregate
)
```

