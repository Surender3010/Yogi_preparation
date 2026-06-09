# Medium to Difficult SQL Interview Questions

---

## Medium Level

### 1. Find employees earning more than their department average

```sql
SELECT e.name, e.salary, e.department
FROM employees e
WHERE e.salary > (
  SELECT AVG(salary)
  FROM employees
  WHERE department = e.department
);
```

**Explanation:** A correlated subquery runs once per row — for each employee, it calculates the average salary of *that employee's* department and compares it. This tests understanding of correlated vs. non-correlated subqueries.

---

### 2. Find departments with no employees

```sql
SELECT d.department_name
FROM departments d
LEFT JOIN employees e ON d.id = e.dept_id
WHERE e.id IS NULL;
```

**Explanation:** LEFT JOIN includes all departments even if no employee matches. Filtering `WHERE e.id IS NULL` isolates departments with zero employees. This is the classic **anti-join** pattern.

---

### 3. Get the top 3 earners per department

```sql
SELECT name, department, salary
FROM (
  SELECT name, department, salary,
    DENSE_RANK() OVER (PARTITION BY department ORDER BY salary DESC) AS rnk
  FROM employees
) ranked
WHERE rnk <= 3;
```

**Explanation:** `DENSE_RANK()` assigns the same rank to ties and doesn't skip numbers (unlike `RANK()`). `PARTITION BY department` resets the ranking per department. Very commonly asked window function question.

---

### 4. Calculate month-over-month revenue growth

```sql
SELECT
  month,
  revenue,
  LAG(revenue) OVER (ORDER BY month) AS prev_month_revenue,
  ROUND(
    100.0 * (revenue - LAG(revenue) OVER (ORDER BY month))
    / LAG(revenue) OVER (ORDER BY month), 2
  ) AS growth_pct
FROM monthly_revenue;
```

**Explanation:** `LAG()` fetches the previous row's value in the ordered set. Dividing the difference by the previous value gives percentage growth. Tests window functions combined with arithmetic.

---

### 5. Find consecutive available dates (gaps in bookings)

```sql
SELECT a.date + INTERVAL '1 day' AS gap_start,
       b.date - INTERVAL '1 day' AS gap_end
FROM bookings a
JOIN bookings b ON b.date = (
  SELECT MIN(date) FROM bookings WHERE date > a.date
)
WHERE b.date > a.date + INTERVAL '1 day';
```

**Explanation:** Detects "gaps" between consecutive booking dates by finding pairs where the next booking date is more than 1 day away. Tests date arithmetic and self-join reasoning.

---

### 6. Pivot rows into columns using CASE

```sql
SELECT
  employee_id,
  SUM(CASE WHEN month = 'Jan' THEN sales END) AS Jan,
  SUM(CASE WHEN month = 'Feb' THEN sales END) AS Feb,
  SUM(CASE WHEN month = 'Mar' THEN sales END) AS Mar
FROM sales_data
GROUP BY employee_id;
```

**Explanation:** Conditional aggregation simulates a pivot table without native `PIVOT` syntax. Each `CASE WHEN` filters rows for one category and `SUM` collapses them. Common in reporting scenarios.

---

### 7. Cumulative distribution of salaries

```sql
SELECT name, salary,
  CUME_DIST()    OVER (ORDER BY salary) AS cumulative_dist,
  PERCENT_RANK() OVER (ORDER BY salary) AS percent_rank
FROM employees;
```

**Explanation:** `CUME_DIST()` returns the fraction of rows with values ≤ the current row's value. `PERCENT_RANK()` = (rank - 1) / (total rows - 1). Used in statistical analysis of distributions.

---

### 8. Find users who logged in on consecutive days

```sql
SELECT DISTINCT a.user_id
FROM logins a
JOIN logins b
  ON a.user_id = b.user_id
  AND b.login_date = a.login_date + INTERVAL '1 day';
```

**Explanation:** Self-join on the same user where one login date is exactly 1 day after another detects consecutive-day logins. Classic streak-detection pattern.

---

## Difficult Level

### 9. Islands and Gaps — group consecutive sequences

```sql
SELECT user_id,
  MIN(login_date) AS streak_start,
  MAX(login_date) AS streak_end,
  COUNT(*)        AS streak_length
FROM (
  SELECT user_id, login_date,
    login_date
      - ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY login_date) * INTERVAL '1 day'
      AS grp
  FROM logins
) grouped
GROUP BY user_id, grp
ORDER BY user_id, streak_start;
```

**Explanation:** Subtracting `ROW_NUMBER() * 1 day` from each date produces the same "anchor" date for consecutive dates — creating a group key (`grp`). Rows in the same streak share the same `grp`. This is the classic **Islands & Gaps** pattern.

---

### 10. Recursive CTE — organizational hierarchy

```sql
WITH RECURSIVE org_chart AS (
  -- Base case: root node (CEO has no manager)
  SELECT id, name, manager_id, 0 AS level
  FROM employees
  WHERE manager_id IS NULL

  UNION ALL

  -- Recursive step: join each employee to their manager
  SELECT e.id, e.name, e.manager_id, oc.level + 1
  FROM employees e
  JOIN org_chart oc ON e.manager_id = oc.id
)
SELECT * FROM org_chart ORDER BY level, name;
```

**Explanation:** Recursive CTEs have a base case (anchor) and a recursive step that references the CTE itself. Starts from the root employee and traverses down the tree level by level. Tests deep understanding of CTEs and hierarchical data.

---

### 11. Median salary (without MEDIAN function)

```sql
SELECT AVG(salary) AS median
FROM (
  SELECT salary,
    ROW_NUMBER() OVER (ORDER BY salary) AS rn,
    COUNT(*)     OVER ()                AS total
  FROM employees
) t
WHERE rn IN (FLOOR((total + 1) / 2.0), CEIL((total + 1) / 2.0));
```

**Explanation:** There's no universal `MEDIAN()` in SQL. This finds the middle row(s) using `ROW_NUMBER()` and `COUNT()`, then averages them — handles both odd and even row counts correctly. Tests number theory combined with window functions.

---

### 12. Find the longest streak of wins

```sql
WITH ranked AS (
  SELECT player_id, match_date, result,
    ROW_NUMBER() OVER (PARTITION BY player_id ORDER BY match_date) -
    ROW_NUMBER() OVER (PARTITION BY player_id, result ORDER BY match_date) AS grp
  FROM match_results
),
streaks AS (
  SELECT player_id, result, COUNT(*) AS streak_len
  FROM ranked
  WHERE result = 'Win'
  GROUP BY player_id, result, grp
)
SELECT player_id, MAX(streak_len) AS longest_win_streak
FROM streaks
GROUP BY player_id;
```

**Explanation:** The **double ROW_NUMBER trick** — subtracting row numbers with and without partitioning on `result` produces the same group key for consecutive same-result rows. Counting per group gives streak length.

---

### 13. Running total that resets per partition

```sql
SELECT order_id, amount, category,
  SUM(amount) OVER (
    PARTITION BY category
    ORDER BY order_date
    ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
  ) AS running_total_by_category
FROM orders;
```

**Explanation:** `PARTITION BY category` resets the running total for each category. `ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW` explicitly defines the window frame. Tests precise window frame specification.

---

### 14. Detect duplicate transactions within a time window

```sql
SELECT t1.transaction_id AS duplicate_id,
       t2.transaction_id AS original_id
FROM transactions t1
JOIN transactions t2
  ON  t1.user_id        = t2.user_id
  AND t1.amount         = t2.amount
  AND t1.transaction_id > t2.transaction_id
  AND t1.created_at BETWEEN t2.created_at
                        AND t2.created_at + INTERVAL '5 minutes';
```

**Explanation:** Self-join finds two transactions by the same user for the same amount within 5 minutes. `t1.id > t2.id` prevents matching a row with itself and avoids duplicating result pairs. Real-world fraud detection pattern.

---

### 15. Allocate budget — greedy first-fit

```sql
WITH cumulative AS (
  SELECT project_id, cost,
    SUM(cost) OVER (ORDER BY priority ROWS UNBOUNDED PRECEDING) AS cumulative_cost
  FROM projects
)
SELECT project_id, cost, cumulative_cost
FROM cumulative
WHERE cumulative_cost <= 1000000; -- budget cap
```

**Explanation:** Cumulative sum with window frames lets you greedily select projects in priority order until the budget is exhausted. Tests applied use of window frames for optimization problems.

---

### 16. Compare two snapshots — find added/deleted/modified rows

```sql
SELECT
  COALESCE(n.id, o.id) AS id,
  CASE
    WHEN o.id IS NULL THEN 'Added'
    WHEN n.id IS NULL THEN 'Deleted'
    ELSE 'Modified'
  END AS change_type,
  o.salary AS old_salary,
  n.salary AS new_salary
FROM employees_snapshot_new  n
FULL OUTER JOIN employees_snapshot_old o ON n.id = o.id
WHERE n.salary IS DISTINCT FROM o.salary
   OR n.id IS NULL
   OR o.id IS NULL;
```

**Explanation:** `FULL OUTER JOIN` captures rows that exist in only one snapshot. `IS DISTINCT FROM` handles NULLs safely (unlike `!=`). Used in data audit and change-data-capture (CDC) scenarios.

---

### 17. Sessionization — group events into sessions

```sql
WITH gaps AS (
  SELECT user_id, event_time,
    CASE
      WHEN event_time - LAG(event_time) OVER (PARTITION BY user_id ORDER BY event_time)
           > INTERVAL '30 minutes'
      THEN 1 ELSE 0
    END AS new_session
  FROM events
),
sessions AS (
  SELECT user_id, event_time,
    SUM(new_session) OVER (PARTITION BY user_id ORDER BY event_time) AS session_id
  FROM gaps
)
SELECT user_id, session_id,
  MIN(event_time) AS session_start,
  MAX(event_time) AS session_end,
  COUNT(*)        AS event_count
FROM sessions
GROUP BY user_id, session_id;
```

**Explanation:** A gap > 30 minutes marks the start of a new session (`new_session = 1`). `SUM(new_session) OVER (...)` accumulates these flags into an incrementing session number. Grouping gives session-level metrics. Classic product analytics pattern.

---

### 18. Weighted moving average

```sql
SELECT date, price,
  SUM(price * weight) OVER (ORDER BY date ROWS BETWEEN 2 PRECEDING AND CURRENT ROW) /
  SUM(weight)         OVER (ORDER BY date ROWS BETWEEN 2 PRECEDING AND CURRENT ROW) AS wma_3day
FROM (
  SELECT date, price,
    ROW_NUMBER() OVER (ORDER BY date) AS weight
  FROM stock_prices
) weighted;
```

**Explanation:** Assigns increasing weights using `ROW_NUMBER()` and computes a weighted average over a 3-row sliding window. Tests advanced window frame arithmetic used in financial time-series analysis.

---

### 19. Find all pairs with a given sum

```sql
SELECT a.id AS id1, b.id AS id2, a.value + b.value AS total
FROM numbers a
JOIN numbers b
  ON  a.id < b.id
  AND a.value + b.value = 100;
```

**Explanation:** `a.id < b.id` prevents duplicate pairs (A,B) and (B,A) and avoids self-matching. Joining a table to itself and filtering on sum tests self-join reasoning — common in combinatorics and algorithmic SQL problems.

---

### 20. Slowly Changing Dimension (SCD Type 2) — get current record

```sql
-- Option A: NULL end_date marks the current record
SELECT * FROM employee_history
WHERE end_date IS NULL;

-- Option B: effective date range covers today
SELECT * FROM employee_history
WHERE CURRENT_DATE BETWEEN effective_date AND COALESCE(expiry_date, '9999-12-31');
```

**Explanation:** SCD Type 2 stores historical versions of a record with date ranges. The "current" version either has `end_date = NULL` or a date range that covers today. Fundamental concept in data warehousing interviews.

---

## Key Concepts Summary

| Concept | Key Functions / Patterns |
|---|---|
| Window Functions | `ROW_NUMBER`, `RANK`, `DENSE_RANK`, `LAG`, `LEAD`, `SUM OVER` |
| Gaps & Islands | Double `ROW_NUMBER` subtraction trick |
| Hierarchical Data | Recursive CTEs |
| Anti-Join | `LEFT JOIN ... WHERE right.id IS NULL` |
| Pivoting | `CASE WHEN` + `SUM` / `GROUP BY` |
| Sessionization | `LAG` + cumulative `SUM OVER` |
| Change Detection | `FULL OUTER JOIN` + `IS DISTINCT FROM` |
| Streak Detection | Islands & Gaps + result filter |
| Median | `ROW_NUMBER` + middle-row selection |
| SCD Type 2 | Date range or NULL end-date pattern |
