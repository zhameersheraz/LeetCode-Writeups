# 584. Q4 Find Customer Referee

Writeup by: zham

- **Difficulty:** Easy
- **Topic:** SQL, Database
- **Platform:** LeetCode (SQL 50)
- **Language:** MySQL

---

## Description

> **Table: Customer**
>
> | Column Name | Type    |
> |-------------|---------|
> | id          | int     |
> | name        | varchar |
> | referee_id  | int     |
>
> In SQL, `id` is the primary key column for this table.
> Each row of this table indicates the id of a customer, their name, and the id of the customer who referred them.
>
> Find the names of the customer that are **either**:
>
> 1. referred by any customer with `id != 2`.
> 2. not referred by any customer.
>
> Return the result table in **any order**.
>
> The result format is in the following example.
>
> **Example 1:**
>
> **Input:**
> Customer table:
>
> | id | name | referee_id |
> |----|------|------------|
> | 1  | Will | null       |
> | 2  | Jane | null       |
> | 3  | Alex | 2          |
> | 4  | Bill | null       |
> | 5  | Zack | 1          |
> | 6  | Mark | 2          |
>
> **Output:**
>
> | name |
> |------|
> | Will |
> | Jane |
> | Bill |
> | Zack |

---

## How I thought about it

The first instinct is to write `WHERE referee_id != 2`. That would almost work, but it would also drop customers whose `referee_id` is `NULL`. In SQL, anything compared to `NULL` (using `=`, `!=`, `<`, `>`) returns `NULL`, not `TRUE` or `FALSE`. The row does not pass the WHERE filter.

So I have to handle `NULL` explicitly with `IS NULL`.

The two cases from the problem:
- Referred by someone with id != 2 (`referee_id != 2`)
- Not referred by anyone (`referee_id IS NULL`)

Either one qualifies. So the filter is `referee_id != 2 OR referee_id IS NULL`.

---

## Solution

```sql
SELECT name
FROM Customer
WHERE referee_id != 2 OR referee_id IS NULL;
```

---

## Walking through an example

Using the example from the problem:

1. `referee_id != 2` keeps Will (null), Jane (null), Bill (null), Zack (1). Drops Alex (2), Mark (2).
2. `OR referee_id IS NULL` keeps all of them anyway since they are all null or non-2. (This part does the extra work of making sure null-referee customers stay in.)
3. Final result: Will, Jane, Bill, Zack.

---

## Cost

- **Time:** O(n) where n is the number of customer rows. The filter is a full table scan with a simple condition.
- **Space:** O(k) where k is the number of rows in the result.

---

## What I took away from this one

The `NULL` trap in SQL is real and it caught me the first time. `referee_id != 2` looks like it should match everyone who is not referred by id 2, but a customer with `referee_id = NULL` is exactly that (not referred by id 2), and the row gets dropped. The fix is `IS NULL` to test for the null case explicitly.

The rule to remember: any time a column can be NULL, and the filter says anything about its value, I have to think about whether NULL should be included. If yes, I add `OR column IS NULL` to the WHERE. The `IS NULL` and `IS NOT NULL` are the only valid ways to test for NULL in SQL.

I will run into this same trap in the next several problems on the SQL 50 list. The fix is the same every time: think about the nulls, add `IS NULL` or `IS NOT NULL` when needed.
