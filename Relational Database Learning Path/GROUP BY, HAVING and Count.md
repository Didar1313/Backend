# Understanding `GROUP BY` and `HAVING` in SQL (with Duplicate Email Example)

## 1. Introduction

In SQL, `GROUP BY` is a powerful clause that lets you group rows that share the same value in one or more columns, and then perform calculations on each group using aggregate functions like `COUNT()`, `SUM()`, or `AVG()`.
When combined with the `HAVING` clause, it becomes a great tool for filtering groups based on those calculated results.

In this article, we’ll explain:

* How `GROUP BY` works
* Why we need `HAVING` instead of `WHERE` for aggregated data
* A real example to find duplicate emails

---

## 2. Example Problem

We have a `Person` table:

| id | email                     |
| -- | ------------------------- |
| 1  | [a@b.com](mailto:a@b.com) |
| 2  | [c@d.com](mailto:c@d.com) |
| 3  | [a@b.com](mailto:a@b.com) |

We want to **find all duplicate emails**.

---

### **What `GROUP BY` does**

It takes your table and **groups together rows that have the same value** in the column(s) you specify.
Then, you can run aggregate functions like `COUNT()`, `SUM()`, `AVG()`, etc. **on each group**.

---

### **Example without GROUP BY**

Your table:

| id | email                     |
| -- | ------------------------- |
| 1  | [a@b.com](mailto:a@b.com) |
| 2  | [c@d.com](mailto:c@d.com) |
| 3  | [a@b.com](mailto:a@b.com) |

If you run:

```sql
SELECT COUNT(email) FROM Person;
```

You’ll get:

```
3
```

Because it’s counting **all** rows together.

---

### **Example with GROUP BY**

Now try:

```sql
SELECT email, COUNT(email)
FROM Person
GROUP BY email;
```

**Step-by-step:**

1. **GROUP BY email** → makes “buckets”:

   * Bucket 1: all rows where email = `a@b.com` → (id 1, id 3)
   * Bucket 2: all rows where email = `c@d.com` → (id 2)

2. Then `COUNT(email)` is calculated **inside each bucket**:

   * For `a@b.com` → count = 2
   * For `c@d.com` → count = 1

**Result:**

| email                     | COUNT(email) |
| ------------------------- | ------------ |
| [a@b.com](mailto:a@b.com) | 2            |
| [c@d.com](mailto:c@d.com) | 1            |

---

### **Why it helps here**

We want emails that appear more than once.
After grouping, we can filter with:

```sql
HAVING COUNT(email) > 1;
```

This keeps only:

| email                     |
| ------------------------- |
| [a@b.com](mailto:a@b.com) |


So 

```sql
SELECT email AS Email
FROM Person
GROUP BY email
HAVING COUNT(email) > 1;
```

---

## 6. Final Output

For our example, this query returns:

| Email                     |
| ------------------------- |
| [a@b.com](mailto:a@b.com) |

Because `a@b.com` appears more than once.

---

## 7. Summary

* `GROUP BY` creates groups of rows that share the same value in specified columns.
* Aggregate functions like `COUNT()` operate on each group.
* Use `HAVING` to filter based on aggregate results, since `WHERE` can only filter raw rows before grouping.
