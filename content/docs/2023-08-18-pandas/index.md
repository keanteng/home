---
title: "Pandas"
description: "My Kaggle Learning Note "
date: "2023-08-18"
draft: false
author: "Kean Teng Blog"
tags: ["Pandas", "Data Frame", "Python"]
weight: 5
summary: "In this course, we will explore on the Python pandas module which is a popular library for data analysis. With pandas, we can use it to create data and also work or manipulate the existing data. "
---

<center><img src="https://images.unsplash.com/32/Mc8kW4x9Q3aRR3RkP5Im_IMG_4417.jpg?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=1170&q=80"  class = "center"/></center>
<p style="text-align: center; color:grey;"><i>Images from Unsplash</i></p>

> *Disclaimer: This article is my learning note from the courses I took from Kaggle.*

In this course, we will explore on the Python `pandas` module which is a popular library for data analysis. With `pandas`, we can use it to create data and also work or manipulate the existing data. 

## 1. Introduction
`pandas` has two core objects known as `DataFrame` and Series. A `DataFrame` is a table where it consists of individual entries in the form of an array. Each entry corresponds to a row and a column. For example:

Let us define a `DataFrame`, we can also write string in the `DataFrame`:
```py
import pandas as pd
pd.DataFrame({
    'x': [1,2,3],
    'y': [3,2,1],
})
```

```
| x | y |
|---|---|
| 1 | 3 |
| 2 | 2 |
| 3 | 1 |
```

In the above code we are defining x and y as the column names, and we assigned values to the column. Now, let's try to put an index or row labels to the `DataFrame`:

```py
pd.DataFrame({
    'x': [1,2,3],
    'y': [3,2,1]},
    index = ['Row 1', 'Row 2', 'Row 3']    
)
```
```
|     | x | y |
|-----|---|---|
|Row 1| 1 | 3 |
|Row 2| 2 | 2 |
|Row 3| 3 | 1 |
```

On the other hand, for Series, it represents a sequence of data values. Just now we have shown that `DataFrame` is in the form of table, but Series is in the form of a list. Let's try defining one:

```py
pd.Series([1,2,3,4])
```

We can think of a series as only a single column of a `DataFrane`. In fact, we can add row label or index to Series too:

```py
pd.Series([1,2,3,4], index = ['Row 1', 'Row 2', 'Row 3', 'Row 4'], name = "My Series")
```
```
|Row 1| 1 |
|Row 2| 2 |
|Row 3| 3 |
Name: Product A
```

### 1.1 Reading Data Files
We can use `pandas` to read `.csv` file as well, by typing `df = pd.read_csv('file.csv')`.

To check how large the `DataFrame` is:
```py
df.shape()
```

Here's how we can take a preview of the first five rows of the `DataFrame`:
```py
df.head()
# try df.head(10) for first 10 rows
```

You might be wondering, can we display only the last five rows? Of course:
```py
df.tail()
```

## 2. Indexing, Selecting & Assigning
We have learned how to create a Series and a `DataFrame`. Now let's look at how do we select specific value in the `DataFrame` in a quick and effective manner.

Let's say we have a `DataFrame` called `df` with the following column names: country, points, price, province, state and so on. If we would like to only see the country column, we could access the column by doing this:

```py
df.country

# or alternatively
df['country']
```

Notice two approaches are provided above. Neither of them is more or less syntactically valid than the other. But let's say we have a column name `My House`, we just could not write `df.My House`, it wouldn't work. 

If we want to know the first item in the country column, we can use the indexing operator to drill down to a single specific value:
```py
df['country'][0]
```

### 2.1 Indexing in Pandas
In `pandas`, indexing works in one of two paradigms. We will start with the first one, the index-based selection where data is selected based on its numerical position in the data. To select the first row of a data:

```py
df.iloc[0]
```

For the `loc` and `iloc` function, row will come first then column. 
```py
df.iloc[:,0] # display all row of first column
```

The `:` operator means everything. We can use it to indicate the range of values that we are interested in:

```py
df.iloc[:3, 0] # first three rows of the first column (index 3 or fourth row excluded)
```

```py
df.iloc[1:3, 0] # second and third rows of the first column

# alternatively
df.iloc[[1,2], 0]
```

Interestingly, we can also use negative number to select data items. For negative number, it will start counting from the end of the values:

```py
df.iloc[-5:] # get the last five rows for all columns
```

Furthermore, a second paradigm for attribute selection is done by using the `loc` operator, called as label-based selection. For this approach, it is the data index value, rather than the position that matters. 

```py
df.loc[0, 'country'] # first item in the country column
```

When we compare `iloc` and `loc`, `iloc` seems to be conceptually simpler than `loc`. This is because we ignore the dataset's indices. Using `iloc`, dataset is treated like a big matrix, that we index into by position. For `loc`, we make use of the information in the indices to do its work. Of course, dataset usually comes with indices and this makes `loc` to be easier to work with. 

```py
df.loc[:, ['country', 'regions', 'states']] # display all rows for the column of country, regions and states
```

So, how do we choose between `iloc` and `loc`? For `iloc`, it uses the Python standard library indexing scheme, where the first element in the range is included, and the last one is excluded. But for `loc`, it will include both the first and last element. Of course, using `iloc` can be confusing sometimes. Here's a comparison:

```
Using loc: 0:10
Output: 0,1,...,10

Using iloc 0:10
Output: 0,1,...,9
```

### 2.2 Manipulation Index
When using label-based selection, the power of it comes from the labels in the index. We can manipulate index in any way we see fit as well by using `set_index()`

```py
df.set_index("country")
```

### 2.3 Conditional Selection
In order to do some interesting things with our dataset, we often need to ask questions based on certain conditions. Suppose that we want to know the better-than-average wine produced in Sweden, we can start by checking whether the wine is from Sweden or not:

```py
wine.country == 'Sweden'

# alternatively
wine.loc[wine.country == 'Sweden']
```

Now we would like to add an extra condition. We want the wine review to also be at least 90 points:

```py
wine.loc[(wine.country == 'Sweden') & (wine.points >= 90)]
```

If we would like to know whether a wine is from Sweden or a wine is rated above average:

```py
wine.loc[(wine.country == 'Sweden') | (wine.points >= 90)]
```

What if we want to know whether a wine is produced in Sweden or Italy?

```py
wine.loc[wine.country.isin(['Sweden', 'Italy'])]
```

We notice that for the wine data produced in Sweden or Italy, there are some rows where the points given is empty, we would like to filter them out:

```py
wine.loc[wine.points.notnull()]

# to check for the null rows
wine.loc[wine.points.isnull()]
```

### 2.4 Assigning Data
Now let's look at the method to assign data to a `DataFrame`:

```py
wine['critic'] = everyone # assingning a constant to the column named critic
```
```py
wine['index_reverse'] = range(len('country'),0,-1) 
```

## 3. Summary Functions & Maps
In the previous section, we learned about selecting relevant data from a `DataFrame` and a Series. However, data does not always come out in the format that we want, and often we would want to do some more works to reformat the data at hand. For starter, we could use the `describe` function to generate a high-level summary of the attributes of the given column:

```py
wine.describe()
```

We can select the column that we want to know the summary:

```py
winde.critic.describe()
```

Notice that for numerical column, the `describe` function is telling use about the name, the maximum and the minimum value and a few other information. If we only want to know, says, the mean or the unique labels in a column, we could:

```py
wine.points.mean()
wine.critic.unique()
```

Let's say we want to know for a list of unique value, how frequent they occur in the dataset:

```py
wine.critic.value_counts()
```

### 3.1 Maps
Map is a mathematical term where it describes that a function takes one set of values and maps them to another set of value. Oftentimes, we have to create new representations from existing data or perform transformation to the data. Maps are extremely important to help us to achieve our works.

An example where we remean the scores the wines received to 0:

```py
wine_points_mean = wine.points.mean()
wine_points_mean.map(lambda p : p - wine_points_mean)

# alternative
wine_points_mean = wine.points.mean()
wine.points - wine_points_mean
```

In the function above, `map()` expects a single value from the Series and return a transformed version of that value. A new series with all the transformed values will be returned by `map()`.

On the other hand, we can use `apply()` which is also an equivalent method. Now we want to transform the whole `DataFrame` by calling a function on each row:

```py
def remean(row):
    row.points = row.points - wine_points_mean
    return row

wine.apply(remean, axis = 'columns')
```

If we set the `axis = index`, then we would need to give a function to transform each column rather than each row. 

`map` and `apply` do not modify the original data they're call on. They return new, transformed series and `DataFrame` respectively. 

Pandas understand what we do when we perform operations between Series of equal length. These operations are faster than using `map` or `apply`. Of course, we can use these functions with more advanced case like conditional logic. 

```py
wine.country + " - " + wine_region
```

## 4. Grouping & Sorting
### 4.1 Groupwise Analysis
In previous section, we learned about `value_counts()` to count for the unique labels' occurrences. Here's an alternative:

```py
wine.groupby('points').points.count()
```

This `groupby` function creates a group of reviews which allotted the same point values to the given wine. Then from these groups, we select the `points` column and count how many times they appear. For example, we want to get the cheapest wine in each point value category:

```py
wine.groupby('points').price.min()
```

We can think of each group as a slice of our `DataFrame` with only data with values that match. This `DataFrane` is accessible to use directly using `apply`, and we can manipulate the data. If we want to select the name of the first wine reviewed from each winery:

```py
wine.groupby('winery').apply(lambda df : df.title.iloc[0])
```

We can also group more than one column. Let's say we want to pick out the best wine by country and province:

```py
wine.groupby(['country', 'province']).apply(lambda x : x.loc[x.points.idmax()])
```

Furthermore, if we want to run a bunch of different functions on the `DataFrame` at once, we can generate a simple statistical summary of the dataset with `agg`:

```py
wine.groupby(['country']).price.agg([len, min, max])
```

### 4.2 Multi-indexes
A multi-index example:

```py
countries_reviewed = wine.groupby(['country', 'province']).description.agg([len])
countries_reviewed
```

```
| country |     province     | len |
|---------|------------------|-----|
|Argentina| Mendoza Province | 3264|
|         | Other            | 536 |
|...      | ...              |     |
```

If we check the type:
```py
mi = countries_reviewed.index
type(mi)

# output
# pandas.core.indexes.multi.MultiIndex
```

There are a few ways to deal with this tiered structure which are absent for single-level indices. To retrieve a value, we need to provide two levels of labels. For the first method, we can convert the index back to the regular index:

```py
countries_reviewed.reset_index()
```

```
| | country |     province     | len |
|-|---------|------------------|-----|
|0|Argentina| Mendoza Province | 3264|
|1|Argentina| Other            | 536 |
|2|...      | ...              | ... |
```

### 4.3. Sorting
Previously, we learned about the grouping function to group data in index order. But what about in value order? Can we order the rows from the grouping result based on values in the data rather than index?

```py
countries_reviewed = countries_reviewed.reset_index()
countries_reviewed.sort_values(by = 'len')
```

By default, the sorting will be in ascending order, we can change that by changing the `ascending` parameter:

```py
countries_reviewed.sort_values(by = 'len', ascending = False)
```

Moreover, if we just want to sort by index values, simply leave the `by` parameter blank:

```py
countries_reviewed.sort_values()
```

Of course, we could sort more than one column at a time:
```py
countries_reviewed.sort_values(by = ['country', 'len'])
```

## 5. Data Types & Missing Values
In this section, we will explore how to investigate data types within a `DataFrame` and how to replace entries in it.

```py
wine.price.dtype() # for the specified column

wine.dtype() # for all columns
```

`dtype` function tells us how pandas store the data internally such as `float64` or `int64`. We can convert a column from one type to the other too:

```py
wine.points.astype('float64')
```

### 5.1 Missing Value
In a dataset, we might see values being given as `NaN` or "not a number". Using Pandas, we can check for the missing data in a `DataFrame`:

```py
wine[pd.isnull(wine.country)]
```

To replace the missing value in data, we can use the `fillna()` function:
```py
wine.region.fllna("Unknown")
```

If we want to replace value, here's what we can do:
```py
wine.twitter_handle.replace("@x", "@abc")
```

## 6. Renaming & Combining
`rename` is a function to let you change index names or column names:

```py
wine.rename(column = {'point': 'score'})
```

This function allows us to rename index or column by specifying an index or a column keyword parameter:

```py
wine.rename(index = {0: 'first', 1: 'second'})
```

Of course, we could give name to a row index and the column index as well:

```py
wine.rename_axis('wines', axis = 'rows').rename_axis('fields', axis = 'columns')
```

### 6.1 Combining
The simplest combination method in Pandas is `concat`. This function is useful when we have data in different `DataFrame` but having the same columns:

```py
pd.concat([can_yt, bri_yt])
```

Moreover, for `join`, it will combine different `DataFrame` which have an index in common:

```py
left = canadian_youtube.set_index(['title', 'trending_date'])
right = british_youtube.set_index(['title', 'trending_date'])

left.join(right, lsuffix='_CAN', rsuffix='_UK')
```

For the above example, the `lsuffix` and `rsuffix` parameter is necessary because the data has the same columns names in both of the datasets. If it is not, then we could just ignore that.