## NYC Schools SAT Performance Analysis

This analysis explores SAT performance across NYC public schools using a dataset containing average scores in Math, Reading, and Writing, as well as borough information.

### 🧹 Data Preparation

```python
import pandas as pd

# Load the dataset
new_school = pd.read_csv('schools.csv')

# Fill missing values with 0
new_school.fillna(0, inplace=True)
```

---

### 📊 Best Math Performing Schools

**Criteria**: Schools with an average Math score of at least 640 (80% of the maximum score 800).

```python
best_math_schools = new_school[new_school['average_math'] >= 640][['school_name', 'average_math']]
best_math_schools = best_math_schools.sort_values(by='average_math', ascending=False)
```

---

### 🏆 Top 10 Schools by Combined SAT Score

**Definition**: `total_SAT = average_math + average_reading + average_writing`

```python
# Calculate total SAT score
new_school['total_SAT'] = (
    new_school['average_math'] + 
    new_school['average_reading'] + 
    new_school['average_writing']
)

# Select and sort top 10
top_10_schools = new_school[['school_name', 'total_SAT']]
top_10_schools = top_10_schools.sort_values(by='total_SAT', ascending=False).head(10)
```

---

### 🏙️ Borough with Largest SAT Score Deviation

**Goal**: Identify the borough with the highest standard deviation in `total_SAT`.

```python
borough_stats = new_school.groupby('borough').agg(
    num_schools=('school_name', 'count'),
    average_SAT=('total_SAT', 'mean'),
    std_SAT=('total_SAT', 'std')
).reset_index()

# Get the borough with the max std deviation
max_std_borough = borough_stats.sort_values(by='std_SAT', ascending=False).head(1)

# Round numeric values
largest_std_dev = max_std_borough.copy()
largest_std_dev[['average_SAT', 'std_SAT']] = largest_std_dev[['average_SAT', 'std_SAT']].round(2)
```

---

### ✅ Outputs Summary

- `best_math_schools`: DataFrame with schools scoring >=640 in Math.
- `top_10_schools`: DataFrame with the 10 highest `total_SAT` scores.
- `largest_std_dev`: Single-row DataFrame with borough name, number of schools, average and standard deviation of SAT scores.
