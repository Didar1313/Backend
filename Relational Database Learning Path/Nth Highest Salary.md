
# 🏆 MySQL Function: Get Nth Highest Salary

This project demonstrates how to create a **MySQL stored function** to retrieve the **Nth highest distinct salary** from an `Employee` table.  
If the Nth highest salary does not exist (i.e., there are fewer than `N` distinct salaries), the function returns `NULL`.

---

## 📌 Problem Statement

**Table:** `Employee`

| Column Name | Type |
|-------------|------|
| id          | int  |
| salary      | int  |

- `id` is the primary key.
- Each row stores the salary of one employee.

**Goal:**  
Create a function:

```sql
getNthHighestSalary(N INT) RETURNS INT
````

* Returns the **Nth highest distinct salary**.
* Returns `NULL` if there are fewer than `N` distinct salaries.

---

## 📜 Example

**Input:**

Employee table:

| id | salary |
| -- | ------ |
| 1  | 100    |
| 2  | 200    |
| 3  | 300    |

**Query:**

```sql
SELECT getNthHighestSalary(2);
```

**Output:**

| getNthHighestSalary(2) |
| ---------------------- |
| 200                    |

---

**Another Example:**

Employee table:

| id | salary |
| -- | ------ |
| 1  | 100    |

**Query:**

```sql
SELECT getNthHighestSalary(2);
```

**Output:**

| getNthHighestSalary(2) |
| ---------------------- |
| NULL                   |

---

## 🛠 SQL Function Implementation

```sql
DELIMITER $$

CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN
    DECLARE offsetVal INT;
    DECLARE result INT;

    -- Calculate OFFSET value
    SET offsetVal = N - 1;

    -- Get the Nth highest distinct salary
    SET result = (
        SELECT DISTINCT Salary
        FROM Employee
        ORDER BY Salary DESC
        LIMIT 1 OFFSET offsetVal
    );

    RETURN result;
END$$

DELIMITER ;
```

---

## 🔍 How It Works

1. **`DISTINCT`**
   Removes duplicate salaries to ensure only unique values are considered.

2. **`ORDER BY Salary DESC`**
   Sorts salaries from highest to lowest.

3. **`OFFSET`**
   Skips the first `(N - 1)` salaries to reach the Nth one.
   Example:

   * `N = 1` → `OFFSET 0` → highest salary
   * `N = 2` → `OFFSET 1` → second highest salary
   * `N = 3` → `OFFSET 2` → third highest salary

4. **`LIMIT 1`**
   Returns exactly **one** result — the salary at that position.

5. **`NULL` case**
   If there are fewer than `N` salaries, the query returns `NULL`.

---

## 🎯 Usage

```sql
SELECT getNthHighestSalary(1); -- Returns highest salary
SELECT getNthHighestSalary(2); -- Returns 2nd highest salary
SELECT getNthHighestSalary(5); -- Returns NULL if <5 distinct salaries
```

---

## 💡 Notes

* MySQL does **not** allow expressions directly in `OFFSET` (e.g., `OFFSET N-1`), so we store it in a variable first.
* Works on MySQL 5.x and 8.x.
* On MySQL 8+, you could also use **`ROW_NUMBER()`** for an alternative approach.


