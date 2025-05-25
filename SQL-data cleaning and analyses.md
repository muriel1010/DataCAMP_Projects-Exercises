## 🧪 Hotel Service Analysis – SQL Tasks Breakdown

This document outlines the SQL tasks, business questions, and corresponding queries used in the analysis.

---

### ✅ Task 1: Data Cleaning for `branch` Table

**Goal:**  
Clean the branch table to fix missing/invalid values as per provided criteria.

**Query Summary:**
- Cleaned `location`, `total_rooms`, `staff_count`, `opening_date`, and `target_guests`
- Used `CASE WHEN` statements to impute missing or invalid values

**Output:** `clean_branch_data`

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

---

## 🧠 Task 2: Response Time by Branch and Service

**Goal:**  
To determine if response time to customer requests varies by hotel branch and service type. This will help the Head of Operations identify which branches or services are underperforming.

**What we did:**  
We calculated:
- The average time taken (`avg_time_taken`)  
- The maximum time taken (`max_time_taken`)  
for each combination of service and hotel branch.

**Logic:**
- Grouped data by `service_id` and `branch_id`
- Used `AVG()` and `MAX()` on the `time_taken` column
- Rounded results to two decimal places

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

## 🧠 Task 3: Targeting Meal and Laundry Services in EMEA and LATAM

**Goal:**  
Help management focus on improving **Meal** and **Laundry** services in **EMEA** and **LATAM** regions by identifying relevant service requests.

**What we did:**  
We filtered the data to retrieve:
- Service description  
- Branch ID and location  
- Request ID  
- Customer rating  

**Logic:**
- Joined `request`, `branch`, and `service` tables
- Filtered for services: `'Meal'` and `'Laundry'`
- Filtered for locations: `'EMEA'` and `'LATAM'`
- Used the original `branch` table (not cleaned data)

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

**What we did:**  
We calculated the **average rating** for each combination of `service_id` and `branch_id` and filtered for those **below 4.5**.

**Logic:**
- Grouped by `service_id` and `branch_id`
- Calculated the **average rating**
- Filtered only combinations with an average rating < 4.5
- Rounded results to 2 decimal places

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
