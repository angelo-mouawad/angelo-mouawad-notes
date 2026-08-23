# Python Advanced Notes

This picks up where the basics leave off. Same language, but now the focus is on structuring code properly with classes, writing less of it with functional tools, testing it, and using it to process real data.

---

## Encapsulation

Encapsulation is the practice of hiding information so that other developers working with the code do not have to worry about it. In practice that means private attributes, so the inside of a class can change without breaking everyone using it.

---

## Properties Recap

This is the pattern from the basics, worth having in front of you before anything else.
```python
class Person:
    def __init__(self, age):
        self.age = age

    @property
    def age(self):
        return self._age

    @age.setter
    def age(self, value):
        if value < 0:
            raise ValueError
        else:
            self._age = value
```

The detail people miss is in the constructor. It says `self.age` and not `self._age`, so the assignment goes through the setter and the validation runs even when the object is first created.

---

## Dunder Methods

A dunder method, short for double underscore method, refers to the special methods in Python. The `__add__` method, for example, defines how an object should behave when the `+` operator is used with it. This is what operator overloading means.

The ones tied to the arithmetic operators.
- `+` is `__add__`
- `-` is `__sub__`
- `*` is `__mul__`
- `/` is `__truediv__`
- `//` is `__floordiv__`
- `**` is `__pow__`
- `%` is `__mod__`

Example on a class.
```python
class Money:
    def __init__(self, amount):
        self.amount = amount

    def __add__(self, other):
        return Money(self.amount + other.amount)
```

---

## Print

The `__str__` method returns a readable string representation of an object. This method is called when you use the built in print function, so without it you get the default output with the memory address in it.
```python
class Money:
    def __str__(self):
        return f"{self.amount} euro"
```

---

## Static Methods

A class is a blueprint for objects, so members are not part of the class itself, they are part of objects of that class. It is possible though to attach a method to the class itself instead of to its objects. Static methods do not use the `self` parameter and can be called directly on the class without creating an object first.
```python
class Converter:
    @staticmethod
    def to_celsius(fahrenheit):
        return (fahrenheit - 32) / 1.8
```

Calling it.
```python
Converter.to_celsius(100)
```

You can think of static methods as functions outside a class that should be included in the class for better structure. They are utility functions that logically belong to a class but do not need access to any instance specific data.

---

## Inheritance

Inheritance provides a mechanism for code reuse and for structuring code hierarchies, by allowing a subclass to inherit attributes and methods from a superclass.

![Inheritance hierarchy](inheritance-hierarchy.svg)

```python
class Shape:
    def __init__(self, color):
        self.color = color

    def describe(self):
        return f"This is a {self.color} shape"


class Circle(Shape):
    def __init__(self, color, radius):
        super().__init__(color)
        self.radius = radius

    def area(self):
        return 3.14 * self.radius ** 2
```

The class in brackets is the superclass, and `super().__init__(color)` calls the superclass constructor so you do not have to repeat what it already does.

One thing to remember. Calling a method that exists in both the child and the superclass will call the one in the child class.

---

## Abstract Methods

Abstract methods are methods that are declared in a base class but do not contain any implementation. They are meant to be implemented by the subclasses, so they serve as a blueprint, and any subclass of that base class must implement them.

![Abstract base class and polymorphism](abstract-polymorphism.svg)

```python
from abc import ABC, abstractmethod


class Shape(ABC):
    @abstractmethod
    def calculate_area(self):
        pass


class Circle(Shape):
    def __init__(self, radius):
        self.radius = radius

    def calculate_area(self):
        return 3.14 * self.radius ** 2
```

Two remarks that come up in exams.
- You can not create an object of a class that contains abstract methods.
- A class is only actually abstract when it contains at least one abstract method or property.

---

## Polymorphism

Abstract methods allow dynamic binding, meaning the right method is chosen at runtime based on the actual object type. Each subclass can implement the abstract method in its own way, but has to follow the rules set by the abstract class.

That is runtime polymorphism. Different subclasses have different implementations, and the code calling them does not need to know which one it is holding.
```python
shapes = [Circle(2), Square(3)]

for shape in shapes:
    print(shape.calculate_area())
```

---

## Abstract Properties

You can also combine `@property` and `@abstractmethod`. These are properties that are defined in an abstract base class but must be implemented in the subclasses. You use this when you want to enforce a certain structure in the subclasses.
```python
from abc import ABC, abstractmethod


class Shape(ABC):
    @property
    @abstractmethod
    def color(self):
        pass
```

The order matters here, `@property` goes on top.

---

## Super And Abstract Methods

Super is not only used to call the base class constructor. It is also used by methods in a child class to call abstract methods in a base class, which is useful when the abstract method already does part of the work.
```python
class A(ABC):
    def a(self):
        print("a in A")

    @abstractmethod
    def b(self):
        print("b in A")


class B(A):
    def b(self):
        super().b()
        print("b in B")
```

Calling the method `b` on an object of the child class prints.
```
b in A
b in B
```

---

## Functional Programming

Functional programming is a paradigm where we write programs by combining functions and avoiding repetition. The focus is on cleaner and more maintainable code.

Higher order functions are the tool for that, and they come in two shapes.
- Generalizing functions, so extracting hard coded constants into function arguments.
- Functions as arguments, so assigning functions to variables and passing them around.

---

## Generalizing

This function does one specific job, and the moment you need another director you have to write it again.
```python
def count_movies_by_stan(movies):
    no_movies_by_stan = 0
    for movie in movies:
        if movie.director == "Stan":
            no_movies_by_stan += 1
    return no_movies_by_stan
```

Pulling the hard coded name out into a parameter makes one function do the whole job.
```python
def count_movies_by_director(movies, director):
    count = 0
    for movie in movies:
        if movie.director == director:
            count += 1
    return count
```

The first one is specific, the second one is general. That is the whole idea.

---

## Lambda Functions

A lambda is a different and shorter syntax to define a function. It can be passed directly to another function without ever being named, and it should only be used for very simple functions, most commonly when a nested one liner is needed.

Syntax.
```python
lambda argument: return_statement
```

These two do exactly the same thing.
```python
def is_even(number):
    return number % 2 == 0

is_even = lambda number: number % 2 == 0
```

---

## Sorting

There are two ways to sort, and the difference is what you get back.
- `sorted(my_list)` is a built in function that returns a new sorted copy of the list.
- `my_list.sort()` is a method of the list class that sorts the list itself.

Both rely on `__lt__` behind the scenes, which is why sorting objects does not work out of the box. For that, or for any sorting that is not the normal one, they take an optional `key` argument.
```python
my_list.sort(key=lambda item: item.value)
```

---

## Min And Max With A Key

Min and max work exactly like sorting. They rely on `__lt__` too, and they take the same optional key argument for objects or for unusual orderings.
```python
min(my_list, key=lambda item: item.value)
```

The lambda is what you are passing to min or max, so it tells them which value to actually compare.

---

## List Comprehensions

There is a shorter and easier way to map or iterate over a list using a one liner.

This loop.
```python
def name(my_list):
    result_list = []
    for element in my_list:
        result_list.append(element)
    return result_list
```

Becomes this.
```python
def name(my_list):
    result_list = [element for element in my_list]
```

Filtering works the same way, you just tack the condition on the end.
```python
def name(my_list):
    filtered_list = [element for element in my_list if condition]
```

![Anatomy of a list comprehension](comprehension-anatomy.svg)

---

## Set And Dictionary Comprehensions

Set comprehensions are the same as list ones but using `{}`. Same syntax, except you get no duplicates and no order.
```python
{x for x in my_list}
```

Dictionary comprehensions are the same as set ones, except the element is a key and value pair.
```python
{key: value for key, value in pairs}
```

---

## Nested For Loops

All these comprehensions can use nested for loops, and the pattern tells you which one you are looking at.
```python
[x for x in datatype]              # a normal for loop
[x for x in range(10)]             # a normal for loop
[x + y for x in dt1 for y in dt2]  # nested
```

---

## Useful Built In Functions

These all work on the collections you build with comprehensions.
- `len()`, `min()`, `max()` and `sum()` do what you expect.
- `all()` returns True if all items are True, and returns True if it is passed nothing.
- `any()` returns True if any item is True, and returns False if it is passed nothing.
- `zip()` pairs up corresponding elements from two collections.
- `enumerate()` pairs up elements with their index.

The empty cases of `all()` and `any()` look wrong at first, but they are consistent. There is no False in an empty collection, and there is no True in one either.

---

## Generator Functions

Generator functions are functions that behave like iterables, so you can loop over them the same way you loop over a list.

This is a normal collection.
```python
languages = ["Python", "Java"]

for language in languages:
    print(language)
```

This is the generator version of it.
```python
def languages():
    yield "Python"
    yield "Java"


for language in languages():
    print(language)
```

Yield acts like return, except it remembers its position, so the next time a value is asked for the function carries on from where it stopped.

![List versus generator](list-vs-generator.svg)

A few things worth remembering.
- Generators use way less memory because the values are not stored like they are in an iterable.
- Generators are faster than iterables.
- Generators only iterate once after being called.
- Range is not a generator, it is a special class.
- Instead of looping you can use the built in `next()` function. Every time it is called it returns the next value.

---

## Generator Comprehensions

Generator comprehensions work exactly the same as the other comprehensions, but they do not store the values, they produce elements one by one. The only difference in the syntax is the brackets.
```python
(x for x in collection)
```

So this generator function.
```python
def square_numbers(nums):
    for i in nums:
        yield i * i
```

Is the same as this one liner.
```python
my_nums = (x * x for x in nums)
```

---

## Recursion

A recursive function is a function that calls itself. It keeps calling itself, like a loop, until some condition is met that returns a result.
```python
def factorial(n):
    if n == 1:
        return 1
    else:
        return n * factorial(n - 1)
```

The first part is the base case, the part that stops everything. The second part is the recursive case.

![How the factorial recursion unwinds](recursion-factorial.svg)

---

## Iteration Vs Recursion

The two solve the same problems, so the choice comes down to this.
- Iteration is used for simpler problems and uses less memory, since it does not need the stack.
- Recursion is used for complex divide and conquer problems and uses more memory, since every call sits on the stack until the base case is reached.

---

## Testing With Pytest

Pytest runs the tests in your test file and collects all the functions that start with `test`. A test that returns normally is considered to have passed, so you need to throw an exception to make a test fail. That is what assert is for.

---

## Assertions

An assert checks a statement and throws an exception when it is False.
```python
def test():
    actual = [1, 2, 3]
    expected = [1, 2, 4]
    assert actual == expected
```

So when running `pytest file.py` in the terminal, you either pass or fail the assertions in the file.

Here is the full picture with two files.
```python
# main.py
def get(temp):
    if temp > 20:
        return "hot"
    else:
        return "cold"
```

```python
# test.py
from main import get


def test():
    assert get(21) == "hot"
```

You can also add a message that shows up when the test fails.
```python
assert function() == result, "message"
```

---

## Testing For Errors

Sometimes the correct behaviour is an error, and then you test that the error is actually raised.
```python
# main.py
def divide(a, b):
    if b == 0:
        raise ValueError("Cannot divide by zero")
    return a / b
```

```python
# test.py
from main import divide
import pytest


def test():
    with pytest.raises(ValueError, match="Cannot divide by zero"):
        divide(10, 0)
```

The `match` argument is the message of the error you are expecting.

---

## Parameterized Testing

Parameterized testing enables us to test a function by feeding parameters into it, which avoids writing the same assertion over and over.
```python
@pytest.mark.parameterize("parameter, expect", [
    (parameter, expected_result),
    (parameter, expected_result)
])
def test(parameter, expect):
    assert function(parameter) == expect
```

A real example.
```python
# test.py
import pytest
from main import is_prime


@pytest.mark.parameterize("num, expect", [
    (1, False),
    (2, True),
    (3, True),
    (4, False)
])
def test(num, expect):
    assert is_prime(num) == expect
```

Each line in the list runs as its own test, so you see exactly which value failed.

---

## Approx

Approx is used to compare floating point numbers, where tiny rounding errors might exist.
```python
from pytest import approx

assert 0.3 == approx(0.3)
```

You can also adjust the tolerance.
```python
assert 0.3 == approx(0.3, abs=0.1)
```

---

## Fixtures

You can create a function that runs before every test. This is useful when you want to do the same thing at the beginning of multiple tests.

Say you are working with a class that adds users to a database. You would need to freshly instantiate that class at the beginning of every test, but with a fixture you only write that instantiation once.
```python
@pytest.fixture
def user_manager():
    return UserManager()


def test_add_user(user_manager):
    assert user_manager.add_user("john") == True
```

The fixture should be passed as a parameter in every test function that should use it.

---

## Fixtures Teardown

Just like the setup, you can write code that runs after every test. Both live in the same function, split by a yield.
```python
@pytest.fixture
def fixture():
    setup_code
    yield
    teardown_code
```

Everything before the yield runs before the test, everything after it runs once the test is done.

---

## Regex

Regex refers to a powerful language for describing patterns in strings. In Python it is the `re` module, which stands for regular expressions.
```python
import re
```

The functions, all of which return None when there is no match.
```python
re.match(pattern, string, flag=0)      # matches the pattern with the beginning of the string
re.fullmatch(pattern, string, flag=0)  # matches the pattern with the whole string
re.search(pattern, string, flag=0)     # matches the pattern anywhere in the string
re.findall(pattern, string, flag=0)    # returns all non overlapping matches in the string
re.sub(pattern, replace, string, count=0, flag=0)   # replaces occurrences with replace
```

If the pattern is not found, `re.sub` returns the string unchanged.

Example.
```python
import re

text = "abc123"
pattern = r'a.c'
match = re.search(pattern, text)

if match:
    print("match verified")
```

The `r` prefix is there so the backslashes in the pattern are left alone, which matters as soon as the pattern gets more complicated.

---

## Regex Symbols

- `.` any character except a new line
- `^` matches the start of the string
- `$` matches the end of the string
- `*` matches zero or more occurrences
- `+` matches one or more occurrences
- `?` matches zero or one occurrence
- `{m}` matches m occurrences
- `[abc]` matches any one of the characters
- `(abc)` matches the group of characters
- `|` matches either the left or the right
- `(.)\1` matches any string where a character appears twice in a row
- `(.+)\1` matches any string where a group of characters appears twice in a row
- `\d` any digit
- `\s` a space

---

## Imports, Modules And Packages

Three ways to import, and they change how you call the thing afterwards.
```python
from math import ceil   # then ceil(n)
import math             # then math.ceil(n)
from math import *      # then ceil(n)
```

Math is a module. You can also import packages, and a package is just a group of modules. Pandas is a package, so it contains many modules like the errors module.
```python
from pandas.errors import EmptyDataError   # then raise EmptyDataError
import pandas                              # then raise pandas.errors.EmptyDataError
import pandas as pd                        # then raise pd.errors.EmptyDataError
```

That third way is the one you will see everywhere, since writing `pandas` on every line gets old fast.

---

## Pandas

Pandas is a fundamental package in Python for data manipulation and analysis. It provides high performance data structures and data analysis tools. The two structures you use are Series and DataFrames.

![Series versus DataFrame](series-vs-dataframe.svg)

---

## Pandas Series

A Series is a one dimensional array object capable of holding data of any type. Each element in the series has an index, or a label.

Built from a dictionary, where the keys become the index.
```python
my_series = pd.Series({'London': 10, 'UK': 20})
```

```
London    10
UK        20
dtype: int64
```

Selecting by index name, and filtering by a condition.
```python
my_series['UK']            # 20
my_series[my_series > 10]  # only the rows where the value is above 10
```

Series can also be created with lists and arrays, not only dictionaries. When you do not specify the index, it is chosen automatically as 0, 1, 2.
```python
my_series = pd.Series([10, 20, 30])
```

```
0    10
1    20
2    30
dtype: int64
```

---

## Manipulating Series

- `my_series.index` returns all the indexes.
- `my_series.index[index]` returns the specified index.
- `my_series[index_name]` returns the value corresponding to that index.
- `my_series.values` returns all the values.
- `my_series.values[index]` returns the value at the specified index.
- `my_series.size` and `my_series.count()` return the number of elements.
- `my_series.value_counts()` returns the number of times each value occurs.
- `my_series.dtype` returns the datatype of the series.

Going a bit further.
- `my_series.copy(deep=True)` creates a copy of a series.
- `pd.Series(my_series, index=[index])` creates a new series with only the specified indexes and their values.
- `my_series.drop(labels=[index, index])` removes entries based on their indexes.
- `my_series[index] = "new value"` changes the value at the specified index.
- `my_series.index = [index, index]` changes all the index names to the new ones.

---

## Pandas Dataframes

A DataFrame is a two dimensional labeled table, so basically a spreadsheet with rows and columns. Rows are labeled by an index like in a series, and columns are labeled by a column name. Unlike a series, you can change the size of a dataframe.

Built from a dictionary, where the keys become the column names.
```python
data = {
    'Name': ['Alice', 'Bob'],
    'Age': [25, 30],
    'City': ['London', 'New York']
}

my_df = pd.DataFrame(data)
```

```
    Name  Age      City
0  Alice   25    London
1    Bob   30  New York
```

Built from a list, where each inner list is a row. If the columns are not specified they will automatically be 0, 1, 2.
```python
data = [
    ['John', 25, 'New York'],
    ['Alice', 30, 'Los Angeles']
]

c = ['Name', 'Age', 'City']
my_df = pd.DataFrame(data, columns=c)
```

---

## Selecting From A Dataframe

This is the part that gets mixed up the most, so it is worth learning as three separate tools.

![Selecting a column, a row and a cell](dataframe-selection.svg)

```python
my_df['col']                    # returns the column
my_df.loc['row']                # returns the row by its label
my_df.iloc[index]               # returns the row by its position
my_df.at['row', 'col']          # returns one specific element
my_df.loc['row1':'row3', 'col1':'col3']   # returns a chunk by labels
my_df.iloc[1:3, 1:3]            # returns a chunk by positions
my_df[my_df['col'] > 30]        # returns the rows where the column is above 30
```

The short version to remember.
- Targeting columns is `df['col']`.
- Targeting rows by index is `df.iloc[index]`.
- Targeting rows normally is `df.loc['row']`.

---

## Manipulating Dataframes

Renaming columns.
```python
my_df.columns = ['col', 'col']              # renames all columns, works with the index too
my_df.rename(columns={'old': 'new'})        # renames specific columns
```

Using the `inplace=True` property makes sure a new object is not created, so the change lands on the dataframe you already have.

Adding and removing.
```python
my_df["col name"] = [1, 2, 3]               # adds a new column
my_df.drop(columns=["col name"])            # removes a column
my_df.sort_values(by='col', ascending=False)   # sorts by a column
```

Missing values, and both of these leave the original dataframe unchanged.
```python
my_df.dropna()          # removes rows with missing values
my_df.fillna('value')   # fills in missing values
```

The index.
```python
my_df.set_index("col name")   # replaces the index column with a column from the dataframe
my_df.index.name = None       # removes the index column name
```

Maths and statistics.
```python
my_df['col1'] + my_df['col2']   # adds columns, works with - * / too
my_df['col'].sum()              # the sum of one column, works with max, min, mean, median
my_df.sum()                     # the sum of every column
my_df.describe()                # count, max, min, mean and median of every column
my_df.agg(['sum', 'min'])       # specific statistics of every column
my_df.groupby('col').count()    # groups the values of a column and counts them
```

Looking at the thing.
```python
my_df.info()      # displays info about the dataframe
my_df.head(x)     # displays the first x rows
```

Joining two dataframes.
```python
pd.concat([df, df], axis=1)   # joins along the columns
pd.concat([df, df], axis=0)   # joins along the rows, this is the default
```

One small difference that catches people out. `df.count()` does not include null values, while `df.size` counts them.

---

## CSV Files

You can read data straight from a csv file, and write your dataframe back out to one.
```python
df = pd.read_csv('file.csv', sep=",", index=0)
df.to_csv('output.csv', index=True)
```

---

## Data Visualization

Pandas works with visualization libraries like Matplotlib, which lets you plot both Series and DataFrames. The imports you need.
```python
import matplotlib.pyplot as plt
import numpy as np
import pandas as pd
```

---

## Figure And Axes

The figure is the whole picture, the axes is the plot inside it.

![Figure and axes](matplotlib-anatomy.svg)

```python
fig, ax = plt.subplots()              # returns a figure with one axes
ax.plot([1, 2, 3, 4], [1, 4, 2, 3])   # plots the x and y points and connects them
ax.set_title("Title")
```

The locator tells Matplotlib to figure out the best places for the ticks on the axes.
```python
al = matplotlib.ticker.AutoLocator()
ax.xaxis.set_major_locator(al)
ax.xaxis.set_minor_locator(al)
ax.yaxis.set_major_locator(al)
ax.yaxis.set_minor_locator(al)
```

The formatter labels those ticks, in this case using engineering notation.
```python
ef = matplotlib.ticker.EngFormatter()
ax.xaxis.set_major_formatter(ef)
ax.xaxis.set_minor_formatter(ef)
ax.yaxis.set_major_formatter(ef)
ax.yaxis.set_minor_formatter(ef)
```

---

## Two Ways Of Using Matplotlib

The explicit or object oriented way, where you hold on to the figure and the axes.
```python
fig, ax = plt.subplots()
ax.plot([1, 2, 3], [4, 5, 6])
```

The implicit or fast pyplot way, where Matplotlib keeps track of them for you.
```python
plt.plot([1, 2, 3], [4, 5, 6])
```

The second one is quicker to type, the first one is the one to use as soon as you have more than one plot.

---

## Apply

You can use apply to run a function over a dataframe, which saves writing a loop.
```python
my_df['col'] = my_df.apply(function, axis=1)
```

The function being applied should be written beforehand and ready to be used. It also works with built in functions like sum, and the axis decides the direction.
```python
my_df.apply(sum, axis=0)   # one total per column, this is the default
my_df.apply(sum, axis=1)   # one total per row
```

---
