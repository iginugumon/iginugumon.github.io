---
tags:
  - "#Article"
  - "#Programming"
  - "#SQL"
  - "#Lead"
  - "#Lag"
  - Blog
---
# Lead, Lag Alternative

One of our senior programmers asked for my help regarding composition of a query which compares 2 instances of an item and then output if the end date of the previous instance and the start date of the new instance are different.

So my original approach involves using the Lead() and Lag() functions, which I have tried using when I was still developing in Oracle PL/SQL. I just learned that SQL Server also supports those functions. Here's how I did my original code:


```sql
WITH cte AS
(
SELECT item_id,
instance_no,
start_date,
end_date,
Lag(item_id) OVER ( ORDER BY item_id, instance_no) prev_id,
Lag(instance_no) OVER ( ORDER BY item_id, instance_no) prev_instance,
Lag(end_date) OVER ( ORDER BY item_id, instance_no) prev_end_date
FROM item_table) tbl AS
(
SELECT item_id,
instance_no,
start_date,
end_date,
prev_instance
FROM cte
WHERE item_id = prev_id
AND start_date <> prev_end_date)
SELECT item_id,
instance_no,
start_date,
end_date
FROM tbl
UNION ALL
SELECT cte.item_id,
cte.instance_no,
cte.start_date,
cte.end_date
FROM cte
JOIN tbl
ON (
cte.item_id = tbl.item_id
AND cte.instance_no = tbl.prev_instance)
```


Then I tried running my script. I got an error:

	`"'Lag' is not a r_
	It turned out that the server our company is using is 2008 R2. And the support for those analytic functions started on the later versions. So had to revise.

Here's the new script, sans the Lead and Lag functions:

```sql
WITH cte AS
(
SELECT cur.item_id,
cur.instance_no,
cur.start_date,
cur.end_date,
prev.item_id prev_id,
prev.instance_no prev_instance,
prev.end_date prev_end_date
FROM item_table cur
JOIN item_table prev
ON (
cur.item_id = prev.item_id
AND cur.instance_no = (prev.instance_no + 1)) ) tbl AS
(
SELECT item_id,
instance_no,
start_date,
end_date,
prev_instance
FROM cte
WHERE item_id = prev_id
AND start_date <> prev_end_date)
SELECT item_id,
instance_no,
start_date,
end_date
FROM tbl
UNION ALL
SELECT cte.item_id,
cte.instance_no,
cte.start_date,
cte.end_date
FROM cte
JOIN tbl
ON (
cte.item_id = tbl.item_id
AND cte.instance_no = tbl.prev_instance)
```
