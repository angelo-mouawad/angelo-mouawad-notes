# What Is Python ?

Python is a general purpose programming language. Programming itself is just writing instructions that a computer can understand and execute, basically a step by step algorithm. The goals are always the same, solve a problem, automate a task, or create an interactive system.

In order to run Python you will need to install it from the [python](https://www.python.org/downloads) website. The python package manager is `pip`.

A shell is a piece of software that lets us communicate with the operating system using commands. On Windows, the desktop, the taskbar and the file explorer are all part of the Windows shell.

You can develop Python projects straight in the shell, but it would be an awkward experience. Instead, Python code is written in a file called a script, and then you tell Python to execute all the commands written in that script.

Running a script.
```bash
py my-script.py
```

Testing a script.
```bash
pytest test.py
```

If you have many errors and you want the testing to stop at the first one, use this instead.
```bash
pytest -x test.py
```

---

## Python Virtual Environment

A Python virtual environment `venv` is an isolated workspace that lets a project use its own dependencies without affecting other projects or the global Python installation. It helps keep projects clean, reproducible, and free from version conflicts.

To create a virtual environment in VS Code.
- Step 1: Open your project folder in VS Code.
- Step 2: Press `Ctrl + Shift + P`.
- Step 3: Type `Python: Create Environment`
- Step 4: Select `Venv` and choose your Python version.

VS Code will:
- Create the folder `venv/.`
- Activate it.
- Set it as the interpreter automatically.

Or you can also manually do it in your terminal.
```powershell
py -m venv .venv
.venv\Scripts\Activate.ps1
```

---

## Functions

A function is a block of instructions that you give a name to, so you can run it again later without rewriting it. A function name can not start with a digit.

Syntax.
```python
def function_name():
    instruction1
    instruction2
    instruction3
```

Example.
```python
def zero():
    return 0
```

Once the function is created you can call it anywhere by its name.
```python
zero()
```

---

## Parameters

Parameters are the inputs of a function. The function takes its inputs in the form of parameters, and usually hands something back with a return statement.

Syntax.
```python
def function_name(parameter1, parameter2):
    instruction1
    instruction2
    return return_value
```

Example.
```python
def add(a, b):
    return a + b
```

---

## Arithmetic Operators

These are the operators you will use on numbers.
- Addition `+`
- Subtraction `-`
- Multiplication `*`
- Division `/` always returns a float
- Integer division `//` rounds down and always returns an int
- Exponent `**`
- Modulo `%` gives the remainder

Examples.
```python
7 / 2       # 3.5
7 // 2      # 3
7 % 2       # 1
2 ** 3      # 8
```

---

## Number Representations

Python has two number types you will use all the time.
- `int` for whole numbers.
- `float` for decimal numbers and whole numbers written like `5.0`. Floats are less efficient to work with.

The `type()` function returns the type of whatever you put between the parentheses.
```python
type(5)     # int
type(5.0)   # float
```

---

## Rounding A Float

Rounding down and rounding up are not built in, they have to be imported from the math module. Rounding to the nearest value is built in.
```python
from math import floor, ceil

floor(4.7)      # 4, rounds down
ceil(4.2)       # 5, rounds up
round(4.567)    # 5, rounds to the nearest whole number
round(4.567, 2) # 4.57, rounds to two decimal places
```

You can also write `math.floor()` if you imported the whole module with `import math`.

Floor is actually the same thing as integer division. The result of `//` is always rounded down and always ends up as an int.
```python
floor(7 / 2)    # 3
7 // 2          # 3
```

---

## Min And Max

Unlike floor and ceil, min and max are built in so you do not need to import anything. They can take any number of parameters, which makes them variadic functions.
```python
min(3, 8, 1)        # 1
max(3, 8, 1, 12)    # 12
```

---

## Local Variables And Scope

Variables that are created inside a function are local variables. They can only be used inside that function. The region of code in which a variable `x` is visible is called the scope of `x`.
```python
def total():
    result = 5   # local, only exists inside total()
    return result

print(result)    # error, result does not exist out here
```

Two small things worth remembering here.
- Raising an error yourself is done with `raise ValueError()`.
- Checking the type of a value is done with `isinstance(value, int)`.

---

## Booleans

`True` and `False` are values called booleans. They behave the same way as integers, so you can store them in variables, pass them as parameters and return them from functions.
- `True` is 1.
- `False` is 0.

Python actually allows you to use number operators like addition and subtraction on boolean values. Never do that, it makes the code confusing for no reason.

---

## Boolean Operators

Numbers have number operators like addition and subtraction. Booleans have their own operators, which are `and`, `or` and `not`.

The three truth tables.
```
and                     or                      not
False and False = False False or False = False  not False = True
False and True  = False False or True  = True   not True  = False
True  and False = False True  or False = True
True  and True  = True  True  or True  = True
```

There is one thing that trips everyone up at the start.
- `a = b` takes the value of b and assigns it to a.
- `a == b` compares the values of a and b and leaves both variables unchanged.

---

## If Condition

An if condition lets you skip a block of instructions when the condition is not met.
```python
instruction A
if condition:
    instruction B
    instruction C
instruction D
```

![If condition flow](if-condition-flow.svg)

Keep in mind that a return statement cuts the function short. Everything written after the return statement will not be read or processed.

---

## Else Condition

The else block runs when the condition is False. You can read `else:` as if it said `if not condition:`.
```python
instruction A
if condition:
    instruction B
else:
    instruction C
instruction D
```

![If else flow](if-else-flow.svg)

---

## Elif Condition

An elif is just an else with another if nested inside it. When using elif, everything after the first True condition is ignored.
```python
instruction A
if condition1:
    instruction B1
elif condition2:
    instruction B2
elif condition3:
    instruction B3
else:
    instruction C
```

![Elif flow](elif-flow.svg)

So the choice between elif and a stack of ifs comes down to this.
- If you want only the first True condition to return a value and the rest to be ignored, use elif.
- If you want every True condition to return a value, use separate if conditions.

---

## Boolean Exam Type Questions

These look like trick questions but they all come from the rules above. Work them out from the inside towards the outside.
```python
2 / 2 == True           # True, because 1.0 == True

4.0 == 2 * 2 == True    # False, this is 4.0 == 4 and 4 == True at the same time
(4.0 == 2 * 2) == True  # True, the comparison happens first and gives True

type(4.0) == type(4)    # False, float is not int
4.0 == 4                # True, the values are equal even if the types are not
```

---

## None

`None` represents a missing value, so no value at all. It is different from `0`, because `0` is a real value. You can use it as the value of a parameter when that parameter has nothing to hold yet.

If a function has no return statement it returns `None`. This happens because functions in Python always have to return something.
```python
def greet():
    print("hello")

x = greet()   # x is None
```

---

## Strings

In Python, strings are delimited by double quotes or single quotes. A string is any form of text.
```python
name = "Angelo"
city = 'Leuven'
```

---

## Constructing Strings

Building a string out of variables is called string interpolation. The string needs to have the prefix `f`, then you drop your variables between curly brackets.
```python
name = "Angelo"
age = 18
message = f'I am {name}, and I am {age}'
```

---

## Formatting Options

Inside the curly brackets you can add formatting rules after a colon.

Date and time formatting, `:02` fills the number up to two digits.
```python
h = 1
m = 2
s = 3
f'{h:02}:{m:02}:{s:02}'   # "01:02:03"
```

Float formatting, used to show a specific number of decimals.
```python
pi = 3.141592
f'{pi:.2f}'               # "3.14"
```

Align text formatting, the number is the total width the text is padded to.
```python
text = 'abc'
f'{text:<10}|{text:>10}|{text:^10}'
# "abc       |       abc|   abc    "
```

---

## Print And Input

Print outputs data to the terminal. Input receives data from the user.
```python
print("Hello")
name = input("Enter your name: ")
```

The input of the user is always returned as a string, even when they type a number. That is why converting comes right after this.

---

## Converting Strings

These four functions convert a value from one type to another.
```python
int(x)     # converts to an integer
float(x)   # converts to a float
str(x)     # converts to a string
bool(x)    # converts to True or False
```

`bool()` on a string behaves in a way that surprises people at first.
```python
bool('True')    # True
bool('False')   # True
bool('')        # False
```

The rule is simple once you see it.
- A non empty string always returns True.
- An empty string always returns False.

---

## String Operators

Just like integers, strings have operators of all kinds.
```python
"ab" + "cd"     # "abcd", concatenation
"ab" * 3        # "ababab", repetition
"a" in "abc"    # True, membership, there is also not in
"a" == "a"      # True, equality
"a" != "a"      # False, inequality
len("abc")      # 3, length
"a" < "b"       # True, lexicographic comparison
```

---

## Backslash

The backslash is used to write characters inside a string without them being read as part of the code.
- Adding `"` is written as `\"`
- Adding `'` is written as `\'`
- New line is `\n`
- Tab is `\t`
- Backspace is `\b`
- Writing an actual backslash is `\\`

Using the `r` prefix before a string turns it into a raw string, which ignores backslashes completely.
```python
print(r"C:\new\table")   # C:\new\table
```

---

## Indexing And Slicing

Indexing is accessing a specific character in the string. The characters have indexes starting from 0. Negative indexing starts counting from the end, starting at 1.

Slicing is selecting a group of characters using `string[start_index:end_index:step]`. The start index is included, the end index is excluded, and the step lets you skip characters.

![String indexing and slicing](string-indexing.svg)

```python
s = "abcde"
s[0]        # "a"
s[-1]       # "e"
s[1:4]      # "bcd"
s[:3]       # "abc", leaving the start empty means from the start
s[2:]       # "cde", leaving the end empty means till the end
s[::2]      # "ace", every second character
```

---

## Mask

Masking is converting a string of letters into a string of asterisks. It is not built in, you write it yourself, and it is a good little exercise on `len`.
```python
def mask(text):
    return "*" * len(text)

mask("masking")   # "*******"
```

---

## String Methods

A method has the syntax `a.method(b, c, d)`, so it is called on the string itself instead of taking it as a parameter.
- `s.lower()` returns a lowercase copy of s.
- `s.upper()` returns an uppercase copy of s.
- `s.find('sub')` returns the index of the first letter of the substring in the string. If the substring was not found, `-1` is returned.
- `s.startswith(prefix)` checks if the string starts with a prefix.
- `s.endswith(suffix)` checks if the string ends with a suffix.
- `s.strip()` returns a copy where the whitespace at both ends has been removed.
- `s.lstrip()` removes whitespace at the start.
- `s.rstrip()` removes whitespace at the end.
- `s.ljust(width, fill)` returns a left justified copy of size width, padded with fill.
- `s.rjust(width, fill)` does the same but right justified.
- `s.center(width, fill)` does the same but centered.

Worth remembering that strings are immutable, so none of these change the original string. They all return a copy.

---

## Algorithms Building Blocks

Every algorithm you will ever write is made out of three building blocks.
- Sequencing, steps being executed one after the other in order.
- Selection, specifying whether certain steps should be executed or skipped based on conditions using if.
- Iteration, repeating certain steps using loops.

![Algorithm building blocks](algorithm-blocks.svg)

---

## While Loops

A while loop keeps repeating its block for as long as the condition stays True.
```python
i = 5

while i != 0:
    print(i)
    i = i - 1

print("liftoff!")
```

This code returns.
```
5
4
3
2
1
liftoff!
```

Using return inside a loop interrupts the loop the same way it interrupts a function.

---

## For Loops

A for loop runs its block a set number of times using `range`.
```python
for i in range(0, 3):
    print(i)
```

This code returns.
```
0
1
2
```

Two things to remember about range.
- In `range(start, stop)` the stop value is always excluded.
- Writing `range(stop)` is the same as writing `range(0, stop)`.

You can also loop over a string directly, which gives you one character at a time.
```python
for char in "abcd":
    print(char)
```

This code returns.
```
a
b
c
d
```

---

## Nesting Loops

Nesting loops is not advised. It is better to just write separate functions. This will be more readable, easier to find mistakes in, and easier to write in the first place.

---

## Walrus Operator

The walrus operator is a slightly more advanced tool that helps you clean up your code by removing duplication. The difference is small but it matters.
- `a = b` assigns b to a as a statement on its own.
- `a := b` assigns b to a and gives back the value at the same time, so it can live inside a condition.

Without the walrus operator.
```python
n = len(name)
if n > 10:
    print(n)
```

With the walrus operator.
```python
if (n := len(name)) > 10:
    print(n)
```

---

## Tuples

A tuple is an object that stores integers, floats, strings, and even other tuples. Tuples are written using `()`.
```python
t = (1, 2, 3)
single = (1,)   # a tuple with one element needs the comma
```

Indexing works exactly like string indexing.
```python
len(t)      # length
t[0]        # first item
t[-1]       # last item
t[1:4]      # slicing
```

To determine whether a tuple contains an element, use the `in` operator. Watch out for nesting here, because `in` only looks at the top level.
```python
3 in (1, 2, 3, 4)           # True
3 in (1, (2, 3), 4)         # False
(2, 3) in (1, (2, 3), 4)    # True
(2, 3) in (1, 2, 3, 4)      # False

'relax' in 'rancho relaxo'  # True, it works on strings too
```

---

## Destructuring

Destructuring is accessing each item of the tuple at once instead of one index at a time.

This works but it is long.
```python
def process(color):
    r = color[0]
    g = color[1]
    b = color[2]
```

This does the same thing and reads much better.
```python
def process(color):
    r, g, b = color
```

It also works directly in a loop.
```python
for r, g, b in colors:
    print(r, g, b)
```

Python can also compare tuples. It compares the elements at index 0 first, and if those are equal it moves to the next index.
```python
(2, 4) < (5, 1)   # True, because 2 < 5
```

---

## Tuple Functions

These built in functions work on tuples.
```python
min((5, 2, 3))      # 2
max((5, 2, 3))      # 5
sum((1, 2, 3))      # 6
sorted((3, 1, 2))   # [1, 2, 3]
```

Two remarks on this. When using `sorted`, the elements should all be the same type or you get an error. Also, `sorted` always hands you back a list, even when you gave it a tuple.

---

## Named Tuples

In order to access elements in a tuple in a nicer way, we can give each element a name. Instead of calling an element by its index, we call it by its name.

Before working with named tuples you have to import the function.
```python
from collections import namedtuple
```

Defining a named tuple and creating an object from it.
```python
Color = namedtuple("Color", ["r", "g"])
obj = Color(255, 128)
```

Selecting an element.
```python
obj.r    # 255
obj.g    # 128
```

---

## Lists

Tuples are immutable. Once created they can not be changed, so we can not remove, add, or overwrite elements. Lists are the same as tuples, except they can be modified. Lists are written using `[]`, and a list with one element is written normally as `[a]`.

The same built in functions work here.
```python
min([5, 2, 3])      # 2
max([5, 2, 3])      # 5
sum([1, 2, 3])      # 6
sorted([3, 1, 2])   # [1, 2, 3]
```

Indexing works like string and tuple indexing, but here you can also overwrite.
```python
my_list[index] = new_value
```

Adding items.
```python
my_list.append(new_item)          # adds the item to the end of the list
my_list.insert(index, new_item)   # adds the item at the index mentioned
```

Removing items.
```python
my_list.pop(index)        # removes the value at the index mentioned
del my_list[index]        # same idea, using del
del my_list[start:stop]   # deletes a whole slice
my_list.remove(item)      # removes the first occurrence of item
```

Adding two lists together.
```python
list1 += list2
list1.extend(list2)   # same result
```

---

## Stateful And Stateless Objects

This is the difference between the two styles of programming you will hear about.

Functional programming works with stateless objects, meaning they can not change.
```python
0, 1, 2             # integers
0.1, 0.2            # floats
True, False         # booleans
"Hello"             # strings
(1, 2, 3)           # tuples
```

Imperative programming works with stateful objects, meaning they can change.
```python
[1, 2, 3]           # lists
```

---

## Checking Equality

There are two different questions you can ask about two lists, and they do not mean the same thing.

Checking if two lists have the same content.
```python
list1 == list2   # returns True or False
```

Checking if two lists are actually the same object in memory.
```python
list1 is list2   # False, even when the content is identical
list1 is list1   # True
```

When working with immutable objects, so the stateless ones, the distinction between equal objects and same objects does not matter. When working with mutable objects, so the stateful ones, the distinction does matter, and you should be checking equality with `==`.

---

## Conversion

You can convert between the data types freely.
```python
tuple("string")     # ("s", "t", "r", ...)
tuple([1, 2, 3])    # (1, 2, 3)
str([1, 2, 3])      # "[1, 2, 3]"
str((1, 2, 3))      # "(1, 2, 3)"
list((1, 2, 3))     # [1, 2, 3]
list("string")      # ["s", "t", "r", ...]
list([1, 2, 3])     # [1, 2, 3]
```

That last one looks useless but it is not. List to list is how you make a copy of a list instead of pointing at the same object.

---

## Splitting And Joining

Splitting cuts a string into a list using a separator.
```python
"a,b,c".split(",")   # ["a", "b", "c"]
```

Joining does the opposite, it glues a list of strings together using a separator.
```python
",".join(["a", "b", "c"])     # "a,b,c"
" and ".join(["a", "b"])      # "a and b"
```

---

## Sets

Just like tuples and lists, sets are data structures that store items, but with no duplicates. Sets can be modified. They are unordered, so there is no index, and that is exactly what makes them fast to work with.

Sets are written using `{}`, except for an empty one.
```python
s = {1, 2}
empty = set()     # writing {} does not give you an empty set
{1, 2} == {2, 1}  # True, because sets are unordered
```

To determine whether a set contains an element you could use a loop, but that is slow.
```python
for item in lst:
    if item == x:
        return True

return False
```

The `in` operator does the same job and is much faster.
```python
x in s
```

Adding and removing items.
```python
s.add(value)
s.remove(value)
s.update(value, value)   # adds multiple items at once
```

---

## Set Operations

This is where sets get genuinely useful.

![Set operations](set-operations.svg)

```python
s1.intersection(s2, s3)   # or s1 & s2, returns the common elements
s1.union(s2, s3)          # or s1 | s2, returns all elements combined
s1.difference(s2, s3)     # or s1 - s2, elements in s1 that are not in s2 and s3
s1.isdisjoint(s2)         # checks if the intersection is empty
s1.issuperset(s2)         # or s1 >= s2, checks if s1 contains all elements of s2
s1.issubset(s2)           # or s1 <= s2, checks if s2 contains all elements of s1
```

The strict versions `s1 > s2` and `s1 < s2` mean the same as above, with the extra condition that the two sets are not equal.

---

## Dictionaries

The fourth data structure that can store items. Dicts are written in the form `d = {key: value}` and the keys are unique.

Looking up a value using its key. If there is no such key you get an error.
```python
value = my_dict[key]
```

Adding items and overwriting them use the same syntax.
```python
my_dict[key] = value
my_dict['a'] = 5      # my_dict is now {'a': 5}
```

Deleting items. If the key did not appear in the dictionary you get an error.
```python
del my_dict[key]
```

To determine whether a dictionary contains a key you can use the `in` operator. Note that it only works for keys, not for values.
```python
key in my_dict
```

---

## Enumerating A Dictionary

These three methods let you walk through a dictionary.
```python
my_dict.keys()     # returns the keys of the dict
my_dict.values()   # returns the values of the dict
my_dict.items()    # returns size 2 tuples of the key and value of each item
```

To iterate over all key and value pairs you use a loop. Remember that you can not use indexing on a dictionary.
```python
for key in my_dict.keys():
    value = my_dict[key]
```

This version does the same thing and is more efficient, because you get both at once.
```python
for key, value in my_dict.items():
    print(key, value)
```

---

## Looking Up A Value With Get

`get` allows us to avoid an error if the dict does not contain the key we are searching for.
```python
my_dict.get(key, error_replacement)
my_dict.get(key)   # works exactly like my_dict[key], but returns None instead of an error
```

---

## Choosing A Data Structure

This is the quick comparison of the four, which is usually all you need to pick the right one.

![Comparison of the four data structures](data-structures.svg)

---

## Objects

You use objects when you are trying to build things outside the scale of tuples, lists, sets or dictionaries. A class is the blueprint, and an object is one thing built from that blueprint.

The format of a class.
```python
class Name:
    def __init__(self, val1, val2):
        self.field1 = val1
        self.field2 = val2

    def method_name(self):
        instructions
```

The `__init__` function is the constructor, it runs when the object is created and fills the fields. We often use the same name for the values, so the parameters, and their field. Everything below the constructor is a method, which can be called on the object later.

---

## Making Fields Private

An underscore before a field name marks it as a private field, which means it is not meant to be touched from outside the class.
```python
class Name:
    def __init__(self):
        self._field = value
```

---

## Getters And Setters

Let us build this up with a car, because it shows exactly why getters exist.
```python
class Car:
    def __init__(self, brand, color):
        self.brand = brand
        self.color = color
        self._speed = 0   # this attribute is private
```

Calling a public field works fine.
```python
my_car = Car("Seat", "Black")
print(my_car.brand)   # "Seat"
```

Calling the private one does not, because speed is private.
```python
my_car = Car("Seat", "Black")
print(my_car._speed)   # you should not be doing this
```

So we create a method to reach the speed.
```python
def speed(self):
    return self._speed
```

Now we can call the speed, but we have to write the parentheses since we are calling a function. It is ugly, out of format, and inconvenient.
```python
my_car = Car("Seat", "Black")
print(my_car.speed())   # 0
```

To call speed without the ugly parentheses we use `@property`, which turns the method into a getter.
```python
@property
def speed(self):
    return self._speed
```

Now no parentheses are needed.
```python
my_car = Car("Seat", "Black")
print(my_car.speed)   # 0
```

At this point we can read speed but we can not modify it, since it is private. To let users modify it with restrictions we use a setter.
```python
@speed.setter
def speed(self, value):
    if value > 120:
        raise ValueError("Too fast")
    else:
        self._speed = value
```

If the value is more than 120 an error is raised and the speed can not be modified. If the value is reasonable then the speed is set. That check is the whole point of making the field private in the first place.

---

## File IO

Files can be read and written to from a different location, like another file.

Opening and closing a file. When you exit the `with` block the file closes on its own, which is why it is written this way.
```python
with open("path", "permission", encoding="utf-8") as file:
    instructions
```

The permissions.
- `'r'` read only.
- `'w'` write only, empties the file first.
- `'r+'` reading and writing.
- `'a'` appending, so writing at the end of the file.

---

## Reading From Files

Three methods, depending on how much you want at a time.
```python
file.read()        # reads the whole file and returns it all in one string
file.readlines()   # reads the whole file and returns a list of strings, one per line
file.readline()    # reads the next line and returns it as a string
```

When there are no more lines left, `readline()` returns an empty string. While reading, any enter in the file is represented with `\n`.

---

## Writing To Files

```python
file.write("string")                    # writes the string at the position we are at
file.writelines(['s1', 's2', 's3'])     # writes all the strings to the file
```

Neither of these adds line breaks for you, so when you want to enter a new line you put `\n` in the string yourself.

One last thing to keep in mind if the file does not exist yet.
- `'w'` and `'a'` will create the file.
- `'r'` and `'r+'` will cause an error.

---
