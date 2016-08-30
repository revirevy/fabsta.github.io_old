---
title: "Pandas Cheat Sheet"
description: "Pandas Cheat Sheet"
category: Programming
tags: [programming]
---


[this cheat sheet is based on the fantastic work by @Mark_Graph]. 

# Table of contents

* TOC
{:toc}


# Preliminaries
```python
import numpy as np
import matplotlib.pyplot as plt
import pandas as pd
from pandas import DataFrame, Series
```


# Get data into DataFrame

## Instantiate an empty DataFrame

```python
df = DataFrame()
```

## Load a DataFrame from a CSV file
```python
df = pd.read_csv('file.csv')# often works
df = pd.read_csv('file.csv', header=0,
    index_col=0, quotechar='"',sep=':',
    na_values = ['na', '-', '.', ''])
```

## Get data from inline CSV text to a DataFrame
```python
from io import StringIO
data = """, Animal, Cuteness, Desirable
row-1, dog, 8.7, True
row-2, cat, 9.5, True
row-3, bat, 2.6, False"""
df = pd.read_csv(StringIO(data),
	header=0, index_col=0,
	skipinitialspace=True)
```

## Load DataFrames from a Microsoft Excel file
```python
# Each Excel sheet in a Python dictionary
workbook = pd.ExcelFile('file.xlsx')
d = {} # start with an empty dictionary
for sheet_name in workbook.sheet_names:
	df = workbook.parse(sheet_name)
	d[sheet_name] = df
```

## Load a DataFrame from a MySQL database
```python
import pymysql
from sqlalchemy import create_engine
engine = create_engine('mysql+pymysql://'
+'USER:PASSWORD@HOST/DATABASE')
df = pd.read_sql_table('table', engine)
```



## Data in Series then combine into a DataFrame
```python
# Example 1 ...
s1 = Series(range(6))
s2 = s1 * s1
s2.index = s2.index + 2# misalign indexes
df = pd.concat([s1, s2], axis=1)
# Example 2 ...
s3 = Series({'Tom':1, 'Dick':4, 'Har':9})
s4 = Series({'Tom':3, 'Dick':2, 'Mar':5})
df = pd.concat({'A':s3, 'B':s4 }, axis=1)
```


## Get a DataFrame from a Python dictionary
```python
# default --- assume data is in columns
df = DataFrame({
	'col0' : [1.0, 2.0, 3.0, 4.0],
	'col1' : [100, 200, 300, 400]
})
```



## Get a DataFrame from data in a Python dictionary
```python
# --- use helper method for data in rows
df = DataFrame.from_dict({ # data by row
# rows as python dictionaries
	'row0' : {'col0':0, 'col1':'A'},
	'row1' : {'col0':1, 'col1':'B'}
	}, orient='index')
df = DataFrame.from_dict({ # data by row
# rows as python lists
	'row0' : [1, 1+1j, 'A'],
	'row1' : [2, 2+2j, 'B']
	}, orient='index')
```


## Create play/fake data (useful for testing)
```python
# --- simple - default integer indexes
df = DataFrame(np.random.rand(50,5))
# --- with a time-stamp row index:
df = DataFrame(np.random.rand(500,5))
df.index = pd.date_range('1/1/2005',
periods=len(df), freq='M')
# --- with alphabetic row and col indexes
# and a "groupable" variable
import string
import random
r = 52 # note: min r is 1; max r is 52
c = 5
df = DataFrame(np.random.randn(r, c),
columns = ['col'+str(i) for i in
range(c)],
index = list((string. ascii_uppercase+
string.ascii_lowercase)[0:r]))
df['group'] = list(
''.join(random.choice('abcde')
for _ in range(r)) )
```



# Saving as DataFrame

## Saving a DataFrame to a CSV file
```python
df.to_csv('name.csv', encoding='utf-8')
```

## Saving DataFrames to an Excel Workbook
```python
from pandas import ExcelWriter
writer = ExcelWriter('filename.xlsx')
df1.to_excel(writer,'Sheet1')
df2.to_excel(writer,'Sheet2')
writer.save()
```

## Saving a DataFrame to MySQL
```python
import pymysql
from sqlalchemy import create_engine
e = create_engine('mysql+pymysql://' +
	'USER:PASSWORD@HOST/DATABASE')
df.to_sql('TABLE',e, if_exists='replace')
```

## Saving to Python objects
``python
d = df.to_dict() # to dictionary
str = df.to_string() # to string
m = df.as_matrix() # to numpy matrix
``


# Working with the whole DataFrame

## Peek at the DataFrame contents/structure
df.info() # index & data types

dfT = df.T # transpose rows and cols
df = df.copy() # copy a DataFrame
df.iteritems()# (col-index, Series) pairs
```

## Maths on the whole DataFrame (not a complete list)
df = df.abs() # absolute values
Note: The methods that return a series default to
df = df.filter(items=['a', 'b']) # by col
```

# Working with Columns

Each DataFrame column is a pandas Series object
idx = df.columns # get col index

df.rename(columns={'old1':'new1','old2':'new2'}, inplace=True)
```
Note: can rename multiple columns at once.
s = df['colName'] # select col to Series

s = df.a # same as s = df['a']
```
Trap: column names must be valid identifiers.
df['new_col'] = range(len(df))
```

df[['B', 'A']] = df[['A', 'B']]
df = df.drop('col1', axis=1)

df['proportion']=df['count']/df['total']
df['log_data'] = np.log(df['col1'])
Note: Many more numpy mathematical functions.

## Set column values set based on criteria
df['b']=df['a'].where(df['a']>0,other=0)
```
st = df['col'].astype(str)# Series dtype
```

## Common column-wide methods/attributes
value = df['col'].dtype # type of data
label = df['col1'].idxmin()

s = df['col'].isnull()
Note: also rolling_min(), rolling_max(), and many more.
df['Total'] = df.sum(axis=1)
Note: also means, mins, maxs, etc.
df = df.mul(s, axis=0) # on matched rows
Note: also add, sub, div, etc.
df = df.loc[:, 'col1':'col2'] # inclusive
j = df.columns.get_loc('col_name')
if df.columns.is_unique: pass # ...
```

# Working with rows

## Get the row index and labels
idx = df.index # get row index

df.index = idx # new ad hoc index

df.index = range(len(df)) # set with list

df = original_df.append(more_rows_in_df)
```
df = df.drop('row_label')

df = df[df['col2'] >= 0.0]
## fake up some data

[inclusive-from : exclusive-to [: step]]
Trap: a single integer without a colon is a column label


## Select a slice of rows by label/index
[inclusive-from : inclusive–to [ : step]]
# Option 1: use dictionary comprehension
for (index, row) in df.iterrows(): # pass
```
df = df.sort(df.columns[0], ascending=False)
df.sort_index(inplace=True) # sort by row
import random as r
```
df['index'] = df.index # 1 create new col
len(a)==len(b) and all(a.index==b.index)
a = np.where(df['col'] >= 2) #numpy array
if df.index.is_unique: pass # ...
```

(<a href="#top">Back to top</a>)
<hr>

# Working with cells

Selecting a cell by row and column labels
value = df.at['row', 'col']
Note: .at[] fastest label based scalar lookup
df.at['row', 'col'] = value

df = df.loc['row1':'row3', 'col1':'col3']
Note: the "to" on this slice is inclusive.
df.loc['A':'C', 'col1':'col3'] = np.nan
Remember: inclusive "to" in the slice
value = df.iat[9, 3] # [row, col]

df = df.iloc[2:4, 2:4] # subset of the df
```
df.iloc[0, 0] = value # [row, col]

df.iloc[0:3, 0:5] = value
```

value = df.ix[5, 'col1']
```

(<a href="#top">Back to top</a>)
<hr>

# Summary: Selecting using Index

s = df['col_label'] # returns Series
Note: the difference in return type with the first two
df = df['from':'inc_to']# label slice

# r and c can be scalar, list, slice

# r and c must be label or integer

v = df.get_value(r, c) # get by row, col
__Note__: the indexing attributes (.loc, .iloc, .ix, .at .iat) can




# --- some Index attributes
```

# Joining/Combining DataFrames

Three ways to join two DataFrames:
df_new = pd.merge(left=df1, right=df2, how='outer', left_index=True, right_index=True)
How: 'left', 'right', 'outer', 'inner'
df_new = pd.merge(left=df1, right=df2, how='left', left_on='col1', right_on='col2')
```
df_new = df1.join(other=df2, on='col1',how='outer')
__Note__: DataFrame.join() joins on indexes by default.
df=pd.concat([df1,df2],axis=0)#top/bottom
__Trap__: can end up with duplicate rows or cols
df = df1.combine_first(other=df2)
Uses the non-null values from df1. The index of the

(<a href="#top">Back to top</a>)
<hr>

# GroupBy: Split-Apply-Combine
The pandas "groupby" mechanism allows us to split the


gb = df.groupby('cat') # by one columns
```
for name, group in gb:

dfa = df.groupby('cat').get_group('a')

# apply to a column ...
```
gb = df.groupby('cat')
```
# transform to group z-scores, which have
```
# select groups with more than 10 members
```

## Group by a row index (non-hierarchical index)
df = df.set_index(keys='cat')
```

(<a href="#top">Back to top</a>)
<hr>

# Working with dates, times and their indexes
t = pd.Timestamp('2013-01-01')
```
ts = ['2015-04-01 13:17:27', '2014-04-02 13:17:29']
# Series of Timestamps (good)
```
t = ['09:08:55.7654-JAN092002', '15:42:02.6589-FEB082016']
Also: %B = full month name; %m = numeric month;
date_strs = ['2014-01-01', '2014-04-01','2014-07-01', '2014-10-01']
```

## From DatetimeIndex to Python datetime objects
dti = pd.DatetimeIndex(pd.date_range(
df['date'] = [x.date() for x in df['TS']]
Note: converts to datatime.date or datetime.time. But
df = DataFrame(np.random.randn(20,3))
Note: from period to timestamp defaults to the point in
pi = pd.period_range('1960-01','2015-12',freq='M')

dr = pd.date_range('2013-01-01', '2013-12-31', freq='D')

```python
# 1st example returns string not Timestamp

## The tail of a time-series DataFrame
df = df.last("5M") # the last five months
```



## Upsampling and downsampling
# upsample from quarterly to monthly
```

## Time zones
```	

## Row selection with a time-series index
# start with the play data above
```
## The Series.dt accessor attribute
t = ['2012-04-14 04:06:56.307000', '2011-05-14 06:14:24.457000', '2010-06-14 08:23:07.520000']
```

(<a href="#top">Back to top</a>)
<hr>

# Working with missing and non-finite data
s = Series( [8,None,float('nan'),np.nan])

df = df.dropna() # drop all rows with NaN

df.fillna(0, inplace=True) # np.nan à 0


s = Series([float('inf'), float('-inf'),np.inf, -np.inf])
```


```python
b = np.isfinite(s)
```
(<a href="#top">Back to top</a>)
<hr>


# Working with Categorical Data
s = Series(['a','b','a','c','b','d','a'],
```
s = Series(['a','b','a','c','b','d','a'], dtype='category')

s = Series(list('abc'), dtype='category')
Trap: category must be ordered for it to be sorted
s = Series(list('abc'), dtype='category')
```
s = s.cat.add_categories([4])

s = s.cat.remove_categories([4])
```
(<a href="#top">Back to top</a>)
<hr>