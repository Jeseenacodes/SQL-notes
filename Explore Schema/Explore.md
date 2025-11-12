# Explore the Schema

##  **1. List All Tables in the Database**

| **Database**               | **Code**                                                                                                                                                         |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **SQL Server / Azure SQL** | `sql SELECT TABLE_SCHEMA, TABLE_NAME FROM INFORMATION_SCHEMA.TABLES WHERE TABLE_TYPE = 'BASE TABLE'; `                                                           |
| **MySQL**                  | `sql SHOW TABLES; `                                                                                                                                              |
| **PostgreSQL**             | `sql SELECT table_schema, table_name FROM information_schema.tables WHERE table_type='BASE TABLE' AND table_schema NOT IN ('pg_catalog','information_schema'); ` |
| **SQLite**                 | `sql SELECT name FROM sqlite_master WHERE type='table'; `                                                                                                        |


## **2. Show Columns in a Specific Table**

| **Database**               | **Code**                                                                                                                                     |
| -------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| **SQL Server / Azure SQL** | `sql SELECT COLUMN_NAME, DATA_TYPE, IS_NULLABLE, CHARACTER_MAXIMUM_LENGTH FROM INFORMATION_SCHEMA.COLUMNS WHERE TABLE_NAME = 'your_table'; ` |
| **MySQL**                  | `sql DESCRIBE your_table; `<br>or<br>`sql SHOW COLUMNS FROM your_table; `                                                                    |
| **PostgreSQL**             | `sql SELECT column_name, data_type, is_nullable FROM information_schema.columns WHERE table_name = 'your_table'; `                           |
| **SQLite**                 | `sql PRAGMA table_info('your_table'); `                                                                                                      |


## **3. Find Primary & Foreign Keys**

| **Database**   | **Code**                                                                                                                                                                                                                                                                 |
| -------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **SQL Server** | `sql SELECT KU.table_name, KU.column_name, KU.constraint_name FROM INFORMATION_SCHEMA.TABLE_CONSTRAINTS AS TC JOIN INFORMATION_SCHEMA.KEY_COLUMN_USAGE AS KU ON TC.CONSTRAINT_NAME = KU.CONSTRAINT_NAME WHERE TC.CONSTRAINT_TYPE = 'PRIMARY KEY'; `                      |
| **MySQL**      | `sql SHOW KEYS FROM your_table WHERE Key_name = 'PRIMARY'; `                                                                                                                                                                                                             |
| **PostgreSQL** | `sql SELECT kcu.table_name, kcu.column_name, tc.constraint_type FROM information_schema.table_constraints AS tc JOIN information_schema.key_column_usage AS kcu ON tc.constraint_name = kcu.constraint_name WHERE tc.constraint_type IN ('PRIMARY KEY','FOREIGN KEY'); ` |

##  **4. Check Row Counts for Each Table**

Helpful to detect empty or very small tables:

```sql
-- Works in most databases
SELECT table_name = t.name, row_count = p.rows
FROM sys.tables AS t
JOIN sys.partitions AS p ON t.object_id = p.object_id
WHERE p.index_id IN (0,1)
ORDER BY row_count DESC;
```

## **5. Get a Quick Data Preview**

See a few rows to understand data patterns:

```sql
SELECT TOP 10 * FROM your_table;   -- SQL Server
SELECT * FROM your_table LIMIT 10; -- MySQL, PostgreSQL, SQLite
```


##  **6. Check Data Types and Null Distribution**

Useful for data validation and cleaning prep:

```sql
SELECT
  COLUMN_NAME,
  DATA_TYPE,
  IS_NULLABLE,
  COUNT(*) AS total_rows,
  SUM(CASE WHEN column_name IS NULL THEN 1 ELSE 0 END) AS null_count
FROM your_table;
```

## **7. Check for Relationships (Joins) Between Tables**

Find possible join columns using matching names:

```sql
SELECT t1.name AS table1, c1.name AS column1, t2.name AS table2, c2.name AS column2
FROM sys.columns c1
JOIN sys.columns c2 ON c1.name = c2.name AND c1.object_id <> c2.object_id
JOIN sys.tables t1 ON c1.object_id = t1.object_id
JOIN sys.tables t2 ON c2.object_id = t2.object_id
ORDER BY c1.name;
```


## **8. Get Database Metadata Summary**

Combine key metadata in one query (SQL Server):

```sql
SELECT 
  t.TABLE_SCHEMA,
  t.TABLE_NAME,
  c.COLUMN_NAME,
  c.DATA_TYPE,
  c.IS_NULLABLE
FROM INFORMATION_SCHEMA.TABLES t
JOIN INFORMATION_SCHEMA.COLUMNS c
  ON t.TABLE_NAME = c.TABLE_NAME
WHERE t.TABLE_TYPE = 'BASE TABLE'
ORDER BY t.TABLE_NAME, c.ORDINAL_POSITION;
```

---

Would you like me to turn all these schema exploration queries into a **single `.sql workbook`** (organized by section + reusable templates)?
It would make your data exploration and cleaning much faster.
