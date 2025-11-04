##  What Is a JOIN?

A **JOIN** combines data from **two or more tables** based on a related column — usually a **key** like `id`, `customer_id`, `dept_id`, etc.

Think of it like connecting puzzle pieces that fit together based on matching values.

---

## **1️⃣ INNER JOIN**

**Definition:**
Returns only the rows that have **matching values** in both tables.

**Example:**

```sql
SELECT 
  patients.name, doctors.name AS doctor_name
FROM patients
INNER JOIN doctors
ON patients.doctor_id = doctors.id;
```
You’ll only see patients who have a matching doctor in the doctors table.

 **Think:** “Only matches in both tables.”

| patients               | doctors                | ✅ result = only where doctor_id matches |
| ---------------------- | ---------------------- | --------------------------------------- |
| patient A, doctor_id=1 | doctor_id=1, Dr. Smith | ✔                                       |
| patient B, doctor_id=2 | doctor_id=2, Dr. John  | ✔                                       |
| patient C, doctor_id=3 | doctor_id=4, Dr. Amy   | ❌ (no match)                            |

---

## **2️⃣ LEFT JOIN** (or **LEFT OUTER JOIN**)

**Definition:**
Takes **all rows from the left table**, and matches from the right table.
If there’s **no match**, it fills with **NULL**.

**Example:**

```sql
SELECT 
  patients.name, doctors.name AS doctor_name
FROM patients
LEFT JOIN doctors
ON patients.doctor_id = doctors.id;
```

 You get **all patients**, even if some don’t have a doctor assigned.

 **Think:** “Keep everything from the left table, fill blanks if missing.”

---

## **3️⃣ RIGHT JOIN** (or **RIGHT OUTER JOIN**)

**Definition:**
Takes **all rows from the right table**, and matches from the left table.
If no match, left side gets **NULL**.

**Example:**

```sql
SELECT 
  patients.name, doctors.name AS doctor_name
FROM patients
RIGHT JOIN doctors
ON patients.doctor_id = doctors.id;
```

You get **all doctors**, even if they don’t have any patients.

 **Think:** “Keep everything from the right table.”

---

## **4️⃣ FULL JOIN** (or **FULL OUTER JOIN**)

**Definition:**
Combines **LEFT JOIN + RIGHT JOIN** — returns **all rows** from both tables.
If something doesn’t match, you still see it, with NULLs filled in.

**Example:**

```sql
SELECT 
  patients.name, doctors.name AS doctor_name
FROM patients
FULL JOIN doctors
ON patients.doctor_id = doctors.id;
```

 You’ll see all patients and all doctors, even if they don’t match.

 **Think:** “Show everything from both sides.”

---

## **5️⃣ CROSS JOIN**

**Definition:**
Gives **every combination** of rows from both tables (no condition).
If one table has 3 rows and the other has 2 → result = 3×2 = 6 rows.

**Example:**

```sql
SELECT p.name, d.name AS doctor_name
FROM patients p
CROSS JOIN doctors d;
```
 Every patient gets paired with every doctor.

**Think:** “All possible combinations.”

---

##  Summary Table

| Join Type      | Returns                  | Missing Data                | Simple Meaning             |
| -------------- | ------------------------ | --------------------------- | -------------------------- |
| **INNER JOIN** | Only matching rows       | Excluded                    | Common between both tables |
| **LEFT JOIN**  | All from left + matches  | Right side NULL if no match | Keep all from left         |
| **RIGHT JOIN** | All from right + matches | Left side NULL if no match  | Keep all from right        |
| **FULL JOIN**  | All from both            | NULL where no match         | Everything, matched or not |
| **CROSS JOIN** | All combinations         | None                        | Every possible pair        |
