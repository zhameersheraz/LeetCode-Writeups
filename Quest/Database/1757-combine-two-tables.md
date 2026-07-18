# 1757. Q1 Combine Two Tables

Writeup by: zham

- **Difficulty:** Easy
- **Topic:** SQL, Database
- **Platform:** LeetCode (SQL 50)
- **Language:** MySQL

---

## Description

> **Table: Person**
>
> | Column Name | Type    |
> |-------------|---------|
> | personId    | int     |
> | lastName    | varchar |
> | firstName   | varchar |
>
> `personId` is the primary key (column with unique values) for this table.
> This table contains information about the ID of some persons and their first and last names.
>
> **Table: Address**
>
> | Column Name | Type    |
> |-------------|---------|
> | addressId   | int     |
> | personId    | int     |
> | city        | varchar |
> | state       | varchar |
>
> `addressId` is the primary key (column with unique values) for this table.
> Each row of this table contains information about the city and state of one person with ID = PersonId.
>
> Write a solution to report the first name, last name, city, and state of each person in the Person table. If the address of a personId is not present in the Address table, report `null` instead.
>
> Return the result table in **any order**.
>
> The result format is in the following example.
>
> **Example 1:**
>
> **Input:**
> Person table:
>
> | personId | lastName | firstName |
> |----------|----------|-----------|
> | 1        | Wang     | Allen     |
> | 2        | Alice    | Bob       |
>
> Address table:
>
> | addressId | personId | city          | state     |
> |-----------|----------|---------------|-----------|
> | 1         | 2        | New York City | New York  |
> | 2         | 3        | Leetcode      | California|
>
> **Output:**
>
> | firstName | lastName | city          | state     |
> |-----------|----------|---------------|-----------|
> | Allen     | Wang     | null          | null      |
> | Bob       | Alice    | New York City | New York  |
>
> **Explanation:**
> There is no address in the address table for the personId = 1 so we return null in their city and state.
> addressId = 1 contains information about the address of personId = 2.

---

## How I thought about it

The task is "give me every person, and their address if I have one". That is a textbook `LEFT JOIN`. The Person table is the "main" one, the Address table is the optional one.

If I had used an `INNER JOIN` instead, people without an address would disappear from the result. The problem specifically says "if the address of a personId is not present, report null", so the people have to stay. `LEFT JOIN` is the one that does that.

The match key is `personId` on both tables. That is the only column they share.

---

## Solution

```sql
SELECT firstName, lastName, city, state
FROM Person
LEFT JOIN Address ON Person.personId = Address.personId;
```

---

## Walking through an example

Using the example from the problem:

1. `Person` has two rows (Allen, Bob). `Address` has two rows (one for personId=2, one for personId=3, but the second personId=3 is not in Person).
2. `LEFT JOIN` on `personId` matches each person to their address row, if any.
3. Allen (personId=1) has no match in Address, so `city` and `state` come back as `null`.
4. Bob (personId=2) matches, so we get `New York City` and `New York`.
5. The row for personId=3 in Address is dropped because it has no Person row on the left.

Result matches the expected output.

---

## Cost

- **Time:** O(n + m) where n is the size of Person and m is the size of Address. A LEFT JOIN has to scan both tables once.
- **Space:** O(n + m) for the result set, or whatever the database engine needs internally.

---

## What I took away from this one

The mental model for joins is:
- `INNER JOIN` = "only the rows that have matches on both sides"
- `LEFT JOIN` = "every row on the left, plus matches on the right if they exist (otherwise null)"
- `RIGHT JOIN` = mirror of LEFT
- `FULL OUTER JOIN` = "every row from both sides, with nulls where there's no match"

For this problem, the rule "keep every person, fill missing address with null" is exactly `LEFT JOIN`. Once I had that phrase in my head, the rest of the query just followed: pick the columns, set the join condition on the shared key, done.

The other small thing: the `ON` clause goes on a specific column, not just a table. `ON Person.personId = Address.personId` is what tells the engine which rows belong together. Without that, the join would pair every person with every address, which is wrong.
