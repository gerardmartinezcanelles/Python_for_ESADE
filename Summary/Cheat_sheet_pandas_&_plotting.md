# 🐼 Pandas Cheat Sheet — Core Concepts for Data Analysis

Pandas is a powerful Python library for data manipulation and analysis. It provides two core data structures and a rich set of functions to help you clean, transform, and summarize data efficiently.

---

## 📦 Core Data Structures

| Structure    | Description                                                        | Analogy             |
|--------------|--------------------------------------------------------------------|---------------------|
| `Series`     | 1-dimensional labeled array                                        | A single column     |
| `DataFrame`  | 2-dimensional table with rows and columns                         | Excel worksheet     |
| `Index`      | Labels for rows (or columns)                                       | Row headers         |

---

## 📥 Loading and Inspecting Data

```python
import pandas as pd

# Load data from CSV or Excel
df = pd.read_csv("file.csv")             # Full_path not just namefile
df = pd.read_excel("file.xlsx")          # Full_path not just namefile

# Basic info
df.head()          # First 5 rows
df.tail()          # Last 5 rows
df.info()          # Structure and types
df.describe()      # Summary stats for numeric columns
df.shape           # (rows, columns)
df.columns         # Column names
df.index           # Row index
```

---

## 🔍 Selecting and Filtering Data

In pandas, you can select specific rows and columns using different techniques. It’s important to understand **the difference between selecting a single column (Series) and multiple columns (DataFrame).**

### 🎯 Selecting Columns

| Selection        | Result Type     | Example                          | Notes                                               |
|------------------|------------------|----------------------------------|-----------------------------------------------------|
| Single brackets  | `Series`         | `df['spend']`                    | 1D, one column — use for calculations and plotting  |
| Double brackets  | `DataFrame`      | `df[['spend']]`                  | 2D, preserves DataFrame structure                   |
| Multiple columns | `DataFrame`      | `df[['spend', 'country']]`       | Use for subsetting with multiple columns            |

```python
type(df['spend'])        # pandas.Series
type(df[['spend']])      # pandas.DataFrame
```

### 🔢 Selecting Rows

```python
df.iloc[0]               # First row (Series)
df.iloc[0:5]             # First 5 rows (DataFrame)

df.loc[5]                # Row with index = 5
df.loc[5, 'spend']       # Value at row index 5 and column 'spend'
```

### 🔍 Filtering Rows (Conditional Selection)

```python
df[df['spend'] > 100]                                      # Spend greater than 100
df[df['country'] == 'US']                                  # Only US players
df[(df['spend'] > 100) & (df['country'] == 'US')]          # Combine multiple conditions
```

💡 **Tip:** Use parentheses `()` around conditions when combining them with `&` or `|`.

---

## 📊  Descriptive Stats

Use these functions to calculate summary statistics and group-based aggregations.

This section summarizes the most important **statistical functions** you can use in pandas to analyze one or more variables in your dataset.

---

### 📏 1. Central Tendency and Dispersion (Univariate)

These functions help describe the **shape and spread** of a single variable (e.g., `spend`, `time_in_game`):

| Metric                | Code                                  | Description                          |
|----------------------|---------------------------------------|--------------------------------------|
| **Mean**             | `df['x'].mean()`                      | Average value                        |
| **Median**           | `df['x'].median()`                    | Middle value                         |
| **Mode**             | `df['x'].mode()`                      | Most frequent value(s)               |
| **Standard Deviation** | `df['x'].std()`                     | Spread around the mean               |
| **Variance**         | `df['x'].var()`                       | Squared spread around the mean       |
| **Min / Max**        | `df['x'].min()` / `df['x'].max()`     | Lowest / highest value               |
| **Quartiles**        | `df['x'].quantile(0.25)`              | Percentile-based summaries           |
| **Summary**          | `df['x'].describe()`                  | All key stats in one view            |
| **Skewness**         | `df['x'].skew()`                      | Asymmetry of the distribution        |
| **Kurtosis**         | `df['x'].kurt()`                      | How "peaked" or "tailed" the data is |

---

### 🔗 2. Relationship Between Two Variables (Bivariate)

These metrics show **how two variables relate** to each other.

| Metric                   | Code                                                 | Description                                 |
|--------------------------|------------------------------------------------------|---------------------------------------------|
| **Covariance**           | `df[['x', 'y']].cov()` or `df['x'].cov(df['y'])`    | How two variables vary together             |
| **Correlation (Pearson)**| `df[['x', 'y']].corr()` or `df['x'].corr(df['y'])`  | Linear relationship strength (-1 to 1)      |
| **Spearman Correlation** | `df.corr(method='spearman')`                        | Rank-based correlation                      |
| **Kendall Correlation**  | `df.corr(method='kendall')`                         | Based on concordant/discordant pairs        |

---

### 📚 Optional Advanced: Standardization & Outlier Detection

```python
# Z-score standardization
df_player['z_spend'] = (df_player['moneyspent'] - df_player['moneyspent'].mean()) / df_player['moneyspent'].std()

# IQR-based outlier detection
Q1 = df_player['moneyspent'].quantile(0.25)
Q3 = df_player['moneyspent'].quantile(0.75)
IQR = Q3 - Q1
outliers = df_player[(df_player['moneyspent'] < Q1 - 1.5 * IQR) | (df_player['moneyspent'] > Q3 + 1.5 * IQR)]
```

---

✅ Use these tools to summarize, compare, and investigate your dataset with confidence!


## 🧮 Aggregations

### 👥 Grouped Aggregation (groupby)

```python
df.groupby('country')['spend'].mean()
df.groupby(['casegroup', 'country'])['spend'].sum()
df.groupby('casegroup')['spend'].agg(['mean', 'median', 'std'])
```

### 🇹 Pivot Tables

```python
pd.pivot_table(df, index='dt', columns='casegroup', values='spend', aggfunc='mean')
pd.pivot_table(df, index='country', columns='casegroup', values='spend', aggfunc='sum')
pd.pivot_table(df, index='country', values='id_of_player', aggfunc='nunique')
```

Common `aggfunc` Options for Pivot Tables

| `aggfunc`         | What it does                                         | Example Use Case                                |
|-------------------|------------------------------------------------------|--------------------------------------------------|
| `'mean'`          | Calculates the average                               | Average spend per group                         |
| `'sum'`           | Adds up all values                                   | Total revenue per country                       |
| `'count'`         | Counts non-null values                               | Number of entries (e.g., visits per day)        |
| `'nunique'`       | Counts unique values                                 | Number of unique players per country            |
| `'median'`        | Calculates the median value                          | Median session time                             |
| `'max'` / `'min'` | Returns the max/min value                            | Peak spend in a group                           |
| `len`             | Same as count, but counts all (including nulls)      | Count rows, regardless of missing values        |
| `lambda x: ...`   | Custom function — can write any logic you want       | Advanced summaries (e.g., % over threshold)     |

🧠 You can pass a list of functions too:
```python
pd.pivot_table(df, index='country', values='spend', aggfunc=['mean', 'std', 'min', 'max'])
````

---

## 🧹 Common Data Cleaning Tasks

```python
df.isnull().sum()
df.dropna()
df.fillna(0)

df.rename(columns={'old': 'new'}, inplace=True)
df['spend'] = df['spend'].astype(float)
```

✅ Use these tools to clean and prepare your data for analysis!


---
---
---

# 📈 Plotting Cheat Sheet — Introduction to Data Visualization

This guide summarizes the most common **plot types** using **Matplotlib** and **Seaborn**, along with **when to use them**, **sample code**, and **what they reveal** about the data.

---

### 📊 1. Basic Plot Types — Univariate & Bivariate Analysis

| Plot Type       | When to Use               | Code Example (Matplotlib / Seaborn)                                | What It Shows                                                  |
|------------------|---------------------------|---------------------------------------------------------------------|----------------------------------------------------------------|
| **Line Plot**    | Continuous over time       | `plt.plot(x, y)`<br>`sns.lineplot(x=x, y=y, data=df)`              | Trend or evolution over time                                   |
| **Bar Chart**    | Discrete categories        | `plt.bar(x, height)`<br>`sns.barplot(x=x, y=y, data=df)`           | Comparison of values across categories                         |
| **Histogram**    | Continuous distribution    | `plt.hist(x)`<br>`sns.histplot(x=df['col'], bins=30)`              | Distribution and frequency of values                           |
| **Boxplot**      | Continuous per category    | `plt.boxplot([a, b])`<br>`sns.boxplot(x='group', y='value', data=df)` | Distribution (median, IQR, outliers) by category               |
| **Violin Plot**  | Continuous per category    | N/A<br>`sns.violinplot(x='group', y='value', data=df)`             | Similar to boxplot but shows full distribution shape           |
| **Pie Chart**    | Discrete proportions       | `plt.pie(values, labels=labels)`                                   | Proportion of categories (avoid for >5 categories)             |
| **Scatter Plot** | 2 continuous variables     | `plt.scatter(x, y)`<br>`sns.scatterplot(x='x', y='y', data=df)`    | Relationship between two numeric variables                     |

---

### 📈 2. Advanced / Multivariate Visualizations

| Plot Type             | When to Use                         | Code Example                                                       | What It Shows                                                  |
|------------------------|-------------------------------------|---------------------------------------------------------------------|----------------------------------------------------------------|
| **FacetGrid**          | Same plot across subsets            | `sns.FacetGrid(df, col='group').map(sns.histplot, 'value')`        | Small multiples — helps compare patterns by subgroup           |
| **Pairplot (Scatter Matrix)** | Several numeric variables     | `sns.pairplot(df[['a', 'b', 'c']])`                                 | All pairwise relationships + distributions                     |
| **Heatmap (Correlation Grid)** | Numeric correlations         | `sns.heatmap(df.corr(), annot=True, cmap='coolwarm')`              | Visual correlation matrix (helps spot strong relationships)    |
| **Strip/Swarm Plot**   | Continuous + categorical            | `sns.swarmplot(x='group', y='value', data=df)`                     | Like scatterplot over categories — better for small samples    |
| **KDE Plot**           | Continuous distribution shape       | `sns.kdeplot(df['value'], fill=True)`                              | Smoothed distribution of a continuous variable                 |

---

### 🧪 Sample Plot Code Snippets

```python
# Line Plot
plt.plot(df['dt'], df['spend'])
plt.title("Spend Over Time")

# Bar Plot
sns.barplot(x='country', y='moneyspent', data=df_player)

# Histogram
sns.histplot(df_player['spend'], bins=30)
or
plt.hist(df_player['moneyspent'])

# Boxplot
sns.boxplot(x='platform', y='moneyspent', data=df_player)

# Pie Chart
labels = ['Australia', 'China', 'Europe', 'US']
sizes = df_player['region'].value_counts().loc[labels]
plt.pie(sizes, labels=labels, autopct='%1.1f%%')
plt.title("Player Distribution by Country")

# Scatter Plot
plt.scatter(df_player['gameroundsplayed'], df_player['levelscompleted'])
plt.xlabel("Game Rounds")
plt.ylabel("Levels completed")

# Correlation Heatmap
sns.heatmap(df_player[['age','daysingame','companygamesplayed']].corr(), annot=True, cmap='coolwarm')

# Pairplot
sns.pairplot(df_player[['age','daysingame','companygamesplayed']])
```

---

### 🎯 Tips for Students

- Use **line plots** for anything changing over time (e.g., daily spend).
- Use **histograms** and **KDE plots** for distributions.
- Use **boxplots** and **violin plots** to compare distributions between groups.
- Use **scatter plots** for relationships and trends.
- Use **heatmaps** and **pairplots** for multivariate pattern discovery.

---

✅ Mastering visualization is key to finding insights before modeling!



```python

```
