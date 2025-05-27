# 🏨 LuxurStay Hotel Service Performance Analysis

LuxurStay is an international hotel chain offering services to both business and leisure travelers. Management has observed a decline in customer satisfaction, especially related to slow room services in certain branches. This project explores and cleans service data to help identify performance gaps and recommend targeted improvements.

---

## 📚 Table of Contents
- [📊 Project Objectives](#-project-objectives)
- [🧹 Data Cleaning Steps](#-data-cleaning-steps)
- [📈 Key Analyses](#-key-analyses)
  - [✅ Task 1: Data Cleaning for branch Table](#️-task-1-data-cleaning-for-branch-table)
  - [🧠 Task 2: Response Time by Branch and Service](#-task-2-response-time-by-branch-and-service)
  - [🎯 Task 3: Targeting Meal and Laundry Services](#-task-3-targeting-meal-and-laundry-services)
  - [📉 Task 4: Identify Underperforming Services](#-task-4-identify-underperforming-services)
- [🛠️ Tools Used](#️-tools-used)

---

## 📊 Project Objectives
- Identify data quality issues and clean inconsistencies in the branch table.
- Evaluate service performance across hotel branches.
- Highlight service delays and sub-par ratings to support operational improvements.
- Provide actionable insights into customer satisfaction metrics.

---

## 🧹 Data Cleaning Steps

| Column Name   | Cleaning Rule                                                                 |
|---------------|--------------------------------------------------------------------------------|
| `location`    | Replace NULL or invalid values with `'Unknown'`                                |
| `total_rooms` | Ensure values are between 1–400. Replace NULLs with default 100                |
| `staff_count` | Replace NULLs with `total_rooms * 1.5`                                         |
| `opening_date`| Convert to integer. Acceptable range: 2000–2023. Replace NULLs with 2023       |
| `target_guests` | Replace NULLs with `'Leisure'`. Accept only `'Leisure'` or `'Business'`     |

---

## 📈 Key Analyses

### ✅ Task 1: Data Cleaning for branch Table

**Goal:**  
Clean the `branch` table to fix missing/invalid values as per provided criteria.

**SQL Code:**
```sql
WITH cleaned_branch AS (
  SELECT
    id,
    CASE 
      WHEN location NOT IN ('EMEA', 'NA', 'LATAM', 'APAC') OR location IS NULL 
      THEN 'Unknown' ELSE location 
    END AS location,
    CASE 
      WHEN total_rooms BETWEEN 1 AND 400 THEN total_rooms 
      ELSE 100 
    END AS total_rooms,
    CASE 
      WHEN staff_count IS NOT NULL THEN staff_count 
      ELSE CASE 
        WHEN total_rooms BETWEEN 1 AND 400 THEN total_rooms * 1.5 
        ELSE 100 * 1.5 
      END 
    END AS staff_count,
    CASE 
      WHEN TRY_CAST(opening_date AS INTEGER) BETWEEN 2000 AND 2023 
      THEN CAST(opening_date AS INTEGER)
      ELSE 2023 
    END AS opening_date,
    CASE 
      WHEN target_guests IN ('Leisure', 'Business') THEN target_guests 
      ELSE 'Leisure' 
    END AS target_guests
  FROM branch
)
SELECT * FROM cleaned_branch;

