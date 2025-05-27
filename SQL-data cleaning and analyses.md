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
.
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

- SQL -PostgreSQL(data wrangling and cleaning)

---

### ✅ Task 1: Data Cleaning for `branch` Table

**Goal:**  
Clean the branch table to fix missing/invalid values as per provided criteria.

```sql
WITH cleaned_branch AS (
  SELECT
    id,
    
    -- Clean `location`: valid values or 'Unknown'
    CASE 
      WHEN location NOT IN ('EMEA', 'NA', 'LATAM', 'APAC') OR location IS NULL 
      THEN 'Unknown' 
      ELSE location 
    END AS location,
    
    -- Clean `total_rooms`: must be between 1–400, default to 100
    CASE 
      WHEN total_rooms BETWEEN 1 AND 400 THEN total_rooms 
      ELSE 100 
    END AS total_rooms,
    
    -- Clean `staff_count`: use total_rooms * 1.5 if NULL or invalid
    CASE 
      WHEN staff_count IS NOT NULL THEN staff_count 
      ELSE 
        CASE 
          WHEN total_rooms BETWEEN 1 AND 400 THEN total_rooms * 1.5 
          ELSE 100 * 1.5 
        END 
    END AS staff_count,
    
    -- Clean `opening_date`: convert to integer, ensure it’s between 2000–2023, else default to 2023
    CASE 
      WHEN TRY_CAST(opening_date AS INTEGER) BETWEEN 2000 AND 2023 
      THEN CAST(opening_date AS INTEGER)
      ELSE 2023 
    END AS opening_date,
    
    -- Clean `target_guests`: must be 'Leisure' or 'Business', else default to 'Leisure'
    CASE 
      WHEN target_guests IN ('Leisure', 'Business') THEN target_guests 
      ELSE 'Leisure' 
    END AS target_guests

  FROM branch
)

SELECT * FROM cleaned_branch;
;
```


## 🧠 Task 2: Response Time by Branch and Service

**Goal:**  
To determine if response time to customer requests varies by hotel branch and service type. This will help the Head of Operations identify which branches or services are underperforming.

**SQL Summary:**

```sql
SELECT
  service_id,
  branch_id,
  ROUND(AVG(time_taken), 2) AS avg_time_taken,
  ROUND(MAX(time_taken), 2) AS max_time_taken
FROM request
GROUP BY service_id, branch_id;


---
```

## 🧠 Task 3: Targeting Meal and Laundry Services in EMEA and LATAM

**Goal:**  
Help management focus on improving **Meal** and **Laundry** services in **EMEA** and **LATAM** regions by identifying relevant service requests.

**SQL Summary:**

```sql
SELECT
  s.description,
  b.id AS branch_id,
  b.location,
  r.request_id,
  r.rating
FROM request r
JOIN branch b ON r.branch_id = b.id
JOIN service s ON r.service_id = s.id
WHERE s.description IN ('Meal', 'Laundry')
  AND b.location IN ('EMEA', 'LATAM');
```
---

## 📉 Task 4: Identify Underperforming Services by Branch

**Goal:**  
Help management focus on branch-service combinations where customer satisfaction is **below the target rating of 4.5**.

**SQL Summary:**

```sql
SELECT 
  service_id,
  branch_id,
  ROUND(AVG(rating), 2) AS avg_rating
FROM request
GROUP BY service_id, branch_id
HAVING AVG(rating) < 4.5;

```
