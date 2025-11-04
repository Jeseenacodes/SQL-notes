### SQL Keywords
| Keyword                   | Meaning                               | Example                                     |
| ------------------------- | ------------------------------------- | ------------------------------------------- |
| `SELECT`                  | Choose which columns to return        | `SELECT name, age FROM patients;`           |
| `FROM`                    | Specify the table to pull data from   | `FROM staff`                                |
| `WHERE`                   | Filter rows based on conditions       | `WHERE age > 60`                            |
| `AND` / `OR`              | Combine multiple conditions           | `WHERE age > 60 AND service = 'Cardiology'` |
| `IN`                      | Match against a list of values        | `WHERE service IN ('Emergency', 'Surgery')` |
| `BETWEEN`                 | Filter within a numeric or date range | `WHERE age BETWEEN 18 AND 65`               |
| `LIKE`                    | Search for a pattern (wildcards)      | `WHERE name LIKE 'J%'`                      |
| `IS NULL` / `IS NOT NULL` | Check for missing values              | `WHERE discharge_date IS NULL`              |
| `ORDER BY`                | Sort results by column(s)             | `ORDER BY age DESC`                         |
| `DISTINCT`                | Remove duplicate rows                 | `SELECT DISTINCT service FROM patients;`    |
| `LIMIT`                   | Restrict number of rows returned      | `LIMIT 10`                                  |
