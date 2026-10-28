# Shiptivitas Task 3: SQL Queries

## PART 1: Daily Average Users Before and After the Feature Change

### Query 1A: Daily Active Users Timeline
```sql
SELECT 
    DATE(login_timestamp, 'unixepoch') AS login_date,
    COUNT(DISTINCT user_id) AS daily_active_users,
    CASE 
        WHEN DATE(login_timestamp, 'unixepoch') < '2018-06-02' THEN 'before'
        ELSE 'after'
    END AS period
FROM login_history
GROUP BY login_date
ORDER BY login_date;
```

### Query 1B: Aggregate Summary Comparison (Average DAU)
```sql
WITH daily_dau AS (
    SELECT 
        DATE(login_timestamp, 'unixepoch') AS login_date,
        COUNT(DISTINCT user_id) AS dau
    FROM login_history
    GROUP BY login_date
)
SELECT 
    CASE 
        WHEN login_date < '2018-06-02' THEN 'before'
        ELSE 'after'
    END AS period,
    ROUND(AVG(dau), 2) AS avg_daily_users,
    COUNT(login_date) AS total_days
FROM daily_dau
GROUP BY period;
```

---

## PART 2: Number of Status Changes by Card

### Query 2A: Number of Status Changes by CardID
```sql
SELECT 
    cardID, 
    COUNT(*) AS status_changes
FROM card_change_history
WHERE oldStatus IS NOT NULL AND oldStatus != ''
GROUP BY cardID
ORDER BY status_changes DESC;
```

### Query 2B: Detailed Status Changes Joined with Card Name and Current Status
```sql
SELECT 
    c.id AS card_id,
    c.name AS card_name,
    c.status AS current_status,
    COUNT(ch.id) AS status_changes
FROM card c
LEFT JOIN card_change_history ch 
    ON c.id = ch.cardID 
    AND ch.oldStatus IS NOT NULL 
    AND ch.oldStatus != ''
GROUP BY c.id, c.name, c.status
ORDER BY status_changes DESC, c.id ASC;
```
