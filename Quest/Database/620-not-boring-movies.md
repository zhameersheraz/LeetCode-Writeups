# 620. Q3 Not Boring Movies

Writeup by: zham

- **Difficulty:** Easy
- **Topic:** SQL, Database
- **Platform:** LeetCode (SQL 50)
- **Language:** MySQL

---

## Description

> **Table: Cinema**
>
> | Column Name  | Type     |
> |--------------|----------|
> | id           | int      |
> | movie        | varchar  |
> | description  | varchar  |
> | rating       | float    |
>
> `id` is the primary key (column with unique values) for this table.
> Each row contains information about the name of a movie, its genre, and its rating.
> `rating` is a 2 decimal places float in the range `[0, 10]`
>
> Write a solution to report the movies with an **odd-numbered ID** and a description that is **not "boring"**.
>
> Return the result table **ordered by rating in descending order**.
>
> The result format is in the following example.
>
> **Example 1:**
>
> **Input:**
> Cinema table:
>
> | id | movie      | description | rating |
> |----|------------|-------------|--------|
> | 1  | War        | great 3D    | 8.9    |
> | 2  | Science    | fiction     | 8.5    |
> | 3  | irish      | boring      | 6.2    |
> | 4  | Ice song   | Fantacy     | 8.6    |
> | 5  | House card | Interesting | 9.1    |
>
> **Output:**
>
> | id | movie      | description | rating |
> |----|------------|-------------|--------|
> | 5  | House card | Interesting | 9.1    |
> | 1  | War        | great 3D    | 8.9    |
>
> **Explanation:** We have three movies with odd-numbered IDs: 1, 3, and 5. The movie with ID = 3 is boring so we do not include it in the answer.

---

## How I thought about it

Three conditions, all in one filter:
- odd-numbered id
- description is not "boring"
- sort by rating, highest first

`id % 2 = 1` is the standard way to test for odd in SQL. The `%` is modulo. The result is 0 for even, 1 for odd.

`description != 'boring'` filters by string. Strings in SQL go in single quotes, not double quotes. Double quotes are for identifiers like column names in some dialects, single quotes are for string values.

`ORDER BY rating DESC` puts the highest rating at the top. `DESC` is the keyword for descending order.

`SELECT *` works here because the output format is the same as the input. If the problem asked for specific columns, I would have listed them by name instead.

---

## Solution

```sql
SELECT *
FROM Cinema
WHERE id % 2 = 1
  AND description != 'boring'
ORDER BY rating DESC;
```

---

## Walking through an example

Using the example from the problem:

1. `id % 2 = 1` keeps rows with id 1, 3, 5 (skipping 2 and 4).
2. `description != 'boring'` drops id 3 (irish has description "boring"). Now we have id 1 (War, 8.9) and id 5 (House card, 9.1).
3. `ORDER BY rating DESC` puts House card (9.1) before War (8.9).
4. Final result matches the expected output.

---

## Cost

- **Time:** O(n log n) where n is the number of rows. The filter is O(n), the sort is O(n log n).
- **Space:** O(n) for the result set returned to the user.

---

## What I took away from this one

The `%` (modulo) operator in SQL is the same as in C, C++, Python, Java, and most languages. It is the right tool for "is this number even or odd" without having to think about the division. `id % 2 = 0` for even, `id % 2 = 1` for odd.

The combination of `WHERE` conditions uses `AND` to require both. If the problem had said "either odd id OR not boring description", I would have used `OR` instead. The two are the boolean connectors in SQL, same as in code.

I also noticed the problem does not ask for a specific column list, so `SELECT *` is appropriate. In a real query, I would usually write out the columns I want, both for clarity and so the output does not break if a column gets added later. For a quick LeetCode answer, `SELECT *` is fine.
