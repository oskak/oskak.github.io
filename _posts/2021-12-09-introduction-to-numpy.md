---
title: Introduction to NumPy
tags: [NumPy]
---

## Creating Arrays from Python Lists
Use `np.array` to create arrays from Python lists:
```
>>> np.array([1, 4, 2, 5, 3])
array([1, 4, 2, 5, 3])

>>> np.array([1, 2, 3, 4], dtype='float32')
array([1., 2., 3., 4.], dtype=float32)
```

NumPy is constrained to arrays that all contain the same type.

## Creating Arrays from Scratch
```
>>> np.zeros(10, dtype=int)
array([0, 0, 0, 0, 0, 0, 0, 0, 0, 0])

>>> np.ones((3, 5), dtype=float)
array([[1., 1., 1., 1., 1.],
       [1., 1., 1., 1., 1.],
       [1., 1., 1., 1., 1.]])

>>> np.full((3, 5), 3.14)
array([[3.14, 3.14, 3.14, 3.14, 3.14],
       [3.14, 3.14, 3.14, 3.14, 3.14],
       [3.14, 3.14, 3.14, 3.14, 3.14]])

>>> # Create an array of five values evenly spaced between 0 and 1
>>> np.linspace(0, 1, 5)
array([0.  , 0.25, 0.5 , 0.75, 1.  ])

>>> # Create a 3x3 array of random integers in the interval [0, 10)
>>> np.random.randint(0, 10, (3, 3))
array([[6, 0, 4],
       [8, 0, 2],
       [0, 7, 5]])

>>> # Create a 3x3 identity matrix
>>> np.eye(3)
array([[1., 0., 0.],
       [0., 1., 0.],
       [0., 0., 1.]])
```

## NumPy Array Attributes
```
>>> x3 = np.random.randint(10, size=(3, 4, 5))
>>> x3
array([[[9, 9, 5, 6, 8],
        [1, 3, 5, 0, 5],
        [3, 4, 8, 1, 7],
        [6, 4, 2, 7, 7]],

       [[0, 4, 0, 6, 0],
        [5, 3, 5, 1, 2],
        [0, 2, 9, 0, 8],
        [7, 4, 9, 5, 0]],

       [[5, 7, 9, 6, 2],
        [3, 3, 5, 7, 9],
        [7, 9, 8, 4, 5],
        [5, 6, 7, 2, 6]]])

>>> print(x3.ndim)
3
>>> print(x3.shape)
(3, 4, 5)
>>> print(x3.size)
60
```

## Array Indexing: Accessing Single Elements
```
>>> x2 = np.array([[3, 5, 2, 4],
...        [7, 6, 8, 8],
...        [1, 6, 7, 7]])

>>> x2[0,0]
3
```

## Array Slicing: Accessing Subarrays
```
>>> x = np.arange(10)
>>> x
array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])

>>> x[:5] 
array([0, 1, 2, 3, 4])

>>> x[::2]  # every other element
array([0, 2, 4, 6, 8])

>>> x[1::2]  # every other element, starting at index 1
array([1, 3, 5, 7, 9])

>>> x[::-1]  # all elements, reversed
array([9, 8, 7, 6, 5, 4, 3, 2, 1, 0])

>>> x[5::-2]  # reversed every other from index 5
array([5, 3, 1])
```

## Creating copies of arrays
```
>>> x_copy = x.copy()
>>> x_copy[0] = 10
>>> x_copy
array([10,  1,  2,  3,  4,  5,  6,  7,  8,  9])
```

## Reshaping of Arrays
```
>>> grid = np.arange(1, 10).reshape((3, 3))
>>> grid
array([[1, 2, 3],
       [4, 5, 6],
       [7, 8, 9]])
```

## Concatenation of arrays
```
>>> x = np.array([1, 2, 3])
>>> y = np.array([3, 2, 1])
>>> np.concatenate([x, y])
array([1, 2, 3, 3, 2, 1])
```

## aggregation functions
```
>>> x = np.array([1, 2, 3])
>>> sum(x)
6
>>> np.sum(x)
6
>>> np.min(x)
1
>>> np.max(x)
3

>>> M = np.random.random((3, 4))
>>> M
array([[0.78690109, 0.57608077, 0.53244933, 0.73808146],
       [0.53157521, 0.95332754, 0.72744748, 0.39489613],
       [0.05639084, 0.19262539, 0.60533543, 0.70961251]])

# we can find the minimum value within each column by specifying 'axis=0'
>>> M.min(axis=0)
array([0.05639084, 0.19262539, 0.53244933, 0.39489613])

# we can find the maximum value within each row
>>> M.max(axis=1)
array([0.78690109, 0.95332754, 0.70961251])
```

## Introducing Broadcasting
```
>>> a = np.array([0, 1, 2])
>>> a + 5
array([5, 6, 7])

>>> M = np.ones((3, 3))
>>> M
array([[1., 1., 1.],
       [1., 1., 1.],
       [1., 1., 1.]])
>>> M + a
array([[1., 2., 3.],
       [1., 2., 3.],
       [1., 2., 3.]])
```

## Fast Sorting in NumPy
To return a sorted version of the array without modifying the input, you can use `np.sort`:
```
>>> x = np.array([2, 1, 4, 3, 5])
>>> np.sort(x)
array([1, 2, 3, 4, 5])
```

If you prefer to sort the array in-place, you can instead use the sort method of arrays:
```
>>> x.sort()
>>> x
array([1, 2, 3, 4, 5])
```

## References
- [Python Data Science Handbook](https://jakevdp.github.io/PythonDataScienceHandbook/)
