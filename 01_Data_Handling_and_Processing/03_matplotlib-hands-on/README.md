# Matplotlib Hands-On Guide

A practical, example-driven guide to data visualization with **Matplotlib** — Python's most versatile plotting library.

---

## Table of Contents

- [Getting Started](#getting-started)
- [1. The Two Interfaces](#1-the-two-interfaces)
- [2. Line Plots](#2-line-plots)
- [3. Bar Charts](#3-bar-charts)
- [4. Scatter Plots](#4-scatter-plots)
- [5. Histograms](#5-histograms)
- [6. Box Plots & Violin Plots](#6-box-plots--violin-plots)
- [7. Pie Charts & Donut Charts](#7-pie-charts--donut-charts)
- [8. Heatmaps](#8-heatmaps)
- [9. Subplots & Layouts](#9-subplots--layouts)
- [10. Styling & Customization](#10-styling--customization)
- [11. Annotations & Text](#11-annotations--text)
- [12. Saving Figures](#12-saving-figures)
- [13. Seaborn Integration](#13-seaborn-integration)
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
pip install matplotlib numpy seaborn scipy
```

### Verify Installation

```python
import matplotlib
import matplotlib.pyplot as plt
import numpy as np

print(matplotlib.__version__)
```

### Your First Plot

```python
x = [1, 2, 3, 4, 5]
y = [2, 4, 6, 8, 10]

plt.plot(x, y)
plt.title('My First Plot')
plt.xlabel('X axis')
plt.ylabel('Y axis')
plt.show()
```

---

## 1. The Two Interfaces

Matplotlib has two ways to create plots. Understanding both is essential.

### PyPlot Interface (quick & simple)

```python
# State-based: operates on the "current" figure/axes
plt.figure(figsize=(8, 5))
plt.plot([1, 2, 3], [1, 4, 9])
plt.title('PyPlot Style')
plt.xlabel('X')
plt.ylabel('Y')
plt.show()
```

### Object-Oriented Interface (recommended for complex plots)

```python
# Explicit: you control figure and axes objects
fig, ax = plt.subplots(figsize=(8, 5))
ax.plot([1, 2, 3], [1, 4, 9])
ax.set_title('OO Style')
ax.set_xlabel('X')
ax.set_ylabel('Y')
plt.show()
```

> **Rule of thumb**: Use OO style for anything beyond quick exploration. It gives you full control and is less error-prone with multiple plots.

---

## 2. Line Plots

### Basic Line Plot

```python
x = np.linspace(0, 10, 100)
y = np.sin(x)

fig, ax = plt.subplots(figsize=(10, 5))
ax.plot(x, y)
ax.set_title('Sine Wave')
ax.set_xlabel('x')
ax.set_ylabel('sin(x)')
ax.grid(True, alpha=0.3)
plt.show()
```

### Multiple Lines

```python
x = np.linspace(0, 10, 100)

fig, ax = plt.subplots(figsize=(10, 5))
ax.plot(x, np.sin(x), label='sin(x)', color='#2563eb', linewidth=2)
ax.plot(x, np.cos(x), label='cos(x)', color='#dc2626', linewidth=2, linestyle='--')
ax.plot(x, np.sin(x) * np.cos(x), label='sin·cos', color='#16a34a', linewidth=1.5, linestyle='-.')

ax.set_title('Trigonometric Functions', fontsize=14, fontweight='bold')
ax.set_xlabel('x')
ax.set_ylabel('y')
ax.legend(loc='upper right')
ax.grid(True, alpha=0.3)
plt.tight_layout()
plt.show()
```

### Line Styles & Markers

```python
fig, ax = plt.subplots(figsize=(10, 6))

styles = [
    ('-',  'o', 'solid + circle'),
    ('--', 's', 'dashed + square'),
    ('-.',  '^', 'dash-dot + triangle'),
    (':',  'D', 'dotted + diamond'),
]

for i, (ls, marker, label) in enumerate(styles):
    y = np.random.randn(10).cumsum() + i * 3
    ax.plot(y, linestyle=ls, marker=marker, label=label, markersize=6)

ax.legend()
ax.set_title('Line Styles & Markers')
ax.grid(True, alpha=0.3)
plt.show()
```

### Common Marker Reference

| Marker | Symbol | Marker | Symbol |
|--------|--------|--------|--------|
| `'o'` | Circle | `'s'` | Square |
| `'^'` | Triangle up | `'v'` | Triangle down |
| `'D'` | Diamond | `'*'` | Star |
| `'+'` | Plus | `'x'` | X |
| `'.'` | Point | `'|'` | Vertical line |

### Fill Between

```python
x = np.linspace(0, 10, 100)
y = np.sin(x)

fig, ax = plt.subplots(figsize=(10, 5))
ax.plot(x, y, color='#2563eb', linewidth=2)
ax.fill_between(x, y, alpha=0.2, color='#2563eb')

# Conditional fill
ax.fill_between(x, y, where=(y > 0), alpha=0.3, color='green', label='Positive')
ax.fill_between(x, y, where=(y < 0), alpha=0.3, color='red', label='Negative')
ax.legend()
ax.set_title('Fill Between')
plt.show()
```

### Confidence Interval / Error Band

```python
x = np.linspace(0, 10, 50)
y = np.sin(x)
error = 0.3 + 0.2 * np.random.randn(50)

fig, ax = plt.subplots(figsize=(10, 5))
ax.plot(x, y, color='#2563eb', linewidth=2, label='Mean')
ax.fill_between(x, y - error, y + error, alpha=0.2, color='#2563eb', label='Confidence Band')
ax.legend()
ax.set_title('Line Plot with Confidence Band')
plt.show()
```

---

## 3. Bar Charts

### Vertical Bar Chart

```python
categories = ['Python', 'JavaScript', 'Java', 'C++', 'Rust']
values = [85, 78, 72, 65, 58]
colors = ['#2563eb', '#7c3aed', '#dc2626', '#d97706', '#16a34a']

fig, ax = plt.subplots(figsize=(8, 5))
bars = ax.bar(categories, values, color=colors, edgecolor='white', linewidth=1.5)

# Add value labels on bars
for bar, val in zip(bars, values):
    ax.text(bar.get_x() + bar.get_width()/2, bar.get_height() + 1,
            str(val), ha='center', va='bottom', fontweight='bold')

ax.set_title('Programming Language Popularity', fontsize=14, fontweight='bold')
ax.set_ylabel('Score')
ax.set_ylim(0, 100)
ax.spines['top'].set_visible(False)
ax.spines['right'].set_visible(False)
plt.tight_layout()
plt.show()
```

### Horizontal Bar Chart

```python
fig, ax = plt.subplots(figsize=(8, 5))
ax.barh(categories, values, color=colors, edgecolor='white')
ax.set_xlabel('Score')
ax.set_title('Language Popularity (Horizontal)')
ax.invert_yaxis()  # highest value on top
plt.tight_layout()
plt.show()
```

### Grouped Bar Chart

```python
categories = ['Q1', 'Q2', 'Q3', 'Q4']
product_a = [120, 150, 180, 200]
product_b = [90, 110, 140, 170]
product_c = [60, 80, 100, 130]

x = np.arange(len(categories))
width = 0.25

fig, ax = plt.subplots(figsize=(10, 6))
ax.bar(x - width, product_a, width, label='Product A', color='#2563eb')
ax.bar(x, product_b, width, label='Product B', color='#dc2626')
ax.bar(x + width, product_c, width, label='Product C', color='#16a34a')

ax.set_xticks(x)
ax.set_xticklabels(categories)
ax.set_ylabel('Revenue ($K)')
ax.set_title('Quarterly Revenue by Product')
ax.legend()
ax.spines['top'].set_visible(False)
ax.spines['right'].set_visible(False)
plt.tight_layout()
plt.show()
```

### Stacked Bar Chart

```python
fig, ax = plt.subplots(figsize=(10, 6))
ax.bar(categories, product_a, label='Product A', color='#2563eb')
ax.bar(categories, product_b, bottom=product_a, label='Product B', color='#dc2626')
bottom_c = [a + b for a, b in zip(product_a, product_b)]
ax.bar(categories, product_c, bottom=bottom_c, label='Product C', color='#16a34a')

ax.set_ylabel('Revenue ($K)')
ax.set_title('Stacked Quarterly Revenue')
ax.legend()
plt.tight_layout()
plt.show()
```

---

## 4. Scatter Plots

### Basic Scatter Plot

```python
np.random.seed(42)
x = np.random.randn(100)
y = 2 * x + np.random.randn(100) * 0.5

fig, ax = plt.subplots(figsize=(8, 6))
ax.scatter(x, y, alpha=0.6, edgecolors='white', linewidth=0.5)
ax.set_title('Basic Scatter Plot')
ax.set_xlabel('X')
ax.set_ylabel('Y')
ax.grid(True, alpha=0.3)
plt.show()
```

### Scatter with Size & Color (Bubble Chart)

```python
np.random.seed(42)
n = 50
x = np.random.randn(n)
y = np.random.randn(n)
sizes = np.random.uniform(50, 500, n)      # bubble size
colors = np.random.uniform(0, 100, n)       # color intensity

fig, ax = plt.subplots(figsize=(10, 7))
scatter = ax.scatter(x, y, s=sizes, c=colors, cmap='viridis',
                      alpha=0.7, edgecolors='white', linewidth=1)

# Add colorbar
cbar = plt.colorbar(scatter, ax=ax)
cbar.set_label('Score')

ax.set_title('Bubble Chart', fontsize=14, fontweight='bold')
ax.set_xlabel('Feature 1')
ax.set_ylabel('Feature 2')
ax.grid(True, alpha=0.3)
plt.tight_layout()
plt.show()
```

### Scatter with Regression Line

```python
np.random.seed(42)
x = np.random.randn(80)
y = 1.5 * x + np.random.randn(80) * 0.8

# Fit a line
coeffs = np.polyfit(x, y, 1)
x_line = np.linspace(x.min(), x.max(), 100)
y_line = np.polyval(coeffs, x_line)

fig, ax = plt.subplots(figsize=(8, 6))
ax.scatter(x, y, alpha=0.6, color='#2563eb', edgecolors='white', label='Data')
ax.plot(x_line, y_line, color='#dc2626', linewidth=2,
        label=f'Fit: y = {coeffs[0]:.2f}x + {coeffs[1]:.2f}')
ax.legend()
ax.set_title('Scatter with Regression Line')
ax.grid(True, alpha=0.3)
plt.show()
```

### Scatter by Category

```python
np.random.seed(42)
groups = {'Group A': (np.random.randn(30), np.random.randn(30), '#2563eb'),
          'Group B': (np.random.randn(30) + 2, np.random.randn(30) + 1, '#dc2626'),
          'Group C': (np.random.randn(30) - 1, np.random.randn(30) + 2, '#16a34a')}

fig, ax = plt.subplots(figsize=(8, 6))
for name, (x, y, color) in groups.items():
    ax.scatter(x, y, c=color, label=name, alpha=0.6, edgecolors='white', s=60)

ax.legend()
ax.set_title('Scatter by Category')
ax.grid(True, alpha=0.3)
plt.show()
```

---

## 5. Histograms

### Basic Histogram

```python
np.random.seed(42)
data = np.random.randn(1000)

fig, ax = plt.subplots(figsize=(8, 5))
ax.hist(data, bins=30, color='#2563eb', edgecolor='white', alpha=0.7)
ax.set_title('Normal Distribution (n=1000)')
ax.set_xlabel('Value')
ax.set_ylabel('Frequency')
ax.axvline(data.mean(), color='red', linestyle='--', label=f'Mean: {data.mean():.2f}')
ax.legend()
plt.tight_layout()
plt.show()
```

### Overlapping Histograms

```python
np.random.seed(42)
data1 = np.random.normal(0, 1, 1000)
data2 = np.random.normal(2, 1.5, 1000)

fig, ax = plt.subplots(figsize=(10, 5))
ax.hist(data1, bins=40, alpha=0.5, color='#2563eb', label='Group A (μ=0)')
ax.hist(data2, bins=40, alpha=0.5, color='#dc2626', label='Group B (μ=2)')
ax.legend()
ax.set_title('Overlapping Distributions')
ax.set_xlabel('Value')
ax.set_ylabel('Frequency')
plt.tight_layout()
plt.show()
```

### Histogram Types

```python
fig, axes = plt.subplots(1, 3, figsize=(15, 4))
data = np.random.randn(500)

# Standard
axes[0].hist(data, bins=25, color='#2563eb', edgecolor='white')
axes[0].set_title('Standard')

# Density (normalized)
axes[1].hist(data, bins=25, density=True, color='#16a34a', edgecolor='white')
axes[1].set_title('Density (normalized)')

# Cumulative
axes[2].hist(data, bins=25, cumulative=True, color='#d97706', edgecolor='white')
axes[2].set_title('Cumulative')

plt.tight_layout()
plt.show()
```

### 2D Histogram (Hexbin)

```python
np.random.seed(42)
x = np.random.randn(5000)
y = x * 0.5 + np.random.randn(5000) * 0.5

fig, ax = plt.subplots(figsize=(8, 6))
hb = ax.hexbin(x, y, gridsize=25, cmap='Blues', mincnt=1)
plt.colorbar(hb, ax=ax, label='Count')
ax.set_title('2D Hexbin Plot')
ax.set_xlabel('X')
ax.set_ylabel('Y')
plt.tight_layout()
plt.show()
```

---

## 6. Box Plots & Violin Plots

### Box Plot

```python
np.random.seed(42)
data = [np.random.normal(loc, 1, 200) for loc in [0, 2, 1, 3]]

fig, ax = plt.subplots(figsize=(8, 5))
bp = ax.boxplot(data, labels=['A', 'B', 'C', 'D'], patch_artist=True,
                boxprops=dict(facecolor='#dbeafe', edgecolor='#2563eb'),
                medianprops=dict(color='#dc2626', linewidth=2),
                whiskerprops=dict(color='#2563eb'),
                capprops=dict(color='#2563eb'),
                flierprops=dict(marker='o', markersize=4, alpha=0.5))

ax.set_title('Box Plot Comparison')
ax.set_ylabel('Value')
ax.grid(True, axis='y', alpha=0.3)
plt.tight_layout()
plt.show()
```

### Box Plot Anatomy

```
        ┬─── max whisker (Q3 + 1.5 × IQR, or max data point)
        │
   ┌────┤
   │    │    ← Q3 (75th percentile)
   │  ──┤    ← Median (50th percentile)
   │    │    ← Q1 (25th percentile)
   └────┤
        │
        ┴─── min whisker (Q1 - 1.5 × IQR, or min data point)

   ○         ← outlier (beyond whiskers)
```

### Violin Plot

```python
fig, ax = plt.subplots(figsize=(8, 5))
vp = ax.violinplot(data, showmeans=True, showmedians=True)

# Customize colors
for body in vp['bodies']:
    body.set_facecolor('#dbeafe')
    body.set_edgecolor('#2563eb')
    body.set_alpha(0.7)

ax.set_xticks([1, 2, 3, 4])
ax.set_xticklabels(['A', 'B', 'C', 'D'])
ax.set_title('Violin Plot')
ax.set_ylabel('Value')
plt.tight_layout()
plt.show()
```

### Side-by-Side Box + Strip

```python
fig, ax = plt.subplots(figsize=(8, 5))

# Box plot (no outlier markers)
bp = ax.boxplot(data, labels=['A', 'B', 'C', 'D'],
                patch_artist=True, showfliers=False,
                boxprops=dict(facecolor='#dbeafe', edgecolor='#2563eb'))

# Overlay individual points (jittered)
for i, d in enumerate(data):
    jitter = np.random.normal(0, 0.04, len(d))
    ax.scatter(np.full_like(d, i + 1) + jitter, d,
               alpha=0.2, s=10, color='#2563eb')

ax.set_title('Box Plot + Strip Plot')
ax.set_ylabel('Value')
plt.tight_layout()
plt.show()
```

---

## 7. Pie Charts & Donut Charts

### Pie Chart

```python
labels = ['Python', 'JavaScript', 'Java', 'C++', 'Other']
sizes = [35, 25, 20, 12, 8]
colors = ['#2563eb', '#7c3aed', '#dc2626', '#d97706', '#6b7280']
explode = (0.05, 0, 0, 0, 0)  # pull out first slice

fig, ax = plt.subplots(figsize=(8, 8))
wedges, texts, autotexts = ax.pie(
    sizes, explode=explode, labels=labels, colors=colors,
    autopct='%1.1f%%', startangle=90,
    textprops={'fontsize': 12}
)

# Style percentage text
for autotext in autotexts:
    autotext.set_color('white')
    autotext.set_fontweight('bold')

ax.set_title('Language Usage Share', fontsize=14, fontweight='bold', pad=20)
plt.show()
```

### Donut Chart

```python
fig, ax = plt.subplots(figsize=(8, 8))
wedges, texts, autotexts = ax.pie(
    sizes, labels=labels, colors=colors,
    autopct='%1.1f%%', startangle=90,
    pctdistance=0.75, textprops={'fontsize': 11}
)

# Draw center circle to make it a donut
centre_circle = plt.Circle((0, 0), 0.55, fc='white')
ax.add_artist(centre_circle)

# Center text
ax.text(0, 0, 'Languages\n2024', ha='center', va='center',
        fontsize=14, fontweight='bold', color='#333')

ax.set_title('Language Distribution', fontsize=14, fontweight='bold', pad=20)
plt.show()
```

---

## 8. Heatmaps

### Correlation Heatmap

```python
np.random.seed(42)
data = np.random.randn(100, 5)
labels = ['Temp', 'Humidity', 'Wind', 'Pressure', 'Rain']
corr = np.corrcoef(data.T)

fig, ax = plt.subplots(figsize=(8, 6))
im = ax.imshow(corr, cmap='RdBu_r', vmin=-1, vmax=1)

# Add colorbar
cbar = plt.colorbar(im, ax=ax)
cbar.set_label('Correlation')

# Add labels
ax.set_xticks(range(len(labels)))
ax.set_yticks(range(len(labels)))
ax.set_xticklabels(labels, rotation=45, ha='right')
ax.set_yticklabels(labels)

# Add text annotations
for i in range(len(labels)):
    for j in range(len(labels)):
        color = 'white' if abs(corr[i, j]) > 0.5 else 'black'
        ax.text(j, i, f'{corr[i, j]:.2f}', ha='center', va='center',
                color=color, fontsize=10)

ax.set_title('Correlation Heatmap', fontsize=14, fontweight='bold')
plt.tight_layout()
plt.show()
```

### Custom Heatmap (e.g., Confusion Matrix)

```python
confusion = np.array([[85, 10, 5],
                       [8, 78, 14],
                       [3, 12, 85]])
classes = ['Cat', 'Dog', 'Bird']

fig, ax = plt.subplots(figsize=(7, 6))
im = ax.imshow(confusion, cmap='Blues')

ax.set_xticks(range(3))
ax.set_yticks(range(3))
ax.set_xticklabels(classes)
ax.set_yticklabels(classes)
ax.set_xlabel('Predicted')
ax.set_ylabel('Actual')
ax.set_title('Confusion Matrix')

for i in range(3):
    for j in range(3):
        color = 'white' if confusion[i, j] > 50 else 'black'
        ax.text(j, i, str(confusion[i, j]), ha='center', va='center',
                color=color, fontsize=16, fontweight='bold')

plt.colorbar(im, ax=ax)
plt.tight_layout()
plt.show()
```

### Missing Values Heatmap

```python
import pandas as pd

# Create sample data with missing values
np.random.seed(42)
df = pd.DataFrame(np.random.randn(20, 6), columns=['A', 'B', 'C', 'D', 'E', 'F'])
# Introduce random missing values
mask = np.random.random(df.shape) < 0.2
df[mask] = np.nan

fig, ax = plt.subplots(figsize=(10, 6))
im = ax.imshow(df.isnull().T, cmap='YlOrRd', aspect='auto', interpolation='nearest')

ax.set_yticks(range(len(df.columns)))
ax.set_yticklabels(df.columns)
ax.set_xlabel('Row Index')
ax.set_title('Missing Values Heatmap (yellow = present, red = missing)')
plt.tight_layout()
plt.show()
```

---

## 9. Subplots & Layouts

### Basic Grid

```python
fig, axes = plt.subplots(2, 2, figsize=(12, 8))

# Top-left
axes[0, 0].plot(np.random.randn(50).cumsum())
axes[0, 0].set_title('Line Plot')

# Top-right
axes[0, 1].scatter(np.random.randn(50), np.random.randn(50))
axes[0, 1].set_title('Scatter Plot')

# Bottom-left
axes[1, 0].bar(['A', 'B', 'C', 'D'], [25, 40, 30, 55])
axes[1, 0].set_title('Bar Chart')

# Bottom-right
axes[1, 1].hist(np.random.randn(500), bins=25)
axes[1, 1].set_title('Histogram')

fig.suptitle('Four Plot Dashboard', fontsize=16, fontweight='bold')
plt.tight_layout()
plt.show()
```

### Unequal Subplot Sizes (GridSpec)

```python
from matplotlib.gridspec import GridSpec

fig = plt.figure(figsize=(12, 8))
gs = GridSpec(2, 3, figure=fig)

# Large plot on the left (spanning 2 rows)
ax1 = fig.add_subplot(gs[:, 0:2])
ax1.plot(np.random.randn(100).cumsum(), linewidth=2)
ax1.set_title('Main Plot (2×2)')

# Small plot top-right
ax2 = fig.add_subplot(gs[0, 2])
ax2.bar(['A', 'B', 'C'], [10, 25, 15])
ax2.set_title('Bar')

# Small plot bottom-right
ax3 = fig.add_subplot(gs[1, 2])
ax3.hist(np.random.randn(200), bins=15, color='#16a34a')
ax3.set_title('Histogram')

plt.tight_layout()
plt.show()
```

### Shared Axes

```python
# Share x-axis (aligned time series)
fig, (ax1, ax2) = plt.subplots(2, 1, figsize=(10, 6), sharex=True)

x = np.linspace(0, 10, 100)
ax1.plot(x, np.sin(x), color='#2563eb')
ax1.set_ylabel('sin(x)')
ax1.set_title('Shared X-Axis')

ax2.plot(x, np.cos(x), color='#dc2626')
ax2.set_ylabel('cos(x)')
ax2.set_xlabel('x')

plt.tight_layout()
plt.show()
```

### Inset Plot (plot within a plot)

```python
fig, ax = plt.subplots(figsize=(10, 6))

# Main plot
x = np.linspace(0, 10, 1000)
y = np.sin(x) * np.exp(-0.1 * x)
ax.plot(x, y, color='#2563eb')
ax.set_title('Signal with Zoomed Inset')

# Inset
ax_inset = ax.inset_axes([0.55, 0.55, 0.4, 0.4])  # [x, y, width, height]
mask = (x > 2) & (x < 4)
ax_inset.plot(x[mask], y[mask], color='#dc2626', linewidth=2)
ax_inset.set_title('Zoomed', fontsize=10)
ax_inset.set_xlim(2, 4)
ax.indicate_inset_zoom(ax_inset, edgecolor='#dc2626')

plt.show()
```

---

## 10. Styling & Customization

### Built-in Styles

```python
# See all available styles
print(plt.style.available)

# Use a style
plt.style.use('seaborn-v0_8-whitegrid')

# Temporarily use a style
with plt.style.context('dark_background'):
    plt.plot([1, 2, 3], [1, 4, 9])
    plt.show()
```

### Popular Styles

```python
styles = ['default', 'seaborn-v0_8', 'ggplot', 'fivethirtyeight',
          'dark_background', 'bmh', 'tableau-colorblind10']

fig, axes = plt.subplots(2, 4, figsize=(20, 8))
axes = axes.flatten()

for ax, style in zip(axes, styles):
    with plt.style.context(style):
        x = np.linspace(0, 5, 50)
        ax.plot(x, np.sin(x), label='sin')
        ax.plot(x, np.cos(x), label='cos')
        ax.set_title(style, fontsize=10)
        ax.legend(fontsize=8)

axes[-1].set_visible(False)
plt.tight_layout()
plt.show()
```

### Color Palettes

```python
# Named colors
colors = ['#2563eb', '#dc2626', '#16a34a', '#d97706', '#7c3aed']

# Colormaps (for continuous data)
# Sequential:  'Blues', 'Reds', 'Greens', 'viridis', 'plasma'
# Diverging:   'RdBu', 'coolwarm', 'RdYlGn'
# Qualitative: 'Set1', 'Set2', 'tab10', 'Paired'

# Generate colors from a colormap
from matplotlib.cm import get_cmap
cmap = get_cmap('tab10')
colors = [cmap(i) for i in range(10)]
```

### Customizing the Figure

```python
fig, ax = plt.subplots(figsize=(10, 6))

# Remove top and right spines
ax.spines['top'].set_visible(False)
ax.spines['right'].set_visible(False)

# Customize remaining spines
ax.spines['bottom'].set_color('#999999')
ax.spines['left'].set_color('#999999')

# Tick styling
ax.tick_params(axis='both', colors='#666666', labelsize=11)

# Grid
ax.grid(True, alpha=0.3, linestyle='--')

# Background color
ax.set_facecolor('#fafafa')
fig.patch.set_facecolor('white')
```

### Custom Fonts

```python
plt.rcParams.update({
    'font.family': 'sans-serif',
    'font.size': 12,
    'axes.titlesize': 16,
    'axes.labelsize': 13,
    'xtick.labelsize': 11,
    'ytick.labelsize': 11,
    'legend.fontsize': 11,
    'figure.titlesize': 18
})
```

---

## 11. Annotations & Text

### Adding Text

```python
fig, ax = plt.subplots(figsize=(10, 6))
x = np.linspace(0, 10, 100)
y = np.sin(x)
ax.plot(x, y)

# Simple text at coordinates
ax.text(5, 0.8, 'Peak Region', fontsize=12,
        ha='center', color='#dc2626', fontweight='bold')

# Text with background box
ax.text(8, -0.5, 'Trough', fontsize=11,
        bbox=dict(boxstyle='round,pad=0.3', facecolor='#fef3c7',
                  edgecolor='#d97706', alpha=0.8))

plt.show()
```

### Annotations with Arrows

```python
fig, ax = plt.subplots(figsize=(10, 6))
x = np.linspace(0, 10, 100)
y = np.sin(x)
ax.plot(x, y)

# Annotation with arrow
ax.annotate('Maximum', xy=(np.pi/2, 1), xytext=(3, 1.3),
            arrowprops=dict(arrowstyle='->', color='#dc2626', lw=2),
            fontsize=12, color='#dc2626', fontweight='bold')

ax.annotate('Minimum', xy=(3*np.pi/2, -1), xytext=(6, -1.3),
            arrowprops=dict(arrowstyle='->', color='#2563eb', lw=2),
            fontsize=12, color='#2563eb', fontweight='bold')

ax.annotate('Zero Crossing', xy=(np.pi, 0), xytext=(5, 0.5),
            arrowprops=dict(arrowstyle='->', connectionstyle='arc3,rad=0.3',
                          color='#16a34a', lw=1.5),
            fontsize=11, color='#16a34a')

plt.show()
```

### Vertical / Horizontal Lines & Spans

```python
fig, ax = plt.subplots(figsize=(10, 6))
x = np.linspace(0, 10, 100)
ax.plot(x, np.sin(x))

# Horizontal line
ax.axhline(y=0, color='gray', linestyle='--', alpha=0.5)

# Vertical line
ax.axvline(x=np.pi, color='red', linestyle=':', label='x = π')

# Shaded region
ax.axvspan(4, 6, alpha=0.1, color='green', label='Region of Interest')
ax.axhspan(-0.5, 0.5, alpha=0.05, color='blue')

ax.legend()
plt.show()
```

---

## 12. Saving Figures

### Basic Save

```python
fig, ax = plt.subplots(figsize=(10, 6))
ax.plot([1, 2, 3], [1, 4, 9])
ax.set_title('My Plot')

# Save as PNG (default)
fig.savefig('plot.png', dpi=150, bbox_inches='tight')

# Save as high-res PNG
fig.savefig('plot_hires.png', dpi=300, bbox_inches='tight')

# Save as SVG (vector — scales perfectly)
fig.savefig('plot.svg', bbox_inches='tight')

# Save as PDF
fig.savefig('plot.pdf', bbox_inches='tight')
```

### Save Options

```python
fig.savefig('output.png',
    dpi=300,               # resolution (dots per inch)
    bbox_inches='tight',   # crop whitespace
    facecolor='white',     # background color
    edgecolor='none',      # edge color
    transparent=False,     # transparent background
    pad_inches=0.1         # padding around figure
)
```

### Common DPI Reference

| Use Case | DPI |
|----------|-----|
| Screen / Web | 72–100 |
| Presentations | 150 |
| Print / Publication | 300 |
| High-quality print | 600 |

---

## 13. Seaborn Integration

Seaborn builds on matplotlib with nicer defaults and statistical plots.

```bash
pip install seaborn
```

```python
import seaborn as sns
import pandas as pd

# Set style
sns.set_theme(style='whitegrid')
```

### Distribution Plot

```python
data = np.random.randn(500)

fig, ax = plt.subplots(figsize=(8, 5))
sns.histplot(data, kde=True, ax=ax, color='#2563eb')
ax.set_title('Distribution with KDE')
plt.show()
```

### Pair Plot (multi-variable exploration)

```python
df = pd.DataFrame(np.random.randn(200, 4), columns=['A', 'B', 'C', 'D'])
df['group'] = np.random.choice(['X', 'Y'], 200)

sns.pairplot(df, hue='group', diag_kind='kde')
plt.show()
```

### Heatmap (easier than raw matplotlib)

```python
corr = df[['A', 'B', 'C', 'D']].corr()

fig, ax = plt.subplots(figsize=(7, 6))
sns.heatmap(corr, annot=True, fmt='.2f', cmap='RdBu_r',
            center=0, square=True, ax=ax)
ax.set_title('Correlation Heatmap (Seaborn)')
plt.tight_layout()
plt.show()
```

### Categorical Plots

```python
tips = sns.load_dataset('tips')

fig, axes = plt.subplots(1, 3, figsize=(18, 5))

sns.boxplot(data=tips, x='day', y='total_bill', ax=axes[0])
axes[0].set_title('Box Plot')

sns.violinplot(data=tips, x='day', y='total_bill', ax=axes[1])
axes[1].set_title('Violin Plot')

sns.swarmplot(data=tips, x='day', y='total_bill', ax=axes[2], size=4)
axes[2].set_title('Swarm Plot')

plt.tight_layout()
plt.show()
```

---

## Practice Exercises

### Exercise 1: Sales Dashboard

> Create a 2×2 dashboard with: (top-left) monthly revenue line chart, (top-right) revenue by category bar chart, (bottom-left) scatter of revenue vs. ad spend, (bottom-right) distribution of order values. Use a consistent color scheme and add proper titles.

```python
np.random.seed(42)
months = ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun',
          'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec']
revenue = np.random.uniform(80, 150, 12)
# Your solution here
```

### Exercise 2: Before & After Comparison

> Plot two distributions (before and after treatment) on the same histogram with different colors and transparency. Add vertical lines for each mean, annotate the shift, and include a legend.

```python
before = np.random.normal(50, 10, 500)
after = np.random.normal(55, 8, 500)
# Your solution here
```

### Exercise 3: Multi-Line Time Series

> Plot 5 stock prices over 252 trading days as a random walk. Each should have a different color. Add a moving average (20-day) for each stock as a dashed line. Include a legend.

```python
np.random.seed(42)
days = 252
stocks = {}
for name in ['AAPL', 'GOOGL', 'MSFT', 'AMZN', 'META']:
    stocks[name] = 100 + np.random.randn(days).cumsum()
# Your solution here
```

### Exercise 4: Annotated Scatter

> Create a scatter plot of 20 cities with population (x) vs. GDP (y). Make bubble size proportional to area. Annotate the top 3 cities by GDP with their names and arrows.

```python
# Your solution here
```

### Exercise 5: Custom Styled Report Figure

> Create a publication-quality figure with: removed top/right spines, custom font sizes, a subtle grid, proper axis labels with units, a title, and save at 300 DPI as both PNG and SVG.

```python
# Your solution here
```

---

## Quick Reference Cheat Sheet

| Task | Code |
|------|------|
| Create figure | `fig, ax = plt.subplots(figsize=(w, h))` |
| Line plot | `ax.plot(x, y)` |
| Scatter | `ax.scatter(x, y)` |
| Bar chart | `ax.bar(x, y)` |
| Histogram | `ax.hist(data, bins=n)` |
| Box plot | `ax.boxplot(data)` |
| Pie chart | `ax.pie(sizes, labels=labels)` |
| Heatmap | `ax.imshow(data, cmap='...')` |
| Title | `ax.set_title('...')` |
| X label | `ax.set_xlabel('...')` |
| Y label | `ax.set_ylabel('...')` |
| Legend | `ax.legend()` |
| Grid | `ax.grid(True, alpha=0.3)` |
| Limits | `ax.set_xlim(a, b)` |
| Horizontal line | `ax.axhline(y=val)` |
| Vertical line | `ax.axvline(x=val)` |
| Annotate | `ax.annotate('text', xy=(...))` |
| Subplots | `fig, axes = plt.subplots(r, c)` |
| Remove spine | `ax.spines['top'].set_visible(False)` |
| Save | `fig.savefig('f.png', dpi=300)` |
| Style | `plt.style.use('seaborn-v0_8')` |
| Colorbar | `plt.colorbar(mappable, ax=ax)` |
| Tight layout | `plt.tight_layout()` |

---

*Part of the [Data Handling and Processing](../) module. See also: [Pandas Hands-On](../pandas-hands-on/) | [NumPy Hands-On](../numpy-hands-on/)*
