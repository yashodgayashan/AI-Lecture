# Pandas Hands-On Guide

A practical, example-driven guide to data manipulation with **pandas** — the backbone of data handling in Python.

---

## Table of Contents

- [Getting Started](#getting-started)
- [1. Creating DataFrames](#1-creating-dataframes)
- [2. Reading & Writing Data](#2-reading--writing-data)
- [3. Inspecting Data](#3-inspecting-data)
- [4. Selecting & Filtering](#4-selecting--filtering)
- [5. Handling Missing Data](#5-handling-missing-data)
- [6. Data Transformation](#6-data-transformation)
- [7. Grouping & Aggregation](#7-grouping--aggregation)
- [8. Merging & Joining](#8-merging--joining)
- [9. String Operations](#9-string-operations)
- [10. Date & Time](#10-date--time)
- [11. Pivot Tables & Reshaping](#11-pivot-tables--reshaping)
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
pip install pandas numpy
```

### Verify Installation

```python
import pandas as pd
import numpy as np

print(pd.__version__)
```

---

## 1. Creating DataFrames

### From a Dictionary

```python
data = {
    'name': ['Alice', 'Bob', 'Charlie', 'Diana'],
    'age': [25, 30, 35, 28],
    'city': ['London', 'Paris', 'Berlin', 'Tokyo'],
    'salary': [50000, 62000, 58000, 71000]
}

df = pd.DataFrame(data)
print(df)
```

**Output:**

| | name | age | city | salary |
|---|------|-----|--------|--------|
| 0 | Alice | 25 | London | 50000 |
| 1 | Bob | 30 | Paris | 62000 |
| 2 | Charlie | 35 | Berlin | 58000 |
| 3 | Diana | 28 | Tokyo | 71000 |

### From a List of Lists

```python
data = [
    ['Alice', 25, 'London'],
    ['Bob', 30, 'Paris'],
    ['Charlie', 35, 'Berlin']
]

df = pd.DataFrame(data, columns=['name', 'age', 'city'])
```

### Creating a Series

```python
ages = pd.Series([25, 30, 35, 28], name='age')
print(ages)
# 0    25
# 1    30
# 2    35
# 3    28
# Name: age, dtype: int64
```

### With a Custom Index

```python
df = pd.DataFrame(data, index=['emp_001', 'emp_002', 'emp_003', 'emp_004'])
```

---

## 2. Reading & Writing Data

### CSV

```python
# Read
df = pd.read_csv('data.csv')
df = pd.read_csv('data.csv', sep=';', encoding='utf-8')
df = pd.read_csv('data.csv', usecols=['name', 'age'], nrows=1000)

# Write
df.to_csv('output.csv', index=False)
```

### Excel

```python
# Read
df = pd.read_excel('data.xlsx', sheet_name='Sheet1')

# Read all sheets into a dictionary
all_sheets = pd.read_excel('data.xlsx', sheet_name=None)

# Write
df.to_excel('output.xlsx', index=False, sheet_name='Results')
```

### JSON

```python
# Read
df = pd.read_json('data.json')

# Write
df.to_json('output.json', orient='records', indent=2)
```

### SQL (with SQLAlchemy)

```python
from sqlalchemy import create_engine

engine = create_engine('sqlite:///database.db')

# Read
df = pd.read_sql('SELECT * FROM users', engine)

# Write
df.to_sql('users', engine, if_exists='replace', index=False)
```

### Clipboard (quick & dirty)

```python
# Paste from spreadsheet
df = pd.read_clipboard()
```

---

## 3. Inspecting Data

```python
# Shape and size
df.shape          # (rows, columns)
len(df)           # number of rows
df.columns        # column names
df.dtypes         # data types per column

# Quick look
df.head()         # first 5 rows
df.tail(10)       # last 10 rows
df.sample(5)      # 5 random rows

# Summary statistics
df.describe()                  # numeric columns
df.describe(include='object')  # categorical columns

# Detailed info
df.info()         # dtypes, non-null counts, memory usage

# Value counts
df['city'].value_counts()
df['city'].nunique()           # number of unique values

# Missing values
df.isnull().sum()              # count per column
df.isnull().sum().sum()        # total missing
df.isnull().mean() * 100      # percentage missing
```

### Memory Optimization Check

```python
df.memory_usage(deep=True)

# Downcast numeric types to save memory
df['age'] = pd.to_numeric(df['age'], downcast='integer')
df['salary'] = pd.to_numeric(df['salary'], downcast='float')
```

---

## 4. Selecting & Filtering

### Column Selection

```python
# Single column (returns Series)
df['name']

# Multiple columns (returns DataFrame)
df[['name', 'age']]

# By position
df.iloc[:, 0]        # first column
df.iloc[:, [0, 2]]   # first and third columns
```

### Row Selection

```python
# By label
df.loc[0]                    # first row
df.loc[0:2]                  # rows 0, 1, 2 (inclusive!)

# By position
df.iloc[0]                   # first row
df.iloc[0:2]                 # rows 0, 1 (exclusive end)

# Specific cell
df.loc[0, 'name']            # 'Alice'
df.iloc[0, 0]                # 'Alice'
```

### Boolean Filtering

```python
# Single condition
df[df['age'] > 28]

# Multiple conditions (use & for AND, | for OR)
df[(df['age'] > 25) & (df['salary'] > 55000)]

# Using isin()
df[df['city'].isin(['London', 'Tokyo'])]

# String contains
df[df['name'].str.contains('li', case=False)]

# Between
df[df['age'].between(25, 30)]

# Not null
df[df['salary'].notna()]
```

### Query Method (cleaner syntax)

```python
df.query('age > 28 and salary > 55000')
df.query('city in ["London", "Tokyo"]')
df.query('name.str.contains("li")', engine='python')
```

---

## 5. Handling Missing Data

### Creating Data with Missing Values

```python
df = pd.DataFrame({
    'name': ['Alice', 'Bob', None, 'Diana', 'Eve'],
    'age': [25, np.nan, 35, 28, np.nan],
    'salary': [50000, 62000, np.nan, 71000, 55000],
    'department': ['HR', 'IT', 'IT', None, 'HR']
})
```

### Detection

```python
df.isnull()          # Boolean mask
df.isnull().sum()    # Count per column
df.notnull()         # Inverse
```

### Dropping

```python
# Drop rows with any null
df.dropna()

# Drop rows where specific columns are null
df.dropna(subset=['name', 'age'])

# Drop columns with any null
df.dropna(axis=1)

# Drop rows where ALL values are null
df.dropna(how='all')

# Keep rows with at least 3 non-null values
df.dropna(thresh=3)
```

### Filling

```python
# Fill with a constant
df['department'].fillna('Unknown')

# Fill numeric with mean/median
df['age'].fillna(df['age'].mean())
df['salary'].fillna(df['salary'].median())

# Forward fill (use previous value)
df['age'].ffill()

# Backward fill (use next value)
df['age'].bfill()

# Fill with different values per column
df.fillna({'age': df['age'].median(), 'department': 'Unknown'})

# Interpolate (linear by default)
df['salary'].interpolate()
```

### Creating a Missing Indicator

```python
df['age_was_missing'] = df['age'].isnull().astype(int)
```

---

## 6. Data Transformation

### Adding / Modifying Columns

```python
# New column from calculation
df['annual_bonus'] = df['salary'] * 0.10

# Conditional column
df['seniority'] = np.where(df['age'] > 30, 'Senior', 'Junior')

# Multiple conditions with np.select
conditions = [
    df['salary'] < 55000,
    df['salary'].between(55000, 65000),
    df['salary'] > 65000
]
choices = ['Low', 'Mid', 'High']
df['salary_band'] = np.select(conditions, choices, default='Unknown')

# Apply a function
df['name_length'] = df['name'].apply(len)

# Apply a custom function
def categorize_age(age):
    if age < 26:
        return 'Young'
    elif age < 35:
        return 'Mid'
    else:
        return 'Senior'

df['age_group'] = df['age'].apply(categorize_age)
```

### Renaming

```python
# Rename columns
df.rename(columns={'name': 'full_name', 'age': 'years'})

# Rename all columns at once
df.columns = ['full_name', 'years', 'location', 'annual_salary']

# Clean column names
df.columns = df.columns.str.lower().str.replace(' ', '_')
```

### Sorting

```python
# Sort by column
df.sort_values('salary', ascending=False)

# Sort by multiple columns
df.sort_values(['city', 'salary'], ascending=[True, False])

# Sort by index
df.sort_index()
```

### Dropping

```python
# Drop columns
df.drop(columns=['salary_band', 'name_length'])

# Drop rows by index
df.drop(index=[0, 3])

# Drop duplicates
df.drop_duplicates()
df.drop_duplicates(subset=['name', 'city'], keep='last')
```

### Type Conversion

```python
# Convert types
df['age'] = df['age'].astype(int)
df['salary'] = df['salary'].astype(float)
df['city'] = df['city'].astype('category')  # saves memory

# Parse dates
df['join_date'] = pd.to_datetime(df['join_date'])

# Handle errors
df['age'] = pd.to_numeric(df['age'], errors='coerce')  # invalid → NaN
```

### Binning

```python
# Equal-width bins
df['age_bin'] = pd.cut(df['age'], bins=3, labels=['Young', 'Mid', 'Senior'])

# Custom bins
df['salary_bin'] = pd.cut(
    df['salary'],
    bins=[0, 40000, 60000, 80000, float('inf')],
    labels=['Low', 'Mid', 'High', 'Top']
)

# Equal-frequency bins (quantiles)
df['salary_quartile'] = pd.qcut(df['salary'], q=4, labels=['Q1', 'Q2', 'Q3', 'Q4'])
```

---

## 7. Grouping & Aggregation

### Basic GroupBy

```python
df = pd.DataFrame({
    'department': ['HR', 'IT', 'IT', 'HR', 'Finance', 'Finance', 'IT'],
    'name': ['Alice', 'Bob', 'Charlie', 'Diana', 'Eve', 'Frank', 'Grace'],
    'salary': [50000, 62000, 58000, 71000, 55000, 48000, 67000],
    'age': [25, 30, 35, 28, 32, 45, 27]
})

# Single aggregation
df.groupby('department')['salary'].mean()

# Multiple aggregations
df.groupby('department')['salary'].agg(['mean', 'median', 'min', 'max', 'count'])
```

### Named Aggregation (cleaner output)

```python
df.groupby('department').agg(
    avg_salary=('salary', 'mean'),
    max_salary=('salary', 'max'),
    headcount=('name', 'count'),
    avg_age=('age', 'mean')
).round(2)
```

**Output:**

| department | avg_salary | max_salary | headcount | avg_age |
|------------|-----------|-----------|-----------|---------|
| Finance | 51500.0 | 55000 | 2 | 38.50 |
| HR | 60500.0 | 71000 | 2 | 26.50 |
| IT | 62333.3 | 67000 | 3 | 30.67 |

### Custom Aggregation Functions

```python
# Lambda
df.groupby('department')['salary'].agg(lambda x: x.max() - x.min())

# Named custom function
def salary_range(x):
    return x.max() - x.min()

df.groupby('department')['salary'].agg(salary_range)
```

### Transform (returns same-size output)

```python
# Add group mean as a new column
df['dept_avg_salary'] = df.groupby('department')['salary'].transform('mean')

# Z-score within each group
df['salary_zscore'] = df.groupby('department')['salary'].transform(
    lambda x: (x - x.mean()) / x.std()
)
```

### Filter Groups

```python
# Keep only departments with average salary > 55000
df.groupby('department').filter(lambda x: x['salary'].mean() > 55000)
```

---

## 8. Merging & Joining

### Sample DataFrames

```python
employees = pd.DataFrame({
    'emp_id': [1, 2, 3, 4, 5],
    'name': ['Alice', 'Bob', 'Charlie', 'Diana', 'Eve'],
    'dept_id': [101, 102, 101, 103, 102]
})

departments = pd.DataFrame({
    'dept_id': [101, 102, 103, 104],
    'dept_name': ['HR', 'IT', 'Finance', 'Marketing']
})
```

### Merge (SQL-style joins)

```python
# Inner join (default) — only matching rows
pd.merge(employees, departments, on='dept_id')

# Left join — all employees, matching departments
pd.merge(employees, departments, on='dept_id', how='left')

# Right join — all departments, matching employees
pd.merge(employees, departments, on='dept_id', how='right')

# Outer join — all rows from both
pd.merge(employees, departments, on='dept_id', how='outer')

# Join on different column names
pd.merge(employees, departments, left_on='dept_id', right_on='dept_id')
```

### Visual Join Reference

```
Inner:   Left ∩ Right     (only matches)
Left:    All Left + Right  (NaN where no match on right)
Right:   Left + All Right  (NaN where no match on left)
Outer:   All Left ∪ Right  (NaN where no match on either side)
```

### Concatenation

```python
# Stack vertically (same columns)
df_all = pd.concat([df_2023, df_2024], ignore_index=True)

# Stack horizontally (same index)
df_wide = pd.concat([df_names, df_scores], axis=1)
```

### Merge Indicator

```python
# See which rows matched
result = pd.merge(employees, departments, on='dept_id', how='outer', indicator=True)
print(result['_merge'].value_counts())
# both          4
# right_only    1
# left_only     0
```

---

## 9. String Operations

All string methods are accessed via `.str`:

```python
df = pd.DataFrame({
    'name': ['  Alice Smith  ', 'BOB jones', 'charlie BROWN', 'Diana Prince'],
    'email': ['alice@gmail.com', 'bob@yahoo.COM', 'CHARLIE@hotmail.com', None],
    'phone': ['123-456-7890', '(987) 654-3210', '555.123.4567', '1234567890']
})
```

### Cleaning

```python
df['name'] = df['name'].str.strip()         # remove whitespace
df['name'] = df['name'].str.title()          # Title Case
df['email'] = df['email'].str.lower()        # lowercase

# Chain operations
df['name_clean'] = df['name'].str.strip().str.title()
```

### Extracting & Splitting

```python
# Split name into first/last
df[['first_name', 'last_name']] = df['name'].str.split(' ', n=1, expand=True)

# Extract domain from email
df['domain'] = df['email'].str.split('@').str[1]

# Extract with regex
df['area_code'] = df['phone'].str.extract(r'(\d{3})')
```

### Searching

```python
df['name'].str.contains('ali', case=False)    # Boolean mask
df['name'].str.startswith('A')
df['name'].str.endswith('th')
df['email'].str.match(r'\w+@gmail\.com')      # regex match
```

### Replacing

```python
# Simple replace
df['phone'].str.replace('-', '')
df['phone'].str.replace(r'[^\d]', '', regex=True)   # digits only
```

### Useful String Methods

```python
df['name'].str.len()           # length
df['name'].str.upper()         # UPPERCASE
df['name'].str.lower()         # lowercase
df['name'].str.title()         # Title Case
df['name'].str.zfill(10)       # zero-pad
df['name'].str.slice(0, 3)     # first 3 chars
df['name'].str.count('a')      # count character
```

---

## 10. Date & Time

```python
df = pd.DataFrame({
    'order_date': ['2024-01-15', '2024-03-22', '2024-07-08', '2024-11-30'],
    'delivery_date': ['2024-01-20', '2024-03-25', '2024-07-15', '2024-12-05'],
    'amount': [150, 230, 410, 95]
})

# Convert strings to datetime
df['order_date'] = pd.to_datetime(df['order_date'])
df['delivery_date'] = pd.to_datetime(df['delivery_date'])
```

### Extracting Components

```python
df['year'] = df['order_date'].dt.year
df['month'] = df['order_date'].dt.month
df['month_name'] = df['order_date'].dt.month_name()
df['day'] = df['order_date'].dt.day
df['day_name'] = df['order_date'].dt.day_name()
df['quarter'] = df['order_date'].dt.quarter
df['week'] = df['order_date'].dt.isocalendar().week
df['is_weekend'] = df['order_date'].dt.dayofweek >= 5
```

### Date Arithmetic

```python
# Difference between dates
df['delivery_days'] = (df['delivery_date'] - df['order_date']).dt.days

# Add/subtract time
df['follow_up'] = df['order_date'] + pd.Timedelta(days=30)

# Time since a reference date
df['days_ago'] = (pd.Timestamp.now() - df['order_date']).dt.days
```

### Resampling Time Series

```python
# Create a time-indexed DataFrame
ts = pd.DataFrame({
    'date': pd.date_range('2024-01-01', periods=365, freq='D'),
    'sales': np.random.randint(100, 500, 365)
}).set_index('date')

# Resample to monthly
ts.resample('M').sum()          # monthly total
ts.resample('W').mean()         # weekly average
ts.resample('Q').agg(['sum', 'mean', 'max'])  # quarterly stats
```

### Rolling Windows

```python
ts['sales_7d_avg'] = ts['sales'].rolling(7).mean()
ts['sales_30d_avg'] = ts['sales'].rolling(30).mean()
ts['sales_cumsum'] = ts['sales'].cumsum()
```

---

## 11. Pivot Tables & Reshaping

### Sample Data

```python
sales = pd.DataFrame({
    'date': ['2024-01', '2024-01', '2024-02', '2024-02', '2024-03', '2024-03'],
    'product': ['Widget', 'Gadget', 'Widget', 'Gadget', 'Widget', 'Gadget'],
    'region': ['North', 'North', 'South', 'South', 'North', 'South'],
    'revenue': [1000, 1500, 1200, 800, 1100, 1300],
    'units': [50, 30, 60, 20, 55, 35]
})
```

### Pivot Table

```python
# Simple pivot
sales.pivot_table(values='revenue', index='date', columns='product', aggfunc='sum')

# Multiple aggregations
sales.pivot_table(
    values=['revenue', 'units'],
    index='date',
    columns='product',
    aggfunc={'revenue': 'sum', 'units': 'mean'},
    margins=True  # adds totals
)
```

### Melt (wide → long)

```python
wide_df = pd.DataFrame({
    'name': ['Alice', 'Bob'],
    'math_score': [90, 85],
    'english_score': [88, 92],
    'science_score': [95, 78]
})

long_df = wide_df.melt(
    id_vars='name',
    var_name='subject',
    value_name='score'
)
```

### Stack / Unstack

```python
# Create MultiIndex
grouped = sales.groupby(['date', 'product'])['revenue'].sum()

# Unstack: rows → columns
grouped.unstack()

# Stack: columns → rows
grouped.unstack().stack()
```

### Cross-Tabulation

```python
pd.crosstab(sales['product'], sales['region'], values=sales['revenue'], aggfunc='sum')
```

---

## 12. Performance Tips

### Use Categories for Repeated Strings

```python
# Before: 800 KB for 100K rows
df['country'] = df['country'].astype(str)

# After: ~20 KB for 100K rows
df['country'] = df['country'].astype('category')
```

### Vectorize Instead of Looping

```python
# BAD: slow loop
for i in range(len(df)):
    df.loc[i, 'bonus'] = df.loc[i, 'salary'] * 0.1

# GOOD: vectorized
df['bonus'] = df['salary'] * 0.1
```

### Use `.values` or `.to_numpy()` for Heavy Computation

```python
# Faster NumPy operations on raw arrays
arr = df['salary'].to_numpy()
result = np.where(arr > 60000, arr * 1.1, arr * 1.05)
```

### Read Only What You Need

```python
# Read specific columns and limit rows
df = pd.read_csv('huge_file.csv', usecols=['name', 'salary'], nrows=10000)

# Read in chunks
for chunk in pd.read_csv('huge_file.csv', chunksize=50000):
    process(chunk)
```

### Method Chaining

```python
# Clean, readable pipeline
result = (
    df
    .query('salary > 40000')
    .assign(bonus=lambda x: x['salary'] * 0.1)
    .groupby('department')
    .agg(avg_salary=('salary', 'mean'), total_bonus=('bonus', 'sum'))
    .sort_values('avg_salary', ascending=False)
    .round(2)
)
```

---

## Practice Exercises

### Exercise 1: Sales Analysis

> Given a CSV of daily sales, find the top 5 products by total revenue for each quarter.

```python
# Starter code
df = pd.read_csv('sales.csv')
df['date'] = pd.to_datetime(df['date'])
df['quarter'] = df['date'].dt.quarter

# Your solution here:
# Hint: groupby → sort → head
```

### Exercise 2: Data Cleaning Pipeline

> Clean a messy employee dataset: fix names (strip, title-case), parse salaries (remove $ and commas), fill missing departments with "Unassigned", drop duplicates.

```python
# Starter code
df = pd.DataFrame({
    'name': ['  john DOE ', 'Jane Smith', 'john doe', '  JANE SMITH  '],
    'salary': ['$52,000', '$61,000', '$52,000', '$61,000'],
    'department': ['IT', None, 'IT', 'HR']
})

# Your solution here:
# Hint: str.strip().str.title(), str.replace(), fillna(), drop_duplicates()
```

### Exercise 3: Merge & Analyze

> You have two DataFrames: `orders` (order_id, customer_id, amount) and `customers` (customer_id, name, city). Find the total spend per city and the city with the highest average order value.

```python
# Your solution here:
# Hint: merge → groupby → agg → sort
```

### Exercise 4: Time Series Exploration

> From a daily stock price DataFrame, calculate: 7-day moving average, 30-day rolling standard deviation, month-over-month percentage change, and cumulative returns.

```python
# Your solution here:
# Hint: rolling(), pct_change(), cumprod()
```

### Exercise 5: Pivot Report

> From a transaction log (date, category, region, amount), create a pivot table showing total amount per category (rows) per region (columns), with grand totals.

```python
# Your solution here:
# Hint: pivot_table(margins=True)
```

---

## Quick Reference Cheat Sheet

| Task | Code |
|------|------|
| Read CSV | `pd.read_csv('file.csv')` |
| First 5 rows | `df.head()` |
| Shape | `df.shape` |
| Column types | `df.dtypes` |
| Statistics | `df.describe()` |
| Missing count | `df.isnull().sum()` |
| Filter rows | `df[df['col'] > val]` |
| Sort | `df.sort_values('col')` |
| Group & aggregate | `df.groupby('col').agg(...)` |
| Merge | `pd.merge(df1, df2, on='key')` |
| Concat | `pd.concat([df1, df2])` |
| Pivot | `df.pivot_table(...)` |
| Apply function | `df['col'].apply(func)` |
| Rename columns | `df.rename(columns={...})` |
| Drop columns | `df.drop(columns=[...])` |
| Fill NaN | `df.fillna(value)` |
| To datetime | `pd.to_datetime(df['col'])` |
| Value counts | `df['col'].value_counts()` |
| Unique values | `df['col'].nunique()` |
| Save CSV | `df.to_csv('out.csv', index=False)` |

---

*Part of the [Data Handling and Processing](../) module. See also: [NumPy Hands-On](../numpy-hands-on/) | [Matplotlib Hands-On](../matplotlib-hands-on/)*
