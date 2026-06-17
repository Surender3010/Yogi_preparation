# Medium Complexity Python Concepts for KV/NV Interview
## Kendriya Vidyalaya and Navodaya Vidyalaya Interview Preparation

This guide is prepared for KV/NV Computer Science interview readiness at a medium level. It assumes you already know basic syntax, variables, conditionals, loops, strings, lists, dictionaries, functions, file handling, and OOP basics. The focus here is on deeper understanding, explanation quality, classroom teaching points, and common interview-level coding logic.

---

## 1. Python Execution Model

Python is an interpreted language, but internally it first converts source code into bytecode. The Python Virtual Machine (PVM) executes this bytecode.

### Interview Answer
Python code is not directly converted into machine code like C or C++. The interpreter compiles it into bytecode, and the Python Virtual Machine executes that bytecode. This makes Python portable and easy to use, but generally slower than fully compiled languages.

### Teaching Point
For school students, explain it as: source code is written by the programmer, bytecode is an intermediate form, and the PVM runs the program.

---

## 2. Dynamic Typing and Strong Typing

Python is dynamically typed because the type of a variable is decided at runtime. It is strongly typed because Python does not silently mix incompatible types.

```python
x = 10
x = "Python"  # allowed because type is dynamic

print("Marks: " + str(95))  # explicit conversion required
```

### Interview Answer
Dynamic typing means a variable can refer to different types of values during execution. Strong typing means operations between incompatible types are not automatically allowed.

---

## 3. Mutable and Immutable Objects

Objects whose values can be changed are mutable. Objects whose values cannot be changed are immutable.

| Mutable | Immutable |
|---|---|
| `list` | `int` |
| `dict` | `float` |
| `set` | `str` |
| | `tuple` |

```python
marks = [80, 90]
marks.append(95)
print(marks)

name = "Amit"
name = name.upper()  # creates a new string
print(name)
```

### Common Viva Question
Why is string called immutable?

### Answer
Because any operation that appears to modify a string actually creates a new string object.

---

## 4. Variable References and Object Identity

In Python, variables are references to objects. Assignment does not always create a new object.

```python
a = [10, 20]
b = a
b.append(30)

print(a)  # [10, 20, 30]
print(a is b)  # True
```

### Interview Point
`==` checks value equality, while `is` checks whether both variables refer to the same object.

```python
x = [1, 2]
y = [1, 2]

print(x == y)  # True
print(x is y)  # False
```

---

## 5. Shallow Copy and Deep Copy

A shallow copy creates a new outer object but keeps references to nested objects. A deep copy recursively copies nested objects also.

```python
import copy

students = [["Amit", 85], ["Neha", 90]]
shallow = copy.copy(students)
deep = copy.deepcopy(students)

students[0][1] = 95

print(shallow)  # nested change visible
print(deep)     # nested change not visible
```

### Interview Answer
Shallow copy is enough for simple one-level collections. Deep copy is useful when a collection contains nested mutable objects.

---

## 6. List Comprehension

List comprehension is a concise way to create a list using an expression and loop.

```python
squares = [n * n for n in range(1, 6)]
print(squares)

even_numbers = [n for n in range(1, 21) if n % 2 == 0]
print(even_numbers)
```

### Normal Loop Equivalent

```python
even_numbers = []
for n in range(1, 21):
    if n % 2 == 0:
        even_numbers.append(n)
```

### Teaching Point
First teach normal loops, then introduce comprehension as a shorter form.

---

## 7. Dictionary Comprehension

Dictionary comprehension creates dictionaries in a compact way.

```python
numbers = [1, 2, 3, 4]
square_map = {n: n * n for n in numbers}
print(square_map)
```

### Practical School Example

```python
students = ["Amit", "Neha", "Ravi"]
attendance = {name: "Present" for name in students}
print(attendance)
```

---

## 8. Function Arguments in Python

Python supports several types of function arguments.

```python
def student_report(name, marks, section="A"):
    return f"{name} from {section} scored {marks}"

print(student_report("Amit", 88))
print(student_report(name="Neha", marks=91, section="B"))
```

### Types of Arguments
- Positional arguments
- Keyword arguments
- Default arguments
- Variable-length arguments using `*args` and `**kwargs`

```python
def total_marks(*marks):
    return sum(marks)

print(total_marks(78, 82, 91))
```

---

## 9. Common Mistake: Mutable Default Arguments

Using a mutable object like a list as a default argument can create unexpected results.

```python
def add_student(name, students=[]):
    students.append(name)
    return students

print(add_student("Amit"))
print(add_student("Neha"))  # previous value remains
```

### Correct Version

```python
def add_student(name, students=None):
    if students is None:
        students = []
    students.append(name)
    return students
```

### Interview Answer
Default argument values are created only once when the function is defined, not every time it is called.

---

## 10. Lambda Functions

Lambda functions are small anonymous functions used for short logic.

```python
square = lambda n: n * n
print(square(5))
```

### With Sorting

```python
students = [("Amit", 85), ("Neha", 92), ("Ravi", 78)]
students.sort(key=lambda item: item[1], reverse=True)
print(students)
```

### Interview Point
Lambda is useful for short functions, especially with `sort()`, `map()`, `filter()`, and similar operations.

---

## 11. `map()`, `filter()`, and `reduce()`

These functions support functional programming style.

```python
numbers = [1, 2, 3, 4, 5]

squares = list(map(lambda n: n * n, numbers))
evens = list(filter(lambda n: n % 2 == 0, numbers))

print(squares)
print(evens)
```

`reduce()` is available from `functools`.

```python
from functools import reduce

numbers = [1, 2, 3, 4]
product = reduce(lambda a, b: a * b, numbers)
print(product)
```

---

## 12. String Formatting

Python supports multiple formatting styles, but f-strings are most readable.

```python
name = "Amit"
marks = 88.456

print("Name: {}, Marks: {:.2f}".format(name, marks))
print(f"Name: {name}, Marks: {marks:.2f}")
```

### Interview Answer
F-strings are preferred because they are readable, concise, and support expressions inside braces.

---

## 13. Advanced String and List Slicing

Slicing can extract parts of sequences using `start:stop:step`.

```python
text = "COMPUTER"

print(text[1:5])
print(text[::-1])
print(text[::2])
```

### Common Coding Use

```python
word = "level"
if word == word[::-1]:
    print("Palindrome")
```

---

## 14. Sorting with Custom Logic

Python can sort data using custom keys.

```python
records = [
    {"name": "Amit", "marks": 85},
    {"name": "Neha", "marks": 92},
    {"name": "Ravi", "marks": 78},
]

records.sort(key=lambda student: student["marks"], reverse=True)
print(records)
```

### Interview Point
`sort()` changes the original list, while `sorted()` returns a new sorted list.

---

## 15. Nested Data Structures

Real programs often use nested lists and dictionaries.

```python
class_result = {
    "12A": [
        {"name": "Amit", "marks": 85},
        {"name": "Neha", "marks": 92},
    ],
    "12B": [
        {"name": "Ravi", "marks": 78},
    ],
}

for section, students in class_result.items():
    print("Section:", section)
    for student in students:
        print(student["name"], student["marks"])
```

### Teaching Point
Use real classroom examples like student records, attendance, and marks analysis.

---

## 16. Exception Handling with `else` and `finally`

`else` runs only if no exception occurs. `finally` runs whether an exception occurs or not.

```python
try:
    marks = int(input("Enter marks: "))
except ValueError:
    print("Please enter a valid number")
else:
    print("Marks entered:", marks)
finally:
    print("Input process completed")
```

### Interview Answer
Exception handling improves program reliability because runtime errors can be handled gracefully.

---

## 17. Raising Custom Exceptions

Programmers can raise exceptions when input or state is invalid.

```python
def validate_marks(marks):
    if marks < 0 or marks > 100:
        raise ValueError("Marks must be between 0 and 100")
    return marks

try:
    validate_marks(105)
except ValueError as error:
    print(error)
```

### Classroom Use
This is useful for teaching validation in practical programs.

---

## 18. File Handling with CSV Data

CSV files are useful for storing tabular data such as student marks.

```python
import csv

with open("students.csv", "w", newline="") as file:
    writer = csv.writer(file)
    writer.writerow(["Name", "Marks"])
    writer.writerow(["Amit", 85])
    writer.writerow(["Neha", 92])

with open("students.csv", "r") as file:
    reader = csv.reader(file)
    for row in reader:
        print(row)
```

### Interview Point
Use `with open()` because it automatically closes the file after use.

---

## 19. JSON Handling

JSON is commonly used for structured data exchange.

```python
import json

student = {"name": "Amit", "marks": 85, "section": "12A"}

json_text = json.dumps(student)
print(json_text)

data = json.loads(json_text)
print(data["name"])
```

### Interview Answer
JSON represents data in key-value format and is widely used in APIs, configuration files, and data exchange.

---

## 20. Object-Oriented Programming: Medium Level

OOP helps organize data and behavior together using classes and objects.

```python
class Student:
    school_name = "KV School"

    def __init__(self, name, marks):
        self.name = name
        self.marks = marks

    def grade(self):
        if self.marks >= 90:
            return "A"
        if self.marks >= 75:
            return "B"
        return "C"

student = Student("Amit", 88)
print(student.name, student.grade())
```

### Interview Point
Instance variables belong to individual objects. Class variables are shared by all objects of the class.

---

## 21. Inheritance and Method Overriding

Inheritance allows one class to reuse and extend another class.

```python
class Person:
    def __init__(self, name):
        self.name = name

    def role(self):
        return "Person"

class Teacher(Person):
    def role(self):
        return "Teacher"

teacher = Teacher("Yogender")
print(teacher.name, teacher.role())
```

### Interview Answer
Method overriding means a child class provides its own implementation of a method already defined in the parent class.

---

## 22. Encapsulation with Properties

Encapsulation protects data by controlling access through methods or properties.

```python
class Student:
    def __init__(self, marks):
        self._marks = marks

    @property
    def marks(self):
        return self._marks

    @marks.setter
    def marks(self, value):
        if value < 0 or value > 100:
            raise ValueError("Invalid marks")
        self._marks = value

student = Student(80)
student.marks = 95
print(student.marks)
```

### Interview Point
Python does not enforce strict private variables, but a single underscore indicates protected/internal use by convention.

---

## 23. Modules, Packages, and `__name__`

A module is a Python file. A package is a folder containing modules. The `__name__` variable helps separate reusable code from direct execution code.

```python
def main():
    print("Program started")

if __name__ == "__main__":
    main()
```

### Interview Answer
This condition ensures that `main()` runs only when the file is executed directly, not when it is imported as a module.

---

## 24. Iterators and Generators

An iterator produces values one at a time. A generator is a simple way to create an iterator using `yield`.

```python
def first_n_numbers(n):
    for number in range(1, n + 1):
        yield number

for value in first_n_numbers(5):
    print(value)
```

### Interview Answer
Generators are memory efficient because they produce values one by one instead of storing all values at once.

---

## 25. Time Complexity Awareness

At medium level, you should be able to discuss basic time complexity.

| Operation | General Complexity |
|---|---|
| Access list item by index | `O(1)` |
| Search item in list | `O(n)` |
| Dictionary key lookup | Average `O(1)` |
| Nested loop over n items | `O(n^2)` |

```python
numbers = [10, 20, 30, 40]
print(30 in numbers)  # O(n)

marks = {"Amit": 85, "Neha": 92}
print(marks["Neha"])  # average O(1)
```

### Interview Point
For school-level interviews, focus on explaining efficiency in simple terms: how program time grows as input size grows.

---

## 26. Medium-Level Programs Often Asked

### Program 1: Frequency of each character

```python
text = "programming"
frequency = {}

for char in text:
    frequency[char] = frequency.get(char, 0) + 1

print(frequency)
```

### Program 2: Find second largest number

```python
numbers = [10, 45, 23, 45, 67, 67]
unique_numbers = sorted(set(numbers), reverse=True)

if len(unique_numbers) >= 2:
    print(unique_numbers[1])
else:
    print("Second largest does not exist")
```

### Program 3: Merge two dictionaries

```python
marks1 = {"Amit": 85, "Neha": 90}
marks2 = {"Ravi": 78, "Amit": 88}

merged = {**marks1, **marks2}
print(merged)
```

### Program 4: Count words in a sentence

```python
sentence = "Python is easy and Python is powerful"
words = sentence.lower().split()
word_count = {}

for word in words:
    word_count[word] = word_count.get(word, 0) + 1

print(word_count)
```

### Program 5: Remove duplicates while preserving order

```python
numbers = [4, 2, 4, 5, 2, 8]
result = []

for number in numbers:
    if number not in result:
        result.append(number)

print(result)
```

### Program 6: Read CSV marks and calculate average

```python
import csv

total = 0
count = 0

with open("students.csv", "r") as file:
    reader = csv.DictReader(file)
    for row in reader:
        total += int(row["Marks"])
        count += 1

if count > 0:
    print("Average:", total / count)
```

---

## 27. High-Probability Difference Questions

### `sort()` vs `sorted()`

| `sort()` | `sorted()` |
|---|---|
| List method | Built-in function |
| Changes original list | Returns new sorted list |
| Returns `None` | Returns sorted collection |

### Shallow Copy vs Deep Copy

| Shallow Copy | Deep Copy |
|---|---|
| Copies only outer object | Copies nested objects also |
| Faster | Slower but safer for nested data |
| Uses `copy.copy()` | Uses `copy.deepcopy()` |

### List Comprehension vs Generator Expression

| List Comprehension | Generator Expression |
|---|---|
| Creates full list in memory | Produces values one by one |
| Uses `[]` | Uses `()` |
| Good for small/medium data | Good for large data |

### Class Variable vs Instance Variable

| Class Variable | Instance Variable |
|---|---|
| Shared by all objects | Separate for each object |
| Defined inside class but outside methods | Usually defined using `self` |

---

## 28. KV/NV Interview-Oriented Viva Questions

### 1. How is Python suitable for teaching computational thinking?
Python has readable syntax, so students can focus on logic, decomposition, pattern recognition, and step-wise problem solving instead of syntax complexity.

### 2. How will you explain mutable and immutable objects to students?
I will use list and string examples. A list can be changed directly using methods like `append()`, but a string operation creates a new string because strings are immutable.

### 3. Why should students learn file handling?
File handling connects programming with real data storage. It helps students build practical programs like marks records, attendance files, and result reports.

### 4. How will you teach exception handling in a classroom?
I will first show a program that fails for invalid input, then introduce `try-except` to handle the error gracefully. This helps students understand reliability.

### 5. What is the importance of functions in large programs?
Functions divide a program into smaller reusable parts. They improve readability, testing, debugging, and teamwork.

### 6. How will you explain OOP using school examples?
I can use `Student`, `Teacher`, and `Classroom` as examples. Attributes store data, and methods describe behavior, such as calculating grade or displaying details.

### 7. What is the role of dictionaries in Python programs?
Dictionaries store data in key-value form. They are very useful for structured records, fast lookup, and real-life data representation.

### 8. How do you check whether a student understands code rather than copying it?
I ask the student to trace the output, explain each line, change one condition, or write the same logic for a slightly different problem.

### 9. How will you teach debugging to beginners?
I will teach students to read error messages, use print-based tracing, test small parts separately, and check input-output step by step.

### 10. Why is time complexity useful at school level?
It helps students understand that different solutions may produce the same result but not with the same efficiency. It builds better problem-solving habits.

---

## 29. Additional Medium-Level Viva Questions

### 1. What is the difference between dynamic typing and static typing?
In dynamic typing, the type of a variable is decided at runtime. In static typing, the type is declared or checked before execution. Python uses dynamic typing.

### 2. Why is Python called strongly typed?
Python is strongly typed because it does not automatically combine incompatible data types. For example, adding a string and an integer directly gives an error.

### 3. What is the difference between `==` and `is`?
`==` compares values, while `is` compares object identity. Two lists may have the same values but still be different objects in memory.

### 4. Why should we avoid mutable default arguments?
Mutable default arguments are created only once when the function is defined. If the same list or dictionary is modified in one call, the change may appear in later calls also.

### 5. What is the use of `None` in Python?
`None` represents the absence of a value. It is often used as a default value, placeholder, or return value when a function has nothing meaningful to return.

### 6. What is short-circuit evaluation?
In logical expressions, Python may stop evaluation early when the final result is already known. For example, in `A and B`, if `A` is false, Python does not evaluate `B`.

### 7. What is the difference between `break`, `continue`, and `pass`?
`break` exits the loop, `continue` skips the current iteration, and `pass` does nothing. `pass` is used as a placeholder where syntax requires a statement.

### 8. What is the purpose of `enumerate()`?
`enumerate()` gives both index and value while looping through a sequence.

```python
names = ["Amit", "Neha", "Ravi"]

for index, name in enumerate(names, start=1):
    print(index, name)
```

### 9. What is the purpose of `zip()`?
`zip()` combines two or more iterables element by element.

```python
names = ["Amit", "Neha"]
marks = [85, 92]

for name, mark in zip(names, marks):
    print(name, mark)
```

### 10. What is unpacking in Python?
Unpacking means assigning values from a sequence to multiple variables.

```python
student = ("Amit", 85, "12A")
name, marks, section = student
```

### 11. What is the difference between a module and a package?
A module is a single Python file. A package is a folder that contains multiple modules and usually an `__init__.py` file.

### 12. What is the role of `pip`?
`pip` is Python's package installer. It is used to install external libraries that are not part of the standard library.

### 13. What is the difference between an error and an exception?
An error is a general problem in a program. An exception is a runtime problem that can be handled using `try-except`.

### 14. Why is `with open()` preferred in file handling?
It automatically closes the file after the block finishes, even if an exception occurs. This makes file handling cleaner and safer.

### 15. What is recursion?
Recursion is a technique where a function calls itself. It must have a base case to stop repeated calls.

---

## 30. Output Prediction Questions

### Question 1

```python
x = [1, 2, 3]
y = x
y.append(4)
print(x)
```

### Answer

```text
[1, 2, 3, 4]
```

Both `x` and `y` refer to the same list object.

### Question 2

```python
x = "Python"
print(x[1:4])
```

### Answer

```text
yth
```

Slicing starts at index `1` and stops before index `4`.

### Question 3

```python
def show(value=[]):
    value.append(10)
    return value

print(show())
print(show())
```

### Answer

```text
[10]
[10, 10]
```

The same default list is reused across function calls.

### Question 4

```python
numbers = [1, 2, 3, 4]
result = [n * 2 for n in numbers if n % 2 == 0]
print(result)
```

### Answer

```text
[4, 8]
```

Only even numbers are selected, then multiplied by `2`.

### Question 5

```python
data = {"Amit": 85, "Neha": 92}
print(data.get("Ravi", 0))
```

### Answer

```text
0
```

`get()` returns the default value because the key does not exist.

### Question 6

```python
for i in range(3):
    if i == 1:
        continue
    print(i)
```

### Answer

```text
0
2
```

When `i` is `1`, `continue` skips the print statement.

---

## 31. Coding Practice Questions

### 1. Write a program to find all prime numbers between 1 and 100.
Use nested loops or a helper function to test whether each number is prime.

### 2. Write a program to count uppercase, lowercase, digits, and special characters in a string.
Use string methods such as `isupper()`, `islower()`, and `isdigit()`.

### 3. Write a program to sort student records by marks.
Store records as a list of dictionaries and use `sort()` or `sorted()` with a key function.

### 4. Write a program to read marks from a CSV file and print students who scored above 80.
Use `csv.DictReader()` and compare marks after converting them to integers.

### 5. Write a program to check whether two strings are anagrams.
Two strings are anagrams if they contain the same characters in any order.

### 6. Write a function to remove duplicate words from a sentence while preserving order.
Use a list to maintain order and a set to check whether a word has already appeared.

### 7. Write a menu-based program for student records.
Include options to add, search, update, delete, and display student records.

### 8. Write a recursive function to calculate the sum of first `n` natural numbers.
Clearly define the base case and recursive case.

### 9. Write a program to merge two dictionaries and handle duplicate keys.
If the same student appears in both dictionaries, decide whether to keep the latest marks or calculate an average.

### 10. Write a program to find the most frequent element in a list.
Use a dictionary to count occurrences.

---

## 32. Teaching Scenario Questions

### 1. A student understands loops but struggles with nested loops. How will you teach it?
I will start with a simple table or pattern example and trace each iteration slowly. I will explain the outer loop as row control and the inner loop as column control.

### 2. A student gets confused between list and dictionary. How will you explain the difference?
I will say that a list is like a numbered register, while a dictionary is like a record with named fields. Lists use indexes, and dictionaries use keys.

### 3. How will you teach functions to weak students?
I will begin with daily-life examples like a calculator or attendance task. Then I will show input, processing, and output using simple functions.

### 4. How will you teach debugging during practical lab?
I will ask students to read the error message, identify the line number, check the variable values, and test the program with small inputs.

### 5. How will you teach file handling practically?
I will connect it with school examples such as storing marks, attendance, or student names in a file and reading them back.

### 6. How will you handle students who memorize programs without understanding?
I will ask them to modify the program, predict output, explain each line, and solve a similar problem with changed input conditions.

### 7. How will you introduce OOP to Class 12 students?
I will use real-life examples such as `Student`, `Teacher`, and `LibraryBook`. Each object has data and behavior, which helps students understand attributes and methods.

### 8. How will you make Python practical classes more engaging?
I will use small projects, pair programming, output prediction games, error-finding tasks, and real-life school data examples.

---

## 33. One-Minute Medium-Level Python Revision

Python is dynamically typed, strongly typed, interpreted through bytecode and the PVM, and supports multiple programming styles. At medium level, important concepts include mutability, object identity, shallow and deep copy, comprehensions, function argument behavior, lambda functions, file handling with CSV and JSON, exception handling, OOP with inheritance and encapsulation, modules, generators, and basic time complexity. For KV/NV interviews, connect these concepts with classroom teaching, practical examples, and student-friendly explanation.

---

## 34. Interview Delivery Tips for Medium-Level Python

- Give a direct definition first.
- Add one small code example.
- Explain the output clearly.
- Mention classroom use where relevant.
- Use terms like mutable, immutable, reference, object, module, package, exception, and complexity correctly.
- Avoid over-explaining advanced internal details unless asked.

---

## 35. Final Medium-Level Practice Plan

- Revise 5 medium concepts daily.
- Write 3 programs using dictionaries, files, or functions.
- Practice 3 difference questions aloud.
- Trace 2 code snippets manually.
- Explain 1 concept as if teaching Class 11 or 12 students.
- Solve 1 list or string problem with time complexity awareness.

This routine will help you move beyond basic definitions and answer KV/NV Python interview questions with stronger technical confidence.

---

## 36. Additional Question Bank: 60 More Python Questions

### Conceptual Questions

### 1. What is the difference between source code, bytecode, and machine code?
Source code is the program written by the programmer. Bytecode is an intermediate form created by Python before execution. Machine code is low-level binary instructions understood directly by the processor.

### 2. Why is Python slower than C or C++ in many cases?
Python is generally slower because it is dynamically typed and executed through an interpreter or virtual machine. C and C++ are compiled closer to machine code, so they usually execute faster.

### 3. What is the role of indentation in Python program structure?
Indentation defines code blocks in Python. It shows which statements belong to loops, functions, classes, and conditional blocks.

### 4. What is the difference between syntax error and logical error?
A syntax error occurs when the program violates Python grammar rules. A logical error occurs when the program runs but gives an incorrect result because the logic is wrong.

### 5. What is the difference between runtime error and compile-time error?
A runtime error occurs while the program is running, such as division by zero. A compile-time error is detected before execution, such as invalid syntax.

### 6. Why does Python use garbage collection?
Python uses garbage collection to automatically remove objects that are no longer referenced. This helps manage memory without requiring the programmer to manually free memory.

### 7. What is memory management in Python?
Memory management is the process of allocating memory for objects and releasing memory when objects are no longer needed. Python handles most memory management automatically through reference counting and garbage collection.

### 8. What is the difference between local variable and global variable?
A local variable is declared inside a function and is accessible only within that function. A global variable is declared outside functions and can be accessed throughout the program.

### 9. What is the difference between `global` and `nonlocal` keywords?
`global` is used to modify a global variable inside a function. `nonlocal` is used inside a nested function to modify a variable from the enclosing function scope.

### 10. What is namespace in Python?
A namespace is a mapping between names and objects. It helps Python identify whether a name belongs to local, global, built-in, or enclosing scope.

### 11. What is the LEGB rule in Python name resolution?
LEGB stands for Local, Enclosing, Global, and Built-in. Python searches for a variable name in this order.

### 12. What is the difference between expression and statement?
An expression produces a value, such as `5 + 3`. A statement performs an action, such as assignment, loop, or function definition.

### 13. What is the difference between parameter and argument?
A parameter is the variable name written in a function definition. An argument is the actual value passed during a function call.

### 14. What is the difference between return value and print output?
`return` sends a value back to the caller and can be reused in further calculations. `print()` only displays output on the screen.

### 15. Why should functions generally return values instead of only printing results?
Returning values makes functions reusable, testable, and useful in larger programs. Printed output is only for display and cannot easily be used by other code.

### Data Structure Questions

### 16. When should you use a list instead of a tuple?
Use a list when the collection needs to change, such as adding, removing, or updating items.

### 17. When should you use a tuple instead of a list?
Use a tuple when the data should remain fixed, such as coordinates, dates, or records that should not be modified.

### 18. Why are dictionary keys required to be hashable?
Dictionary keys must be hashable so Python can quickly locate values using a hash table. Immutable types like strings, numbers, and tuples can usually be used as keys.

### 19. Why can a list not be used as a dictionary key?
A list is mutable, so its contents can change. If a key changes, Python cannot reliably locate it in the dictionary.

### 20. What is the difference between `dict.keys()`, `dict.values()`, and `dict.items()`?
`dict.keys()` returns the keys, `dict.values()` returns the values, and `dict.items()` returns key-value pairs.

### 21. How do you safely access a dictionary key that may not exist?
Use the `get()` method because it returns `None` or a default value instead of raising `KeyError`.

```python
student = {"name": "Amit", "marks": 85}
print(student.get("section", "Not available"))
```

### 22. What is the difference between set union and set intersection?
Union gives all unique elements from both sets. Intersection gives only the elements common to both sets.

### 23. How can sets be used to remove duplicates from a list?
Convert the list into a set. Since sets store only unique values, duplicates are removed.

```python
numbers = [1, 2, 2, 3]
unique = set(numbers)
print(unique)
```

### 24. What is the limitation of using sets for duplicate removal?
A set does not preserve the original order in all situations. If order matters, use another method such as a loop with a separate result list.

### 25. What is the difference between `append()`, `insert()`, and `extend()`?
`append()` adds one item at the end. `insert()` adds one item at a specific position. `extend()` adds multiple items from another iterable.

### 26. What is the difference between `remove()`, `pop()`, and `del`?
`remove()` deletes by value. `pop()` deletes by index and returns the removed item. `del` deletes an item, slice, or entire variable.

### 27. What is the difference between shallow equality and object identity?
Equality checks whether values are the same using `==`. Object identity checks whether two variables refer to the same object using `is`.

### 28. What happens when a list is passed to a function and modified inside it?
The original list can change because lists are mutable and the function receives a reference to the same list object.

### 29. What is nested dictionary, and where can it be used in school data processing?
A nested dictionary is a dictionary inside another dictionary. It can store student records with roll numbers as keys and details such as name, marks, and section as inner dictionaries.

### 30. What is the difference between string indexing and string slicing?
Indexing accesses one character using a position. Slicing extracts a part of the string using `start:stop:step`.

### Function and OOP Questions

### 31. What is the use of `*args` in a function?
`*args` allows a function to accept any number of positional arguments as a tuple.

```python
def total(*marks):
    return sum(marks)
```

### 32. What is the use of `**kwargs` in a function?
`**kwargs` allows a function to accept any number of keyword arguments as a dictionary.

```python
def show(**details):
    print(details)
```

### 33. Can a function return multiple values in Python?
Yes. A function can return multiple values separated by commas. Python returns them as a tuple.

```python
def student():
    return "Amit", 85
```

### 34. What is function annotation in Python?
Function annotation adds optional type information to parameters and return values. It improves readability but does not enforce types by itself.

```python
def add(a: int, b: int) -> int:
    return a + b
```

### 35. What is a docstring, and why is it useful?
A docstring is a string written inside a function, class, or module to describe its purpose. It helps with documentation and code understanding.

### 36. What is the difference between procedural programming and object-oriented programming?
Procedural programming organizes code around functions and steps. Object-oriented programming organizes code around classes and objects that combine data and behavior.

### 37. What is constructor in Python?
A constructor is a special method used to initialize an object. In Python, the constructor method is `__init__()`.

### 38. Why is `self` used in instance methods?
`self` refers to the current object. It is used to access instance variables and methods inside a class.

### 39. What is the difference between instance method, class method, and static method?
An instance method works with object data and uses `self`. A class method works with class data and uses `cls`. A static method does not automatically receive object or class data.

### 40. What is method overriding?
Method overriding occurs when a child class provides its own version of a method already defined in the parent class.

### 41. What is method overloading, and does Python support it directly?
Method overloading means defining multiple methods with the same name but different parameters. Python does not support it directly like Java or C++; later definitions replace earlier ones. Similar behavior can be achieved using default arguments or `*args`.

### 42. What is multiple inheritance?
Multiple inheritance means a class inherits from more than one parent class.

```python
class C(A, B):
    pass
```

### 43. What is the Method Resolution Order (MRO)?
MRO is the order in which Python searches classes for a method or attribute, especially in inheritance. It can be checked using `ClassName.mro()`.

### 44. What is encapsulation in Python?
Encapsulation means keeping data and related methods together inside a class and controlling access to internal data.

### 45. What is abstraction, and how can it be explained to school students?
Abstraction means showing only essential details and hiding internal complexity. For students, it can be explained using a TV remote: users press buttons without knowing the internal circuit working.

### File, Exception, and Module Questions

### 46. What is the difference between text file and binary file?
A text file stores human-readable characters. A binary file stores data in binary form, such as images, audio, video, or compiled files.

### 47. What is the difference between read mode, write mode, and append mode?
Read mode `r` reads existing content. Write mode `w` writes new content and overwrites old content. Append mode `a` adds content at the end of the file.

### 48. What happens if a file is opened in write mode and the file already has content?
The existing content is erased and replaced with the new content written by the program.

### 49. Why is `newline=""` commonly used while writing CSV files?
It prevents extra blank lines from appearing in CSV files, especially on Windows systems.

### 50. What is the difference between `json.dumps()` and `json.loads()`?
`json.dumps()` converts a Python object into a JSON string. `json.loads()` converts a JSON string back into a Python object.

### 51. What is the difference between `json.dump()` and `json.load()`?
`json.dump()` writes a Python object to a JSON file. `json.load()` reads JSON data from a file and converts it into a Python object.

### 52. Why should exceptions be specific instead of using only a broad `except` block?
Specific exceptions make errors easier to understand and debug. A broad `except` block may hide unexpected problems.

### 53. What is the purpose of the `finally` block?
The `finally` block runs whether an exception occurs or not. It is commonly used for cleanup tasks such as closing files or releasing resources.

### 54. What is the purpose of raising a custom exception?
Raising a custom exception allows the programmer to signal invalid conditions clearly, such as invalid marks, negative age, or unavailable records.

### 55. What is the difference between importing a module and importing a specific function?
Importing a module brings the whole module namespace, so functions are accessed with the module name. Importing a specific function allows direct use of that function.

```python
import math
print(math.sqrt(25))

from math import sqrt
print(sqrt(25))
```

### Coding and Teaching Questions

### 56. Write a program to find the longest word in a sentence.

```python
sentence = "Python programming improves logical thinking"
words = sentence.split()
longest = max(words, key=len)

print(longest)
```

### 57. Write a program to count the number of lines, words, and characters in a text file.

```python
line_count = 0
word_count = 0
character_count = 0

with open("sample.txt", "r") as file:
    for line in file:
        line_count += 1
        word_count += len(line.split())
        character_count += len(line)

print("Lines:", line_count)
print("Words:", word_count)
print("Characters:", character_count)
```

### 58. Write a program to find common elements between two lists.

```python
list1 = [1, 2, 3, 4]
list2 = [3, 4, 5, 6]

common = list(set(list1) & set(list2))
print(common)
```

### 59. Write a program to group students by grade using a dictionary.

```python
students = [
    {"name": "Amit", "grade": "A"},
    {"name": "Neha", "grade": "A"},
    {"name": "Ravi", "grade": "B"},
]

grade_group = {}

for student in students:
    grade = student["grade"]
    grade_group.setdefault(grade, []).append(student["name"])

print(grade_group)
```

### 60. How will you explain time complexity using a classroom attendance example?
If a teacher checks attendance by calling each student's name one by one, the time increases as the number of students increases. For 30 students, 30 checks may be needed; for 60 students, 60 checks may be needed. This is similar to `O(n)` time complexity, where work grows with input size.
