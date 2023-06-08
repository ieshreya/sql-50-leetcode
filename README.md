# SQL 50 - LeetCode
Solutions for [SQL 50 Study Plan](https://leetcode.com/studyplan/top-sql-50/) on LeetCode

---

[1757 - Recyclable and Low Fat Products](https://leetcode.com/problems/recyclable-and-low-fat-products/)
```sql
SELECT product_id
FROM Products
WHERE low_fats = 'Y'
AND recyclable = 'Y'
```

[584 - Find Customer Referee](https://leetcode.com/problems/find-customer-referee)
```sql
SELECT name 
FROM Customer 
WHERE referee_id != 2 OR referee_id IS null
```

[595 - Big Countries](https://leetcode.com/problems/big-countries/)
```sql
SELECT name, population, area
FROM WORLD
WHERE area >= 3000000
OR population >= 25000000
```

[1148 - Article Views I](https://leetcode.com/problems/article-views-i)
```sql
SELECT DISTINCT author_id as id
FROM Views
WHERE viewer_id >= 1
AND author_id = viewer_id
ORDER BY author_id
```

[1683 - Invalid Tweets](https://leetcode.com/problems/invalid-tweets/)
```sql
SELECT tweet_id
FROM Tweets
WHERE length(content) > 15
```

[1378 - Replace Employee ID With The Unique Identifier](https://leetcode.com/problems/replace-employee-id-with-the-unique-identifier)
```sql
SELECT unique_id, name
FROM Employees e
LEFT JOIN EmployeeUNI eu
ON e.id = eu.id
```

[1068 - Product Sales Analysis I](https://leetcode.com/problems/product-sales-analysis-i/)
```sql
SELECT product_name, year, price
FROM Sales s
LEFT JOIN Product p
ON s.product_id = p.product_id
```

[1581 - Customer Who Visited but Did Not Make Any Transactions](https://leetcode.com/problems/customer-who-visited-but-did-not-make-any-transactions/)
```sql
SELECT customer_id, COUNT(*) as count_no_trans
FROM Visits 
WHERE visit_id NOT IN (SELECT DISTINCT visit_id FROM Transactions)
GROUP BY customer_id
```

[197 - Rising Temperature](https://leetcode.com/problems/rising-temperature/) 
```sql
SELECT w1.id 
FROM Weather w1, Weather w2
WHERE DATEDIFF(w1.recordDate, w2.recordDate) = 1
AND w1.temperature > w2.temperature

-- OR
SELECT w1.id
FROM Weather w1, Weather w2
WHERE w1.temperature > w2.temperature
AND SUBDATE(w1.recordDate, 1) = w2.recordDate
```

[1661 - Average Time of Process per Machine](https://leetcode.com/problems/average-time-of-process-per-machine/)
```sql
SELECT machine_id, ROUND(AVG(end - start), 3) AS processing_time
FROM 
(SELECT machine_id, process_id, 
    MAX(CASE WHEN activity_type = 'start' THEN timestamp END) AS start,
    MAX(CASE WHEN activity_type = 'end' THEN timestamp END) AS end
 FROM Activity 
  GROUP BY machine_id, process_id) AS subq
GROUP BY machine_id
```

[577 - Employee Bonus](https://leetcode.com/problems/employee-bonus/solutions/)
```sql
SELECT name, bonus
FROM Employee e
LEFT JOIN Bonus b
ON e.empId = b.empId
WHERE bonus < 1000
OR bonus IS NULL
```

[1280 - Students and Examinations](https://leetcode.com/problems/students-and-examinations/)
```sql
SELECT a.student_id, a.student_name, b.subject_name, COUNT(c.subject_name) AS attended_exams
FROM Students a
JOIN Subjects b
LEFT JOIN Examinations c
ON a.student_id = c.student_id
AND b.subject_name = c.subject_name
GROUP BY 1, 3
ORDER BY 1, 3 
```
[570. Managers with at Least 5 Direct Reports](https://leetcode.com/problems/managers-with-at-least-5-direct-reports)
```sql
SELECT name 
FROM Employee 
WHERE id IN
  (SELECT managerId 
   FROM Employee 
   GROUP BY managerId 
   HAVING COUNT(*) >= 5
  )

-- OR
SELECT a.name
FROM Employee a
JOIN Employee b
WHERE a.id = b.managerId
GROUP BY b.managerId
HAVING COUNT(*) >= 5
```

[1934. Confirmation Rate](https://leetcode.com/problems/confirmation-rate/)
```sql
SELECT 
  s.user_id, 
  ROUND(
    COALESCE(
      SUM(
        CASE WHEN ACTION = 'confirmed' THEN 1 END
      ) / COUNT(*), 0),2) 
  AS confirmation_rate 
FROM Signups s 
LEFT JOIN Confirmations c 
ON s.user_id = c.user_id 
GROUP BY s.user_id;  
```

[620. Not Boring Movies](https://leetcode.com/problems/not-boring-movies)
```sql
-- odd id, "boring", rating desc
SELECT *
FROM Cinema
WHERE id % 2 <> 0 
AND description <> "boring"
ORDER BY rating DESC
```

[1251. Average Selling Price](https://leetcode.com/problems/average-selling-price/)
```sql
-- avg(selling), round 2
SELECT p.product_id, 
  ROUND(SUM(price * units) / SUM(units), 2) AS average_price
FROM Prices p
LEFT JOIN UnitsSold s
ON p.product_id = s.product_id
AND purchase_date BETWEEN start_date AND end_date
GROUP BY p.product_id
```

[1075. Project Employees I](https://leetcode.com/problems/project-employees-i)
```sql
-- avg(exp_yr), round 2, by project
SELECT project_id, ROUND(AVG(experience_years), 2) average_years
FROM Project p 
LEFT JOIN Employee e
ON p.employee_id = e.employee_id
GROUP BY project_id
```

[1633. Percentage of Users Attended a Contest](https://leetcode.com/problems/percentage-of-users-attended-a-contest)
```sql
-- % desc, contest_id asc, round 2
SELECT r.contest_id,
       ROUND(COUNT(DISTINCT r.user_id) * 100 / (SELECT COUNT(DISTINCT user_id) FROM Users), 2) AS percentage
FROM Register r
GROUP BY r.contest_id
ORDER BY percentage DESC, r.contest_id ASC;
```

[1211 Queries Quality and Percentage](https://leetcode.com/problems/queries-quality-and-percentage)

```sql
--quality - avg(rating/position), poor query % - %(rating < 3), round 2
SELECT query_name, 
    ROUND(AVG(rating/position), 2) AS quality, 
    ROUND(SUM(IF(rating < 3, 1, 0)) * 100/ COUNT(rating), 2) AS poor_query_percentage
FROM Queries
GROUP BY query_name

-- OR
SELECT query_name, 
    ROUND(AVG(rating/position), 2) AS quality, 
    ROUND(SUM(
        CASE WHEN rating < 3 THEN 1 ELSE 0 END
    ) * 100/ COUNT(rating), 2) AS poor_query_percentage
FROM Queries
GROUP BY query_name
```

[1193. Monthly Transactions I](https://leetcode.com/problems/monthly-transactions-i/)
```sql
-- month, country, count(trans), total(amt), count(approved_trans), total(amt)
SELECT DATE_FORMAT(trans_date, '%Y-%m') month, country, 
        COUNT(state) trans_count, 
        SUM(IF(state = 'approved', 1, 0)) approved_count, 
        SUM(amount) trans_total_amount,
        SUM(IF(state = 'approved', amount, 0)) approved_total_amount
FROM Transactions
GROUP BY 1, 2

-- OR
SELECT DATE_FORMAT(trans_date, '%Y-%m') month, country, 
        COUNT(state) trans_count, 
        SUM(CASE WHEN state = 'approved' THEN 1 ELSE 0 END) approved_count, 
        SUM(amount) trans_total_amount,
        SUM(CASE WHEN state = 'approved' THEN amount ELSE 0 END) approved_total_amount
FROM Transactions
GROUP BY 1, 2
```

[1174. Immediate Food Delivery II](https://leetcode.com/problems/immediate-food-delivery-ii/)
```sql
SELECT
    ROUND((COUNT(CASE WHEN d.order_date = d.customer_pref_delivery_date THEN 1 END) / COUNT(*)) * 100, 2)  immediate_percentage
FROM Delivery d
WHERE d.order_date = (
    SELECT
    MIN(order_date)
    FROM Delivery
    WHERE customer_id = d.customer_id
    );

-- OR
SELECT ROUND(AVG(temp.order_date=temp.customer_pref_delivery_date) * 100, 2) immediate_percentage
FROM (
    SELECT *, RANK() OVER(partition by customer_id ORDER BY order_date) od
    FROM Delivery) temp
WHERE temp.od = 1
```

<!-- [550. Game Play Analysis IV](https://leetcode.com/problems/game-play-analysis-iv/)
```sql

``` -->
