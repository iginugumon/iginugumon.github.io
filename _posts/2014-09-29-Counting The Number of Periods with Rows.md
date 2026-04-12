---
tags:
  - "#Article"
  - "#Programming"
  - "#SQL"
  - "#Count"
  - Legacy-Coder
  - Blog
Date Created: 2014-09-29
---
# Counting The Number of Periods with Rows

A project was developed by my teammates needed to be tested. Since our company is a bit scrimping on hiring testers and QA's -- which I highly discourage because using your programmer as tester / QA actually costs more.

Going back to the project, they have developed a facility that counts the number of weeks that has entries. During testing, I have created my own version of the function by referring to the specs:

\- A week can have multiple entries but it will still be counted as one.
\- An entry will have effective dates. It will then have its own start and end dates.
\- An entry can overlap to multiple weeks. Every week that's been covered will be included in the count.

First, I had to determine the start date and end date of the weeks I need to count. Let's say I had to get the values from the parameter table:

```sql
DECLARE @start_date DATETIME
DECLARE @end_date DATETIME

SELECT @start_date = start_date, @end_date end_date
FROM parameter_table
WHERE <insert conditions here>
```


Then I need a week counter, which I'll be incrementing whenever there's an entry for the week in the current iteration.

```sql
DECLARE @week_count INT
SET @week_count = 0
```


And I also need a date pointer, which initially points to the start date of the current iteration. Its initial value will be the

```sql
DECLARE @date_pointer DATETIME

SET @date_pointer = @start_date
```


Now, we need to determine if there are entries for every week. This can be achieved using the `Count()` function. I'll be using a variable as its temporary storage:

```sql
DECLARE @entry_count INT

WHILE @date_pointer <= @end_date
BEGIN
SELECT @entry_count = Count(1)
FROM entry_table
WHERE start_date BETWEEN @date_pointer AND ( @date_pointer + 6 )
AND <insert additional conditions here>

IF @entry_count > 0
BEGIN
SET @week_count = @week_count + 1
END

SET @date_pointer = @date_pointer + 7
END
```


The final value of `@week_count` will be the value that we need.
