# What is Pandas ?

**Pandas** is one of the most popular and powerful libraries in Python for data analysis and manipulation. It provides high-performance, easy-to-use data structures and tools that make working with structured data efficient and intuitive.

At its core, Pandas introduces two main data structures:
- **Series**: a one-dimensional labeled array like a column in a table.
- **Data Frame**: a two-dimensional labeled data structure like a table itself.

Importing Pandas.
```python
import pandas as pd
```

---

## Creating  Series And Data Frames

Creating Series.
```python
# From a list
series = pd.Series([10, 20, 30, 40])

# From a dictionary (keys become the index)
series = pd.Series({'a': 1, 'b': 2, 'c': 3})

# Custom index
series = pd.Series([5, 10, 15], index=['x', 'y', 'z'])
```

Creating Data frames.
```python
# From a list of dictionaries
dataframe = pd.DataFrame([
    {'Name': 'Alice', 'Age': 25},
    {'Name': 'Bob', 'Age': 30}
])

# From a dictionary of lists
dataframe = pd.DataFrame({
    'Name': ['Alice', 'Bob', 'Charlie'],
    'Age': [25, 30, 35],
    'City': ['NY', 'LA', 'Chicago']
})

# From a CSV file
dataframe = pd.read_csv('file name')
```

---

## Sub Setting Data Frames

Sub setting columns.
```python
# Returns only the specified column of the dataframe
dataframe['column']

# Returns only the specified columns of the dataframe
dataframe[['column', 'column']]

# Returns the values of the specified text column conditionally
dataframe[dataframe['column'] == 'value']

# Returns the values of the specified numeric column conditionally
dataframe[dataframe['column'] > number]
```

**Important Note:** Data frames are mutable. If you modify a subset of a data frame without creating a copy, the changes will also affect the original data frame.

Creating a copy of a data frame.
```python
dataframe.copy()
```

---

## Manipulating Data Frames Using Methods

General methods.
```python
# Returns the first couple rows of a dataframe
dataframe.head()

# Returns the name of colums and the data types they contain
dataframe.info()

# Returns the number of each value in the specified column
dataframe['column'].value_counts()

# Setting a column as an index column
dataframe.set_index('column')
```

Statistics methods.
```python
# Returns mean median or mode of a column
dataframe['column'].mean()
dataframe['column'].median()
dataframe['column'].mode()

# Returns min or max of a column
dataframe['column'].min()
dataframe['column'].max()

# Returns variance or standard deviation of a column
dataframe['column'].var()
dataframe['column'].std()

# Returns the sum of a column
dataframe['column'].sum()

# Returns the quantiles of a column
dataframe['column'].quantile()

# Returns summary statistics for all numerical columns
dataframe.describe()

# Returns summary statictics for the column specified
dataframe['column'].describe()

# Returns a combination of summary statistics
dataframe['column'].agg(['mean', 'median', 'mode'])

# Returns a combination of summary statistics by calling functions
dataframe['column'].agg([function, function])

# Returns the specified column with grouped values of the specified grouped column
dataframe.groupby('grouped column')['column']

# Returns the sum of each group
dataframe.groupby('grouped column')['column'].sum()
```

Sorting methods.
```python
# Returns the dataframe with the values of the specified column sorted (asc)
dataframe.sort_values('column')

# Returns the dataframe with the values of the specified columns sorted (asc)
dataframe.sort_values(['column', 'column'])

# Returns the dataframe with the values of the specified column sorted (desc)
dataframe.sort_values('column', ascending = False)

# Returns the dataframe with the values of the specified columns sorted (desc)
dataframe.sort_values(['column', 'column'], ascending = [False, False])
```

---

## Manipulating Data Frames Using Attributes

General attributes.
```python
# Returns the values of a data frame
dataframe.value

# Returns an Index object that contains all the column labels of a data frame
dataframe.columns 

# Returns a RangeIndex object that contains all the row labels of a data frame
dataframe.index
```

---

## Data Frame Indexing

Using loc to return rows using their label.
```python
# If the data frame has a default index column
dataframe.loc[index]

# If the data frame has a custom index column
dataframe.loc['label']
```

Using loc to return specific rows of one column.
```python
dataframe.loc['label', 'column']
```

Using loc to return rows using their value.
```python
dataframe.loc[dataframe['column'] == 'value']
```

Using i-loc to return rows using their position (filtering rows).
```python
dataframe.iloc[index]
```

---