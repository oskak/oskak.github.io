---
title: Introduction to Pandas (1)
tags: [Pandas]
---

## The Pandas Series Object
A Pandas `Series` is a one-dimensional array of indexed data. It can be created from a list or array as follows:
```
>>> data = pd.Series([0.25, 0.5, 0.75, 1.0])
>>> data
0    0.25
1    0.50
2    0.75
3    1.00
dtype: float64

>>> data.values
array([0.25, 0.5 , 0.75, 1.  ])
>>> data.index
RangeIndex(start=0, stop=4, step=1)

>>> data[1]
0.5
>>> data[1:3]
1    0.50
2    0.75
dtype: float64
```

This explicit index definition gives the `Series` object additional capabilities. For example, the index need not be an integer, but can consist of values of any desired type. For example, if we wish, we can use strings as an index:
```
>>> data = pd.Series([0.25, 0.5, 0.75, 1.0],
...                  index=['a', 'b', 'c', 'd'])
>>> data
a    0.25
b    0.50
c    0.75
d    1.00
dtype: float64
```

## Series as specialized dictionary
The Series-as-dictionary analogy can be made even more clear by constructing a `Series` object directly from a Python dictionary:
```
>>> population_dict = {'California': 38332521,
...                    'Texas': 26448193,
...                    'New York': 19651127,
...                    'Florida': 19552860,
...                    'Illinois': 12882135}
>>> population = pd.Series(population_dict)
>>> population
California    38332521
Texas         26448193
New York      19651127
Florida       19552860
Illinois      12882135
dtype: int64
```

Unlike a dictionary, though, the `Series` also supports array-style operations such as slicing:
```
>>> population['California':'Illinois']
California    38332521
Texas         26448193
New York      19651127
Florida       19552860
Illinois      12882135
dtype: int64
```

## Constructing Series objects
We've already seen a few ways of constructing a Pandas `Series` from scratch; all of them are some version of the following:
```
>>> pd.Series(data, index=index)
```

```
>>> pd.Series([2, 4, 6])
0    2
1    4
2    6
dtype: int64

>>> pd.Series(5, index=[100, 200, 300])
100    5
200    5
300    5
dtype: int64

>>> pd.Series({2:'a', 1:'b', 3:'c'})
2    a
1    b
3    c
dtype: object
```

## DataFrame
If a `Series` is an analog of a one-dimensional array with flexible indices, a `DataFrame` is an analog of a two-dimensional array with both flexible row indices and flexible column names.

```
>>> area_dict = {'California': 423967, 'Texas': 695662, 'New York': 141297,
...              'Florida': 170312, 'Illinois': 149995}
>>> area = pd.Series(area_dict)
>>> area
California    423967
Texas         695662
New York      141297
Florida       170312
Illinois      149995
dtype: int64

>>> states = pd.DataFrame({'population': population,
...                        'area': area})
>>> states
            population    area
California    38332521  423967
Texas         26448193  695662
New York      19651127  141297
Florida       19552860  170312
Illinois      12882135  149995

>>> states.index
Index(['California', 'Texas', 'New York', 'Florida', 'Illinois'], dtype='object')
>>> states.columns
Index(['population', 'area'], dtype='object')

>>> states['area']
California    423967
Texas         695662
New York      141297
Florida       170312
Illinois      149995
Name: area, dtype: int64
```

## Constructing DataFrame objects
A `DataFrame` is a collection of `Series` objects, and a single-column `DataFrame` can be constructed from a single `Series`:
```
>>> pd.DataFrame(population, columns=['population'])
            population
California    38332521
Texas         26448193
New York      19651127
Florida       19552860
Illinois      12882135
```

```
>>> pd.DataFrame([{'a': 1, 'b': 2}, {'b': 3, 'c': 4}])
     a  b    c
0  1.0  2  NaN
1  NaN  3  4.0
```

```
>>> pd.DataFrame(np.random.rand(3, 2),
...              columns=['foo', 'bar'],
...              index=['a', 'b', 'c'])
        foo       bar
a  0.401837  0.214431
b  0.612321  0.883587
c  0.122017  0.943897
```

```
>>> A = np.zeros(3, dtype=[('A', 'i8'), ('B', 'f8')])
>>> A
array([(0, 0.), (0, 0.), (0, 0.)], dtype=[('A', '<i8'), ('B', '<f8')])
>>> pd.DataFrame(A)
   A    B
0  0  0.0
1  0  0.0
2  0  0.0
```

## Data Selection in Series
```
>>> data = pd.Series([0.25, 0.5, 0.75, 1.0],
...                  index=['a', 'b', 'c', 'd'])
>>> data
a    0.25
b    0.50
c    0.75
d    1.00
dtype: float64

>>> 'a' in data
True
>>> data.keys()
Index(['a', 'b', 'c', 'd'], dtype='object')
>>> data.items()
<zip object at 0x1170b8340>
>>> list(data.items())
[('a', 0.25), ('b', 0.5), ('c', 0.75), ('d', 1.0)]
>>> data['e'] = 1.25
>>> data
a    0.25
b    0.50
c    0.75
d    1.00
e    1.25
dtype: float64
```

## Indexers: loc, iloc
```
>>> area = pd.Series({'California': 423967, 'Texas': 695662,
...                   'New York': 141297, 'Florida': 170312,
...                   'Illinois': 149995})
>>> pop = pd.Series({'California': 38332521, 'Texas': 26448193,
...                  'New York': 19651127, 'Florida': 19552860,
...                  'Illinois': 12882135})
>>> data = pd.DataFrame({'area':area, 'pop':pop})
>>> data
              area       pop
California  423967  38332521
Texas       695662  26448193
New York    141297  19651127
Florida     170312  19552860
Illinois    149995  12882135

>>> data.iloc[:3, :2]
              area       pop
California  423967  38332521
Texas       695662  26448193
New York    141297  19651127

>>> data.loc[:'Illinois', :'pop']
              area       pop
California  423967  38332521
Texas       695662  26448193
New York    141297  19651127
Florida     170312  19552860
Illinois    149995  12882135

>>> data['density'] = data['pop'] / data['area']
>>> data
              area       pop     density
California  423967  38332521   90.413926
Texas       695662  26448193   38.018740
New York    141297  19651127  139.076746
Florida     170312  19552860  114.806121
Illinois    149995  12882135   85.883763

>>> data.loc[data.density > 100, ['pop', 'density']]
               pop     density
New York  19651127  139.076746
Florida   19552860  114.806121
```

Any of these indexing conventions may also be used to set or modify values; this is done in the standard way that you might be accustomed to from working with NumPy:
```
>>> data.iloc[0, 2] = 90
>>> data
              area       pop     density
California  423967  38332521   90.000000
Texas       695662  26448193   38.018740
New York    141297  19651127  139.076746
Florida     170312  19552860  114.806121
Illinois    149995  12882135   85.883763
```

## Additional indexing conventions
```
>>> data[1:3]
            area       pop     density
Texas     695662  26448193   38.018740
New York  141297  19651127  139.076746
>>> data[data.density > 100]
            area       pop     density
New York  141297  19651127  139.076746
Florida   170312  19552860  114.806121
>>> data['Florida':'Illinois']
            area       pop     density
Florida   170312  19552860  114.806121
Illinois  149995  12882135   85.883763
```

## Dropping null values
```
>>> data = pd.Series([1, np.nan, 'hello', None])
>>> data.dropna()
0        1
2    hello
dtype: object

>>> df = pd.DataFrame([[1,      np.nan, 2],
...                    [2,      3,      5],
...                    [np.nan, 4,      6]])
>>> df
     0    1  2
0  1.0  NaN  2
1  2.0  3.0  5
2  NaN  4.0  6
>>> df.dropna()
     0    1  2
1  2.0  3.0  5

>>> df[3] = np.nan
>>> df
     0    1  2   3
0  1.0  NaN  2 NaN
1  2.0  3.0  5 NaN
2  NaN  4.0  6 NaN

>>> df.dropna(axis='columns', how='all')
     0    1  2
0  1.0  NaN  2
1  2.0  3.0  5
2  NaN  4.0  6

# For finer-grained control, the thresh parameter lets you specify a minimum 
# number of non-null values for the row/column to be kept
>>> df.dropna(axis='rows', thresh=3)
     0    1  2   3
1  2.0  3.0  5 NaN
```

## Filling null values
```
>>> df.fillna(0)
     0    1  2    3
0  1.0  0.0  2  0.0
1  2.0  3.0  5  0.0
2  0.0  4.0  6  0.0
```

## A Multiply Indexed Series
```
>>> index = [('California', 2000), ('California', 2010),
...          ('New York', 2000), ('New York', 2010),
...          ('Texas', 2000), ('Texas', 2010)]
>>> populations = [33871648, 37253956,
...                18976457, 19378102,
...                20851820, 25145561]
>>> index = pd.MultiIndex.from_tuples(index)
>>> pop = pd.Series(populations, index=index)
>>> pop
California  2000    33871648
            2010    37253956
New York    2000    18976457
            2010    19378102
Texas       2000    20851820
            2010    25145561
dtype: int64
```

Now to access all data for which the second index is 2010, we can simply use the Pandas slicing notation:
```
>>> pop[:, 2010]
California    37253956
New York      19378102
Texas         25145561
dtype: int64
```

## MultiIndex as extra dimension
we could easily have stored the same data using a simple `DataFrame` with index and column labels. In fact, Pandas is built with this equivalence in mind. The `unstack()` method will quickly convert a multiply indexed `Series` into a conventionally indexed `DataFrame`:
```
>>> pop_df = pop.unstack()
>>> pop_df
                2000      2010
California  33871648  37253956
New York    18976457  19378102
Texas       20851820  25145561
```

Naturally, the `stack()` method provides the opposite operation:
```
>>> pop_df.stack()
California  2000    33871648
            2010    37253956
New York    2000    18976457
            2010    19378102
Texas       2000    20851820
            2010    25145561
dtype: int64
```

## Methods of MultiIndex Creation
The most straightforward way to construct a multiply indexed `Series` or `DataFrame` is to simply pass a list of two or more index arrays to the constructor. For example:
```
>>> df = pd.DataFrame(np.random.rand(4, 2),
...                   index=[['a', 'a', 'b', 'b'], [1, 2, 1, 2]],
...                   columns=['data1', 'data2'])
>>> df
        data1     data2
a 1  0.473484  0.899127
  2  0.928004  0.640397
b 1  0.341474  0.561293
  2  0.286458  0.351975
```

Similarly, if you pass a dictionary with appropriate tuples as keys, Pandas will automatically recognize this and use a MultiIndex by default:
```
>>> data = {('California', 2000): 33871648,
...         ('California', 2010): 37253956,
...         ('Texas', 2000): 20851820,
...         ('Texas', 2010): 25145561,
...         ('New York', 2000): 18976457,
...         ('New York', 2010): 19378102}
>>> pd.Series(data)
California  2000    33871648
            2010    37253956
Texas       2000    20851820
            2010    25145561
New York    2000    18976457
            2010    19378102
dtype: int64
```

## References
- [Python Data Science Handbook](https://jakevdp.github.io/PythonDataScienceHandbook/)
