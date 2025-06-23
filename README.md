# 🧑‍💻 User Performance Analysis - SQL Project

## 📌 Overview

This project focuses on analyzing user submission data from an online learning platform using SQL. The dataset captures information like user submissions, earned points, submission timestamps, and usernames. The goal is to derive insights on user performance, ranking, and behavior patterns.

---

## 🎯 Objectives

- Analyze user behavior and performance over time.
- Use SQL to perform aggregation, filtering, ranking, and time-based grouping.
- Solve practical data questions often faced in analytics roles.
- Strengthen hands-on experience with SQL functions like `COUNT`, `SUM`, `AVG`, `EXTRACT()`, and `DENSE_RANK()`.

---

## 📊 Dataset Description

The dataset used (`user_submissions`) contains the following fields:

| Column Name         | Description                                |
|---------------------|--------------------------------------------|
| `user_id`           | Unique identifier for each user            |
| `question_id`       | Identifier for the question attempted      |
| `points`            | Points earned in each submission           |
| `submitted_at`      | Timestamp of submission                    |
| `username`          | User’s display name                        |

---

## 💡 SQL Questions Solved

### Q1: List All Distinct Users and Their Stats
```sql
SELECT 
    username,
    COUNT(id) AS total_submissions,
    SUM(points) AS points_earned
FROM user_submissions
GROUP BY username
ORDER BY total_submissions DESC;
```

### Q2: Calculate the Daily Average Points for Each User
```sql
SELECT 
    TO_CHAR(submitted_at, 'DD-MM') AS day,
    username,
    AVG(points) AS daily_avg_points
FROM user_submissions
GROUP BY 1, 2
ORDER BY username;
```

### Q3: Find the Top 3 Users with the Most Correct Submissions for Each Day
```sql
WITH daily_submissions AS (
    SELECT 
        TO_CHAR(submitted_at, 'DD-MM') AS daily,
        username,
        SUM(CASE WHEN points > 0 THEN 1 ELSE 0 END) AS correct_submissions
    FROM user_submissions
    GROUP BY 1, 2
),
users_rank AS (
    SELECT 
        daily,
        username,
        correct_submissions,
        DENSE_RANK() OVER(PARTITION BY daily ORDER BY correct_submissions DESC) AS rank
    FROM daily_submissions
)
SELECT 
    daily,
    username,
    correct_submissions
FROM users_rank
WHERE rank <= 3;
```

### Q4: Find the Top 5 Users with the Highest Number of Incorrect Submissions
```sql
SELECT 
    username,
    SUM(CASE WHEN points < 0 THEN 1 ELSE 0 END) AS incorrect_submissions,
    SUM(CASE WHEN points > 0 THEN 1 ELSE 0 END) AS correct_submissions,
    SUM(CASE WHEN points < 0 THEN points ELSE 0 END) AS incorrect_submissions_points,
    SUM(CASE WHEN points > 0 THEN points ELSE 0 END) AS correct_submissions_points_earned,
    SUM(points) AS points_earned
FROM user_submissions
GROUP BY 1
ORDER BY incorrect_submissions DESC;
```

### Q5: Find the Top 10 Performers for Each Week
```sql
SELECT *  
FROM (
    SELECT 
        EXTRACT(WEEK FROM submitted_at) AS week_no,
        username,
        SUM(points) AS total_points_earned,
        DENSE_RANK() OVER(PARTITION BY EXTRACT(WEEK FROM submitted_at) ORDER BY SUM(points) DESC) AS rank
    FROM user_submissions
    GROUP BY 1, 2
    ORDER BY week_no, total_points_earned DESC
)
WHERE rank <= 10;
```
### 🧠  Key SQL Concepts Used
COUNT(), SUM(), AVG() for aggregation

CASE WHEN for conditional logic

EXTRACT() and TO_CHAR() for date manipulation

DENSE_RANK() for ranking

GROUP BY and CTEs for structured query logic

### 🛠 Project Level
Beginner — ideal for those looking to practice real-world SQL data problems and performance analysis.


