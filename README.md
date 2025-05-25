# 🏨 LuxurStay Hotel Service Performance Analysis

LuxurStay is an international hotel chain offering services to both business and leisure travelers. Management has observed a decline in customer satisfaction, especially related to slow room services in certain branches. This project explores and cleans service data to help identify performance gaps and recommend targeted improvements.

---

## 📊 Project Objectives

- Identify data quality issues and clean inconsistencies in the `branch` table.
- Evaluate service performance across hotel branches.
- Highlight service delays and sub-par ratings to support operational improvements.
- Provide actionable insights into customer satisfaction metrics.

---

## 🧹 Data Cleaning Steps

We performed cleaning operations on the `branch` table based on the following rules:

| Column Name     | Cleaning Rule                                                                 |
|-----------------|-------------------------------------------------------------------------------|
| `location`      | Replace NULL or invalid values with `'Unknown'`.                              |
| `total_rooms`   | Ensure values are between 1–400. Replace NULLs with default `100`.            |
| `staff_count`   | Replace NULLs with `total_rooms * 1.5`.                                       |
| `opening_date`  | Convert to integer. Acceptable range: 2000–2023. Replace NULLs with `2023`.   |
| `target_guests` | Replace NULLs with `'Leisure'`. Accept only `'Leisure'` or `'Business'`.      |

Output: A cleaned DataFrame named `clean_branch_data`.

---

## 📈 Key Analyses

### 1. Average and Maximum Service Time by Branch
Calculated average and max response time by service and branch.  
**Output**: `average_time_service`

### 2. Targeted Hotel Services in EMEA and LATAM
Filtered Meal and Laundry services in EMEA and LATAM for operational focus.  
**Output**: `target_hotels`

### 3. Low-Performing Services (< 4.5 Rating)
Extracted all services and branches with average ratings below the 4.5 threshold.  
**Output**: `average_rating`

---

## 🛠️ Tools Used

- SQL (data wrangling and cleaning)
- Jupyter Notebooks
- pandas (for DataFrame manipulation)
- PostgreSQL (assumed for query compatibility)

---

