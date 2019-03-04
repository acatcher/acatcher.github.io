---
layout:     post
title:      Python daliy 2
subtitle:   numpy #副标题
date:       2019-03-04
author:     ASeeker
header-img: img/post-bg-debug.png
catalog: true
tags:
    - python
    - numpy
---


# 100 numpy exercises (first 40)


>Numpy Exercise  
>The source is [**FROM HERE**][ref1].   
THX For the Authors' contribution!  
Just a green hand just for study, I will delete it if I infringed your rights.

[ref1]:https://github.com/rougier/numpy-100

#### 1. Import the numpy package under the name `np` (★☆☆)


```python
import numpy as np
```

#### 2. Print the numpy version and the configuration (★☆☆)


```python
print(np.__version__)
np.show_config()
```

#### 3. Create a null vector of size 10 (★☆☆)


```python
Z = np.zeros(10)
print(Z)
```

#### 4.  How to find the memory size of any array (★☆☆)


```python
Z = np.zeros((10,10))
print("%d bytes" % (Z.size * Z.itemsize))
```

#### 5.  How to get the documentation of the numpy add function from the command line? (★☆☆)


```python
%run `python -c "import numpy; numpy.info(numpy.add)"`
```

#### 6.  Create a null vector of size 10 but the fifth value which is 1 (★☆☆)


```python
Z = np.zeros(10)
Z[4] = 1
print(Z)
```

#### 7.  Create a vector with values ranging from 10 to 49 (★☆☆)


```python
Z = np.arange(10,50)
print(Z)
```

#### 8.  Reverse a vector (first element becomes last) (★☆☆)


```python
Z = np.arange(50)
Z = Z[::-1]
print(Z)
```

#### 9.  Create a 3x3 matrix with values ranging from 0 to 8 (★☆☆)


```python
Z = np.arange(9).reshape(3,3)
print(Z)
```

#### 10. Find indices of non-zero elements from \[1,2,0,0,4,0\] (★☆☆)


```python
nz = np.nonzero([1,2,0,0,4,0])
print(nz)
```

#### 11. Create a 3x3 identity matrix (★☆☆)


```python
Z = np.eye(3)
print(Z)
```

#### 12. Create a 3x3x3 array with random values (★☆☆)


```python
Z = np.random.random((3,3,3))
print(Z)
```

#### 13. Create a 10x10 array with random values and find the minimum and maximum values (★☆☆)


```python
Z = np.random.random((10,10))
Zmin, Zmax = Z.min(), Z.max()
print(Zmin, Zmax)
```

#### 14. Create a random vector of size 30 and find the mean value (★☆☆)


```python
Z = np.random.random(30)
m = Z.mean()
print(m)
```

#### 15. Create a 2d array with 1 on the border and 0 inside (★☆☆)


```python
Z = np.ones((10,10))
Z[1:-1,1:-1] = 0
print(Z)
```

#### 16. How to add a border (filled with 0's) around an existing array? (★☆☆)


```python
Z = np.ones((5,5))
Z = np.pad(Z, pad_width=1, mode='constant', constant_values=0)
print(Z)
```

#### 17. What is the result of the following expression? (★☆☆)


```python
print(0 * np.nan)
print(np.nan == np.nan)
print(np.inf > np.nan)
print(np.nan - np.nan)
print(np.nan in set([np.nan]))
print(0.3 == 3 * 0.1)
```

#### 18. Create a 5x5 matrix with values 1,2,3,4 just below the diagonal (★☆☆)

>np.diag

```python
Z = np.diag(1+np.arange(4),k=-1)
print(Z)
```

#### 19. Create a 8x8 matrix and fill it with a checkerboard pattern (★☆☆)


```python
Z = np.zeros((8,8),dtype=int)
Z[1::2,::2] = 1
Z[::2,1::2] = 1
print(Z)
```

#### 20. Consider a (6,7,8) shape array, what is the index (x,y,z) of the 100th element?

>np.unravel_index

```python
print(np.unravel_index(99,(6,7,8)))
```

#### 21. Create a checkerboard 8x8 matrix using the tile function (★☆☆)
>np.tile  
>e.g.
>

```python
>>> a = np.array([0, 1, 2])
>>> np.tile(a, 2)
array([0, 1, 2, 0, 1, 2])
>>> np.tile(a, (2, 2))
array([[0, 1, 2, 0, 1, 2],
       [0, 1, 2, 0, 1, 2]])
>>> np.tile(a, (2, 1, 2))
array([[[0, 1, 2, 0, 1, 2]],
       [[0, 1, 2, 0, 1, 2]]])

>>> b = np.array([[1, 2], [3, 4]])
>>> np.tile(b, 2)
array([[1, 2, 1, 2],
       [3, 4, 3, 4]])
>>> np.tile(b, (2, 1))
array([[1, 2],
       [3, 4],
       [1, 2],
       [3, 4]])

>>> c = np.array([1,2,3,4])
>>> np.tile(c,(4,1))
array([[1, 2, 3, 4],
       [1, 2, 3, 4],
       [1, 2, 3, 4],
       [1, 2, 3, 4]])

```



```python
Z = np.tile( np.array([[0,1],[1,0]]), (4,4))
print(Z)
```

#### 22. Normalize a 5x5 random matrix (★☆☆)


```python
Z = np.random.random((5,5))
Z = (Z - np.mean (Z)) / (np.std (Z))
print(Z)
```

#### 23. Create a custom dtype that describes a color as four unsigned bytes (RGBA) (★☆☆)


```python
color = np.dtype([("r", np.ubyte, 1),
                  ("g", np.ubyte, 1),
                  ("b", np.ubyte, 1),
                  ("a", np.ubyte, 1)])
```

#### 24. Multiply a 5x3 matrix by a 3x2 matrix (real matrix product) (★☆☆)


```python
Z = np.dot(np.ones((5,3)), np.ones((3,2)))
print(Z)

# Alternative solution, in Python 3.5 and above
Z = np.ones((5,3)) @ np.ones((3,2))
print(Z)
```

#### 25. Given a 1D array, negate all elements which are between 3 and 8, in place. (★☆☆)


```python
# Author: Evgeni Burovski

Z = np.arange(11)
Z[(3 < Z) & (Z <= 8)] *= -1
print(Z)
```

#### 26. What is the output of the following script? (★☆☆)


```python
# Author: Jake VanderPlas

print(sum(range(5),-1))
from numpy import *
print(sum(range(5),-1))
```

#### 27. Consider an integer vector Z, which of these expressions are legal? (★☆☆)


```python
Z**Z
2 << Z >> 2
Z <- Z
1j*Z
Z/1/1
Z<Z>Z
```

#### 28. What are the result of the following expressions?


```python
print(np.array(0) / np.array(0))
print(np.array(0) // np.array(0))
print(np.array([np.nan]).astype(int).astype(float))
```

#### 29. How to round away from zero a float array ? (★☆☆)

>***np.copysign***  
>Change the sign of x1 to that of x2, element-wise.
>
>
>***np.ceil***  
>***np.random.uniform***

```python
#Examples
#--------
>>> np.copysign(1.3, -1)
-1.3
>>> 1/np.copysign(0, 1)
inf
>>> 1/np.copysign(0, -1)
-inf

>>> np.copysign([-1, 0, 1], -1.1)
array([-1., -0., -1.])
>>> np.copysign([-1, 0, 1], np.arange(3)-1)
array([-1.,  0.,  1.])
```


```python
# Author: Charles R Harris

Z = np.random.uniform(-10,+10,10)
print (np.copysign(np.ceil(np.abs(Z)), Z))
```

#### 30. How to find common values between two arrays? (★☆☆)

>***np.intersect1d***
>Find the intersection of two arrays.  
Return the sorted, unique values that are in both of the input arrays.
>Examples
>
>``` python
>np.intersect1d([1, 3, 4, 3], [3, 1, 2, 1])
>array([1, 3])
>```

```python
Z1 = np.random.randint(0,10,10)
Z2 = np.random.randint(0,10,10)
print(np.intersect1d(Z1,Z2))
```

#### 31. How to ignore all numpy warnings (not recommended)? (★☆☆)


```python
# Suicide mode on
defaults = np.seterr(all="ignore")
Z = np.ones(1) / 0

# Back to sanity
_ = np.seterr(**defaults)
```

An equivalent way, with a context manager:

```python
with np.errstate(divide='ignore'):
    Z = np.ones(1) / 0
```

#### 32. Is the following expressions true? (★☆☆)


```python
np.sqrt(-1) == np.emath.sqrt(-1)
```

#### 33. How to get the dates of yesterday, today and tomorrow? (★☆☆)


```python
yesterday = np.datetime64('today', 'D') - np.timedelta64(1, 'D')
today     = np.datetime64('today', 'D')
tomorrow  = np.datetime64('today', 'D') + np.timedelta64(1, 'D')
```

#### 34. How to get all the dates corresponding to the month of July 2016? (★★☆)


```python
Z = np.arange('2016-07', '2016-08', dtype='datetime64[D]')
print(Z)
```

#### 35. How to compute ((A+B)\*(-A/2)) in place (without copy)? (★★☆)


```python
A = np.ones(3)*1
B = np.ones(3)*2
C = np.ones(3)*3
np.add(A,B,out=B)
np.divide(A,2,out=A)
np.negative(A,out=A)
np.multiply(A,B,out=A)
```

#### 36. Extract the integer part of a random array using 5 different methods (★★☆)


```python
Z = np.random.uniform(0,10,10)

print (Z - Z%1)
print (np.floor(Z))
print (np.ceil(Z)-1)
print (Z.astype(int))
print (np.trunc(Z))
```

#### 37. Create a 5x5 matrix with row values ranging from 0 to 4 (★★☆)


```python
Z = np.zeros((5,5))
Z += np.arange(5)
print(Z)
```

#### 38. Consider a generator function that generates 10 integers and use it to build an array (★☆☆)


```python
def generate():
    for x in range(10):
        yield x
Z = np.fromiter(generate(),dtype=float,count=-1)
print(Z)
```

#### 39. Create a vector of size 10 with values ranging from 0 to 1, both excluded (★★☆)


```python
Z = np.linspace(0,1,11,endpoint=False)[1:]
print(Z)
```

#### 40. Create a random vector of size 10 and sort it (★★☆)


```python
Z = np.random.random(10)
Z.sort()
print(Z)
```

