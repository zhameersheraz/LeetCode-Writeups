# 181. Q2 Employees Earning More Than Their Managers

Writeup by: zham

- **Difficulty:** Easy
- **Topic:** SQL, Database
- **Platform:** LeetCode (SQL 50)
- **Language:** MySQL

---

## Description

> **Table: Employee**
>
> | Column Name | Type    |
> |-------------|---------|
> | id          | int     |
> | name        | varchar |
> | salary      | int     |
> | managerId   | int     |
>
> `id` is the primary key (column with unique values) for this table.
> Each row of this table indicates the ID of an employee, their name, salary, and the ID of their manager.
>
> Write a solution to find the **employees who earn more than their managers**.
>
> Return the result table in **any order**.
>
> The result format is in the following example.
>
> **Example 1:**
>
> **Input:**
> Employee table:
>
> | id | name  | salary | managerId |
> |----|-------|--------|-----------|
> | 1  | Joe   | 70000  | 3         |
> | 2  | Henry | 80000  | 4         |
> | 3  | Sam   | 60000  | Null      |
> | 4  | Max   | 90000  | Null      |
>
> **Output:**
>
> | Employee |
> |----------|
> | Joe      |
>
> **Explanation:** Joe is the only employee who earns more than his manager.

---

## How I thought about it

The trick here is that the Employee table points to itself. `managerId` is the same type as `id`, so any row's manager is just another row in the same table.

So I treat the Employee table as two tables: one for employees, one for their managers. Join them on `e.managerId = m.id`, then filter where the employee's salary is higher.

The aliases (`e` for employee, `m` for manager) keep the columns straight. Without them I would have to write `Employee.name` and `Employee.salary` everywhere, and the join condition would not be clear about which side is which.

The top-level people (Sam, Max) have `managerId = Null`, so they drop out of the join. That is fine, the problem only asks for employees who earn more than their manager, and a top-level person does not have a manager to compare against.

---

## Solution

```sql
SELECT e.name AS Employee
FROM Employee e
JOIN Employee m ON e.managerId = m.id
WHERE e.salary > m.salary;
```

---

## Walking through an example

Using the example from the problem:

1. `Employee` has four rows.
2. Self-join on `e.managerId = m.id`:
   - Joe (id=1) joins with Sam (id=3) since Joe's managerId=3
   - Henry (id=2) joins with Max (id=4) since Henry's managerId=4
   - Sam and Max have null managerId, so they do not get joined
3. `WHERE e.salary > m.salary`:
   - Joe's 70000 > Sam's 60000. True. Keep.
   - Henry's 80000 > Max's 90000. False. Drop.
4. Final result: `Joe`.

---

## Cost

- **Time:** O(n^2) in the worst case if the database engine does a naive self-join without an index, but in practice it is much faster because `id` is the primary key. Most engines will use the index on `managerId` if it exists, or fall back to a hash join.
- **Space:** O(k) where k is the number of rows that satisfy the WHERE clause, which is at most n.

---

## What I took away from this one

Self-joins are a thing. When a column in a table points to another row in the same table (the managerId pointing to another employee), the way to compare is to treat the table as two copies and join them. Aliases are the trick that makes the SQL readable.

The `AS Employee` in the SELECT is the column name in the result, which the problem statement asked for. Without it, the column would be named `name` which is too generic. The aliases in the FROM clause and the alias in the SELECT are two different uses of the word alias, and both are needed here.

The follow-up thought: this same pattern works for any "find rows where the value is bigger than some related row" problem. Same table, same primary key, just two aliases and a `WHERE` clause comparing them.
