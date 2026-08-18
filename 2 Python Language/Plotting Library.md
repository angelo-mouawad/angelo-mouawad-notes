# What Is Mat Plot Lib ?

**Matplotlib** is a popular plotting library in Python used to create 2D graphs and visualizations. It’s widely used in data analysis, machine learning, and scientific computing to help visualize data in a clear and customizable way.

The name matplotlib comes from matrix plotting library. It's a descendant from the MATLAB programming language. It's by now an older library (2003) that has some quirks, but it is still important to know the basics of Matplotlib since other Python plotting libraries build on top of it.

Importing Mat Plot Lib.
```python
import matplotlib.pyplot as plt 
```

---

## Plotting Basics

Plotting syntax.
```python
plt.plotType(dataframe['column']);
plt.xlabel('name')
plt.ylabel('name')
plt.title('title')
plt.show();
```

Mat Plot Lib prints things while plotting which is ugly, so we use the `;` to tell it not to print anything other than the graph.

---
## Common Graphs For Numeric Data

The graphs below are commonly used for plotting numeric data. Histograms and Box Plots can be used with Mat Plot Lit.

| Plot Type        | Description                                                                                                                                     | When to Use                                                                                                                                                                      |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Histogram**    | Displays the distribution of a single continuous variable by dividing the data into bins and showing the frequency of observations in each bin. | To visualize the distribution of a variable, especially to identify its central tendency (mean), spread (standard deviation), and skewness (are low or high values more common). |
| **Box Plot**     | Shows the distribution of a variable using quartiles and displays potential outliers.                                                           | To get a summary of a variable's distribution in terms of its median, quartiles, and possible outliers. Useful when comparing the distribution across categories.                |
| **Density Plot** | Provides a smoothed version of a histogram.                                                                                                     | To visualize the distribution of a variable in a continuous manner. Particularly useful when comparing the distributions of multiple variables on the same plot.                 |
| **Violin Plot**  | Combines aspects of box plots and density plots.                                                                                                | To visualize both the distribution and summary statistics of a variable. Especially useful when comparing across different categories.                                           |

Plotting a box plot.
```python
plt.boxplot(dataframe['column']);
```

A boxplot provides a comprehensive view of a dataset's distribution, offering more detailed insights than typical tables. The central line within the box represents the median, splitting the data into its lower and upper halves. The box itself is framed by two lines: the lower boundary represents the 25th percentile (or Q1), meaning 25% of the data lies below this value, and the upper boundary denotes the 75th percentile (or Q3), indicating that 75% of the data is below this point.

The range between Q3 and Q1 is known as the Interquartile Range (IQR). Beyond the box, the plot extends T shaped lines we call whiskers. Their distance is calculated as `1.5 x IQR` both above and below the box, providing a range for typical data points. Any data outside these whiskers can be considered outliers.

Plotting a histogram.
```python
plt.hist(dataframe['column'], bins=numberOfIntervals, edgecolor='black');
```

A histogram splits the data up into different bins, and then counts how many data points belong to each bin. When it is not immediately obvious which of the two plot types you prefer or is the best, you can always plot both. As usual, you do not have to only use one method.

---

## Common Graphs For Categorical Data

The graphs below are commonly used for plotting categorical data. Count Plots can be used with Mat Plot Lit.

| Plot Type       | Description                                         | When to Use                                                                                                                            |
| --------------- | --------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| **Count Plot**  | Represents the frequency or count of each category. | To see how often each category appears in the data.                                                                                    |
| **Violin Plot** | Combines aspects of box plots and density plots.    | To visualize both the distribution and summary statistics of a variable. Especially useful when comparing across different categories. |

Plotting a count plot.
```python
column_counts = dataframe["column"].value_counts()
plt.bar(x=column_counts.index, height=column_counts);
```

For categorical data, categories can often serve as a basis for comparison in other plots, like boxplots. This means you can use a single category to differentiate data within such plots. You can also produce the same type of plot multiple times, once for each category, to analyze patterns within individual categories.

If you want to just look at a categoric variable, you can use a count plot, also called a bar plot. This will give you very similar information to using the `value_counts()` function in Pandas.

---

## Customizing Our Plots

It is important to understand what the Mat Plot Library calls every element of its graphs in order to customize them.

![[Mat Plot Library.png]]

Plots are made on a `Figure` object. A `Figure` object can contain multiple `Axes`. `Axes` are the things you are plotting on. Instead of using `plt.plotType` as we have done previously, we will explicitly make a `Figure` and plot on the axis. This allows us to configure the `Figure`.
```python
fig, ax = plt.subplots()
ax.boxplot(dataframe["column"])
ax.set_title("title")
ax.set_ylabel("name")
ax.set_xlabel("name")
ax.set_xticklabels("");
```

For boxplots, most of the time you dont need the x-axis to represent anything, thats why we use `ax.set_xticklabels("")` to leave the tick labels empty.

You can plot two graphs in the same figure, think or it as a grid with rows and columns. You can use `ax[0]` to customize the first graph and `ax[1]` to customize the second.
```python
fig, ax = plt.subplots(nrows=1, ncols=2, figsize=(12, 6))
fig.suptitle("title of the whole figure")

ax[0].boxplot(dataframe["column"])
ax[0].set_title("tile of the first graph")
ax[0].set_ylabel("name")
ax[0].set_xticklabels("");

ax[1].hist(dataframe['column'], bins=numberOfIntervals, edgecolor='black');
ax[1].set_title("tile of the second graph")
ax[1].set_ylabel("name")
ax[1].set_xlabel("name");
```

---