# NumPy Hands-On Guide

A practical, example-driven guide to numerical computing with **NumPy** — the foundation of scientific Python.

---

## Table of Contents

- [Getting Started](#getting-started)
- [1. Creating Arrays](#1-creating-arrays)
- [2. Array Properties](#2-array-properties)
- [3. Indexing & Slicing](#3-indexing--slicing)
- [4. Reshaping & Stacking](#4-reshaping--stacking)
- [5. Math Operations](#5-math-operations)
- [6. Broadcasting](#6-broadcasting)
- [7. Aggregation & Statistics](#7-aggregation--statistics)
- [8. Boolean Masking & Fancy Indexing](#8-boolean-masking--fancy-indexing)
- [9. Linear Algebra](#9-linear-algebra)
- [10. Random Number Generation](#10-random-number-generation)
- [11. Sorting & Searching](#11-sorting--searching)
- [12. Performance Tips](#12-performance-tips)
- [Practice Exercises](#practice-exercises)

---

## Getting Started

### Create a Virtual Environment

```bash
# Create a virtual environment
python -m venv venv

# If `python` doesn't work, try `python3`
python3 -m venv venv

# Activate the virtual environment
# On macOS/Linux:
source venv/bin/activate

# On Windows:
venv\Scripts\activate
```

### Install Dependencies

```bash
pip install numpy
```

### Verify Installation

```python
import numpy as np

print(np.__version__)
```

### Why NumPy Over Python Lists?

```python
import time

# Python list — slow
python_list = list(range(1_000_000))
start = time.time()
result = [x * 2 for x in python_list]
print(f"Python list: {time.time() - start:.4f}s")

# NumPy array — fast
np_array = np.arange(1_000_000)
start = time.time()
result = np_array * 2
print(f"NumPy array: {time.time() - start:.4f}s")

# NumPy is typically 10-100x faster for numerical operations
```

---

## 1. Creating Arrays

### From Python Lists

```python
# 1D array
a = np.array([1, 2, 3, 4, 5])
print(a)  # [1 2 3 4 5]

# 2D array (matrix)
b = np.array([[1, 2, 3],
              [4, 5, 6]])
print(b)
# [[1 2 3]
#  [4 5 6]]

# 3D array
c = np.array([[[1, 2], [3, 4]],
              [[5, 6], [7, 8]]])
print(c.shape)  # (2, 2, 2)
```

### Built-in Constructors

```python
# Zeros and ones
np.zeros((3, 4))          # 3×4 matrix of zeros
np.ones((2, 3))           # 2×3 matrix of ones
np.full((3, 3), 7)        # 3×3 matrix filled with 7

# Identity matrix
np.eye(4)                 # 4×4 identity matrix

# Empty (uninitialized — fast but contains garbage values)
np.empty((2, 3))

# Range
np.arange(0, 10, 2)       # [0, 2, 4, 6, 8]
np.linspace(0, 1, 5)      # [0.0, 0.25, 0.5, 0.75, 1.0]  (5 evenly spaced)
np.logspace(0, 3, 4)      # [1, 10, 100, 1000]  (log scale)
```

### Specifying Data Types

```python
a = np.array([1, 2, 3], dtype=np.float32)
b = np.zeros((3, 3), dtype=np.int64)
c = np.array([True, False, True], dtype=np.bool_)

# Common dtypes:
# np.int32, np.int64, np.float32, np.float64
# np.bool_, np.complex128, np.str_
```

### From Existing Data

```python
# Copy an array
b = np.copy(a)
b = a.copy()

# Create from a function
np.fromfunction(lambda i, j: i + j, (3, 3))
# [[0, 1, 2],
#  [1, 2, 3],
#  [2, 3, 4]]
```

---

## 2. Array Properties

```python
a = np.array([[1, 2, 3, 4],
              [5, 6, 7, 8],
              [9, 10, 11, 12]])

a.ndim       # 2          (number of dimensions)
a.shape      # (3, 4)     (rows, columns)
a.size       # 12         (total elements)
a.dtype      # int64      (data type)
a.itemsize   # 8          (bytes per element)
a.nbytes     # 96         (total bytes = size × itemsize)
```

### Quick Visual

```
a = [[1,  2,  3,  4],     shape: (3, 4)
     [5,  6,  7,  8],     ndim:  2
     [9, 10, 11, 12]]     size:  12

     ↑ axis 0 (rows)
     → axis 1 (columns)
```

---

## 3. Indexing & Slicing

### 1D Indexing

```python
a = np.array([10, 20, 30, 40, 50])

a[0]      # 10     (first element)
a[-1]     # 50     (last element)
a[1:4]    # [20, 30, 40]  (slice)
a[::2]    # [10, 30, 50]  (every other element)
a[::-1]   # [50, 40, 30, 20, 10]  (reversed)
```

### 2D Indexing

```python
b = np.array([[1, 2, 3],
              [4, 5, 6],
              [7, 8, 9]])

b[0, 0]       # 1      (row 0, col 0)
b[1, 2]       # 6      (row 1, col 2)
b[0]          # [1, 2, 3]  (entire row 0)
b[:, 1]       # [2, 5, 8]  (entire column 1)
b[0:2, 1:3]   # [[2, 3], [5, 6]]  (sub-matrix)
b[-1, :]      # [7, 8, 9]  (last row)
```

### Important: Views vs Copies

```python
a = np.array([1, 2, 3, 4, 5])

# Slicing creates a VIEW (shared memory!)
view = a[1:4]
view[0] = 99
print(a)  # [1, 99, 3, 4, 5]  ← original changed!

# Explicit copy
safe_copy = a[1:4].copy()
safe_copy[0] = 0
print(a)  # [1, 99, 3, 4, 5]  ← original unchanged
```

---

## 4. Reshaping & Stacking

### Reshape

```python
a = np.arange(12)  # [0, 1, 2, ..., 11]

# Reshape to 3×4
b = a.reshape(3, 4)
# [[ 0,  1,  2,  3],
#  [ 4,  5,  6,  7],
#  [ 8,  9, 10, 11]]

# Use -1 to auto-calculate one dimension
c = a.reshape(2, -1)   # 2×6
d = a.reshape(-1, 3)   # 4×3

# Flatten back to 1D
b.ravel()    # returns a view (fast)
b.flatten()  # returns a copy (safe)
```

### Transpose

```python
a = np.array([[1, 2, 3],
              [4, 5, 6]])

a.T
# [[1, 4],
#  [2, 5],
#  [3, 6]]

# For higher dimensions
np.transpose(a, axes=(1, 0))  # same as .T for 2D
```

### Adding / Removing Dimensions

```python
a = np.array([1, 2, 3])  # shape: (3,)

# Add dimension
row = a[np.newaxis, :]     # shape: (1, 3)  — row vector
col = a[:, np.newaxis]     # shape: (3, 1)  — column vector
row = np.expand_dims(a, axis=0)  # same as above

# Remove dimension
b = np.array([[1, 2, 3]])  # shape: (1, 3)
np.squeeze(b)               # shape: (3,)
```

### Stacking

```python
a = np.array([1, 2, 3])
b = np.array([4, 5, 6])

# Vertical stack (row-wise)
np.vstack([a, b])
# [[1, 2, 3],
#  [4, 5, 6]]

# Horizontal stack (column-wise)
np.hstack([a, b])
# [1, 2, 3, 4, 5, 6]

# Column stack (adds as columns)
np.column_stack([a, b])
# [[1, 4],
#  [2, 5],
#  [3, 6]]

# General concatenation
np.concatenate([a, b], axis=0)  # same as hstack for 1D
```

### Splitting

```python
a = np.arange(12).reshape(3, 4)

# Split into 2 along columns
np.hsplit(a, 2)    # two 3×2 arrays

# Split into 3 along rows
np.vsplit(a, 3)    # three 1×4 arrays

# Split at specific indices
np.split(a, [1, 3], axis=1)  # split at col 1 and col 3
```

---

## 5. Math Operations

### Element-wise Operations

```python
a = np.array([1, 2, 3, 4])
b = np.array([10, 20, 30, 40])

a + b       # [11, 22, 33, 44]
a - b       # [-9, -18, -27, -36]
a * b       # [10, 40, 90, 160]
a / b       # [0.1, 0.1, 0.1, 0.1]
a ** 2      # [1, 4, 9, 16]
a % 2       # [1, 0, 1, 0]
np.sqrt(a)  # [1.0, 1.414, 1.732, 2.0]
np.exp(a)   # [2.718, 7.389, 20.086, 54.598]
np.log(a)   # [0.0, 0.693, 1.099, 1.386]
np.abs(a)   # absolute value
np.round(a / 3, 2)  # round to 2 decimal places
```

### Comparison Operations (return boolean arrays)

```python
a = np.array([1, 5, 3, 8, 2])

a > 3          # [False, True, False, True, False]
a == 5         # [False, True, False, False, False]
a != 3         # [True, True, False, True, True]
(a > 2) & (a < 6)   # [False, True, True, False, False]
(a < 2) | (a > 7)   # [True, False, False, True, False]
```

### Useful Math Functions

```python
np.sin(a)           # trigonometric
np.cos(a)
np.tan(a)
np.pi               # 3.14159...
np.e                # 2.71828...

np.ceil(2.3)        # 3.0
np.floor(2.7)       # 2.0
np.clip(a, 2, 6)    # clip values to [2, 6]

np.maximum(a, b)    # element-wise max
np.minimum(a, b)    # element-wise min
```

---

## 6. Broadcasting

Broadcasting lets NumPy operate on arrays of different shapes. It's one of the most powerful (and confusing) features.

### Rules

1. If arrays have different `ndim`, pad the smaller shape with 1s on the left
2. Dimensions of size 1 are stretched to match the other array
3. If dimensions differ and neither is 1, raise an error

### Examples

```python
# Scalar + Array
a = np.array([1, 2, 3])
a + 10  # [11, 12, 13]  — scalar broadcasts to [10, 10, 10]

# 1D + 2D
a = np.array([1, 2, 3])           # shape: (3,)
b = np.array([[10], [20], [30]])   # shape: (3, 1)
a + b
# [[11, 12, 13],
#  [21, 22, 23],
#  [31, 32, 33]]
```

### Visual Broadcast Example

```
a:     [1, 2, 3]          shape (3,) → treated as (1, 3)
b:     [[10],              shape (3, 1)
        [20],
        [30]]

Step 1: a gets broadcast to    [[1, 2, 3],
                                [1, 2, 3],
                                [1, 2, 3]]

Step 2: b gets broadcast to    [[10, 10, 10],
                                [20, 20, 20],
                                [30, 30, 30]]

Result: a + b =                [[11, 12, 13],
                                [21, 22, 23],
                                [31, 32, 33]]
```

### Practical Use: Normalize Columns

```python
data = np.array([[1, 100],
                 [2, 200],
                 [3, 300]])

# Mean and std per column
mean = data.mean(axis=0)    # [2.0, 200.0]
std = data.std(axis=0)      # [0.816, 81.649]

# Standardize (z-score) — broadcasting handles shape mismatch
normalized = (data - mean) / std
```

---

## 7. Aggregation & Statistics

```python
a = np.array([[1, 2, 3],
              [4, 5, 6],
              [7, 8, 9]])

# Global aggregations
np.sum(a)       # 45
np.mean(a)      # 5.0
np.std(a)       # 2.582
np.var(a)       # 6.667
np.min(a)       # 1
np.max(a)       # 9
np.median(a)    # 5.0

# Along an axis
np.sum(a, axis=0)    # [12, 15, 18]  (column sums)
np.sum(a, axis=1)    # [6, 15, 24]   (row sums)
np.mean(a, axis=0)   # [4, 5, 6]     (column means)
np.mean(a, axis=1)   # [2, 5, 8]     (row means)
```

### Axis Visual Reference

```
         axis=1 →
         col0  col1  col2
axis=0 ↓ [  1,    2,    3 ]  → sum(axis=1) = 6
         [  4,    5,    6 ]  → sum(axis=1) = 15
         [  7,    8,    9 ]  → sum(axis=1) = 24
          ↓     ↓     ↓
          12    15    18      ← sum(axis=0)
```

### Cumulative Operations

```python
np.cumsum(a, axis=0)     # cumulative sum down rows
np.cumprod(a, axis=1)    # cumulative product across columns
```

### Finding Positions

```python
np.argmin(a)        # 0   (index of min in flattened array)
np.argmax(a)        # 8   (index of max in flattened array)
np.argmin(a, axis=0)  # [0, 0, 0]  (row index of min per column)
np.argmax(a, axis=1)  # [2, 2, 2]  (col index of max per row)

# Unravel flat index to multi-dim index
np.unravel_index(np.argmax(a), a.shape)  # (2, 2)
```

### Percentiles & Quantiles

```python
data = np.random.randn(1000)

np.percentile(data, 25)     # 25th percentile (Q1)
np.percentile(data, 50)     # 50th percentile (median)
np.percentile(data, 75)     # 75th percentile (Q3)
np.percentile(data, [25, 50, 75])  # all three at once

np.quantile(data, 0.95)     # 95th percentile
```

### Correlation & Covariance

```python
x = np.array([1, 2, 3, 4, 5])
y = np.array([2, 4, 5, 4, 5])

np.corrcoef(x, y)      # correlation matrix
np.cov(x, y)           # covariance matrix
```

---

## 8. Boolean Masking & Fancy Indexing

### Boolean Masking

```python
a = np.array([10, 25, 30, 5, 45, 15])

# Create a mask
mask = a > 20
print(mask)  # [False, True, True, False, True, False]

# Apply the mask (selects True elements)
a[mask]      # [25, 30, 45]

# Direct filtering
a[a > 20]    # [25, 30, 45]

# Combined conditions
a[(a > 10) & (a < 40)]  # [25, 30, 15]

# Count matches
np.sum(a > 20)   # 3
np.any(a > 40)   # True
np.all(a > 0)    # True
```

### Where (conditional selection)

```python
a = np.array([1, -2, 3, -4, 5])

# Replace negatives with 0
np.where(a > 0, a, 0)  # [1, 0, 3, 0, 5]

# Categorize
labels = np.where(a > 0, 'positive', 'negative')
# ['positive', 'negative', 'positive', 'negative', 'positive']

# Find indices where condition is true
np.where(a > 0)  # (array([0, 2, 4]),)
```

### Fancy Indexing (indexing with arrays)

```python
a = np.array([10, 20, 30, 40, 50])

# Select specific indices
indices = [0, 2, 4]
a[indices]  # [10, 30, 50]

# 2D fancy indexing
b = np.array([[1, 2, 3],
              [4, 5, 6],
              [7, 8, 9]])

rows = [0, 2]
cols = [1, 2]
b[rows, cols]  # [2, 9]  (elements at (0,1) and (2,2))

# Select specific rows
b[[0, 2]]     # [[1, 2, 3], [7, 8, 9]]
```

### Practical: Replace Outliers

```python
data = np.array([10, 12, 11, 500, 13, 9, 11, -100, 12])

# Define bounds
lower = np.percentile(data, 5)
upper = np.percentile(data, 95)

# Clip outliers
cleaned = np.clip(data, lower, upper)
print(cleaned)
```

---

## 9. Linear Algebra

```python
# Matrix multiplication
A = np.array([[1, 2], [3, 4]])
B = np.array([[5, 6], [7, 8]])

A @ B            # matrix multiply (preferred syntax)
np.dot(A, B)     # same thing

# Dot product of vectors
a = np.array([1, 2, 3])
b = np.array([4, 5, 6])
np.dot(a, b)     # 32  (1*4 + 2*5 + 3*6)
```

### Common Operations

```python
from numpy.linalg import inv, det, eig, norm, solve

A = np.array([[1, 2], [3, 4]])

inv(A)           # inverse
det(A)           # determinant (-2.0)
norm(A)          # Frobenius norm
np.trace(A)      # trace (sum of diagonal) = 5

# Eigenvalues and eigenvectors
eigenvalues, eigenvectors = eig(A)
```

### Solving Linear Systems

```python
# Solve: Ax = b
# 2x + 3y = 8
# 4x + 1y = 7

A = np.array([[2, 3], [4, 1]])
b = np.array([8, 7])

x = solve(A, b)
print(x)  # [1.3, 1.8]  → x=1.3, y=1.8

# Verify
print(A @ x)  # [8, 7] ✓
```

### SVD (Singular Value Decomposition)

```python
A = np.array([[1, 2], [3, 4], [5, 6]])
U, S, Vt = np.linalg.svd(A)

# Reconstruct (for verification)
S_full = np.zeros_like(A, dtype=float)
np.fill_diagonal(S_full, S)
reconstructed = U @ S_full @ Vt
np.allclose(A, reconstructed)  # True
```

---

## 10. Random Number Generation

### Modern API (recommended)

```python
rng = np.random.default_rng(seed=42)  # reproducible

# Uniform [0, 1)
rng.random(5)           # 5 random floats
rng.random((3, 4))      # 3×4 matrix

# Uniform [low, high)
rng.uniform(10, 20, size=5)

# Integer [low, high)
rng.integers(1, 7, size=10)   # dice rolls

# Normal distribution (mean=0, std=1)
rng.standard_normal(1000)

# Normal with specific mean and std
rng.normal(loc=100, scale=15, size=1000)  # IQ scores
```

### Common Distributions

```python
rng = np.random.default_rng(42)

rng.binomial(n=10, p=0.5, size=100)          # coin flips
rng.poisson(lam=5, size=100)                  # event counts
rng.exponential(scale=2.0, size=100)          # wait times
rng.choice([1, 2, 3, 4, 5], size=3)          # random sample
rng.choice(10, size=5, replace=False)         # sample without replacement
```

### Shuffling & Permutation

```python
a = np.array([1, 2, 3, 4, 5])

# Shuffle in-place
rng.shuffle(a)
print(a)  # [3, 1, 5, 2, 4]  (random order)

# Permutation (returns new array, original unchanged)
perm = rng.permutation(a)
```

### Practical: Train/Test Split

```python
data = np.arange(100).reshape(20, 5)  # 20 samples, 5 features
labels = np.random.randint(0, 2, 20)

# Create random indices
indices = rng.permutation(len(data))
split = int(0.8 * len(data))

train_idx = indices[:split]
test_idx = indices[split:]

X_train, X_test = data[train_idx], data[test_idx]
y_train, y_test = labels[train_idx], labels[test_idx]

print(f"Train: {X_train.shape}, Test: {X_test.shape}")
# Train: (16, 5), Test: (4, 5)
```

---

## 11. Sorting & Searching

### Sorting

```python
a = np.array([3, 1, 4, 1, 5, 9, 2, 6])

np.sort(a)       # [1, 1, 2, 3, 4, 5, 6, 9]  (returns sorted copy)
a.sort()         # sorts in-place

# Sort indices (argsort)
indices = np.argsort(a)  # indices that would sort the array
a[indices]               # sorted array

# Sort 2D along axis
b = np.array([[3, 1, 2],
              [6, 4, 5]])
np.sort(b, axis=0)  # sort each column
np.sort(b, axis=1)  # sort each row

# Partial sort (k smallest)
np.partition(a, 3)  # first 3 elements are the 3 smallest (unordered)
```

### Searching

```python
a = np.array([10, 20, 30, 40, 50])

# Find index of value
np.searchsorted(a, 25)    # 2 (where 25 would be inserted)

# Find indices of non-zero elements
b = np.array([0, 5, 0, 3, 0, 7])
np.nonzero(b)              # (array([1, 3, 5]),)
np.flatnonzero(b)          # [1, 3, 5]

# Unique values
c = np.array([3, 1, 2, 1, 3, 2, 3])
np.unique(c)                        # [1, 2, 3]
np.unique(c, return_counts=True)    # ([1, 2, 3], [2, 2, 3])
np.unique(c, return_index=True)     # ([1, 2, 3], [1, 2, 0])
```

---

## 12. Performance Tips

### Use Vectorized Operations (avoid loops)

```python
# BAD: Python loop
result = np.zeros(len(a))
for i in range(len(a)):
    result[i] = a[i] ** 2 + b[i]

# GOOD: vectorized
result = a ** 2 + b
```

### Pre-allocate Arrays

```python
# BAD: growing list then converting
results = []
for i in range(10000):
    results.append(i ** 2)
arr = np.array(results)

# GOOD: pre-allocate
arr = np.empty(10000)
for i in range(10000):
    arr[i] = i ** 2

# BEST: vectorized
arr = np.arange(10000) ** 2
```

### Use In-place Operations

```python
a = np.ones(1_000_000)

# Creates a new array (uses extra memory)
a = a + 1

# Modifies in-place (no extra memory)
a += 1
np.add(a, 1, out=a)
```

### Use Views Instead of Copies

```python
# Reshape and transpose return views (fast, no copy)
b = a.reshape(100, 100)   # view — no memory allocation
c = b.T                    # view — no memory allocation

# Check if it's a view
b.base is a  # True → it's a view
```

### Memory Layout (C vs Fortran order)

```python
# Row-major (C order) — default, fast for row access
a = np.array([[1, 2, 3], [4, 5, 6]], order='C')

# Column-major (Fortran order) — fast for column access
b = np.array([[1, 2, 3], [4, 5, 6]], order='F')

# Check order
a.flags['C_CONTIGUOUS']  # True
b.flags['F_CONTIGUOUS']  # True
```

---

## Practice Exercises

### Exercise 1: Array Gymnastics

> Create a 6×6 matrix of random integers (1-100). Extract the 2×2 sub-matrix from the center. Replace all values > 80 with 80 (cap). Calculate the mean of each row and column.

```python
rng = np.random.default_rng(42)
# Your solution here
```

### Exercise 2: Broadcasting Challenge

> Given a 5×3 matrix of student scores across 3 subjects, normalize each subject (column) to have mean 0 and std 1 using broadcasting.

```python
scores = np.array([[85, 90, 78],
                   [92, 88, 95],
                   [76, 82, 89],
                   [88, 79, 91],
                   [95, 85, 84]])
# Your solution here
# Hint: mean(axis=0) and std(axis=0)
```

### Exercise 3: Image as Array

> Create a 100×100 "image" (2D array) that is black (0) with a white (255) border of width 5 pixels. Then add a white diagonal line from top-left to bottom-right.

```python
# Your solution here
# Hint: slicing for borders, np.arange for diagonal
```

### Exercise 4: Monte Carlo Pi

> Estimate π by generating 1 million random (x, y) points in a unit square and calculating what fraction falls inside a quarter circle (x² + y² ≤ 1).

```python
rng = np.random.default_rng(42)
# Your solution here
# Hint: π ≈ 4 × (points inside circle / total points)
```

### Exercise 5: Moving Average

> Given an array of 100 daily stock prices, compute a 7-day moving average using only NumPy (no pandas).

```python
prices = np.cumsum(np.random.randn(100)) + 100
# Your solution here
# Hint: np.convolve or cumsum trick
```

---

## Quick Reference Cheat Sheet

| Task | Code |
|------|------|
| Create zeros | `np.zeros((m, n))` |
| Create ones | `np.ones((m, n))` |
| Create range | `np.arange(start, stop, step)` |
| Evenly spaced | `np.linspace(start, stop, num)` |
| Shape | `a.shape` |
| Reshape | `a.reshape(m, n)` |
| Transpose | `a.T` |
| Flatten | `a.ravel()` |
| Element-wise multiply | `a * b` |
| Matrix multiply | `a @ b` |
| Sum | `np.sum(a, axis=0)` |
| Mean | `np.mean(a, axis=0)` |
| Std Dev | `np.std(a, axis=0)` |
| Min / Max | `np.min(a)` / `np.max(a)` |
| Argmin / Argmax | `np.argmin(a)` / `np.argmax(a)` |
| Sort | `np.sort(a)` |
| Unique | `np.unique(a)` |
| Where | `np.where(cond, a, b)` |
| Clip | `np.clip(a, low, high)` |
| Stack vertically | `np.vstack([a, b])` |
| Stack horizontally | `np.hstack([a, b])` |
| Random floats | `rng.random((m, n))` |
| Random ints | `rng.integers(low, high, size)` |
| Normal dist | `rng.normal(mean, std, size)` |
| Inverse | `np.linalg.inv(A)` |
| Solve Ax=b | `np.linalg.solve(A, b)` |

---

*Part of the [Data Handling and Processing](../) module. See also: [Pandas Hands-On](../pandas-hands-on/) | [Matplotlib Hands-On](../matplotlib-hands-on/)*
