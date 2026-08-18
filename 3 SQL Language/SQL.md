# What is SQL?

**SQL stands for Structured Query Language** and is a programming language used to **manage and manipulate relational databases**. It is used to store data, retrieve data, update data, delete data, and manage database structure.

SQL works with **Relational Database Management Systems RDBMS** such as:
- MySQL
- PostgreSQL
- SQLite
- Microsoft SQL Server
- Oracle

A relational database stores data in **tables** which consist of **rows** and **columns**. These tables can be connected using **relationships**.

The SQL Language is divided into categories.
- Data Definition Language DDL: Used to define database structure. Consists of `CREATE`, `ALTER`, and `DROP`.
- Data Manipulation Language DML: Used to manipulate data inside tables. Consists of `INSERT`, `UPDATE`, and `DELLETE`.
- Data Query Language DQL: Used to retrieve data. Consists of `SELECT`.
- Data Control Language DCL: Used for permissions. Consists of `GRANT` and `REVOKE`.

---

## Select

The `SELECT` clause is used to select specific columns from tables.

Select the column from the table, if column is not in the table, a new one will be created.
```sql
SELECT col
```

Select several columns from the table.
```sql
SELECT col1, col2, col3
```

Select all columns from the table.
```sql
SELECT *
```

Select the column from the table and removes duplicate rows.
```sql
SELECT DISTINCT col
```

Select the column and changes its name to the specified name. The column can now be called using the new name.
```sql
SELECT col AS new_name
```

Select two columns and combines them using the string into one column.
```sql
SELECT col1 || 'string' || col2
```

Select Operators
```sql
SELECT AVG(col)
SELECT MAX(col)
SELECT MIN(col)
SELECT LOWER(col)
SELECT UPPER(col)
SELECT SUM(col)
```

Count Operator.  Returns the number of rows of the column.
```sql
SELECT COUNT(col)
```

Substring Operator. Returns each string in each row modified.
```sql
SELECT SUBSTRING(col, start, length);
```

For example. This extracts the first 3 letters of each student's name.
```sql
SELECT SUBSTRING(name, 1, 3)
FROM Students;
```

Quotations matter in SQL.
- Single `''` Used for strings and dates
- Double `""` Used for table names and aliases with spaces or special characters

---

## From

The `FROM` clause is used to tell the `SELECT` clause which table to select the columns from.

Select from one table.
```sql
FROM table1
```

Or if you are working in a database that has **multiple schemas**.
```sql
FROM schema.table1
```


Sometimes you will need to select from multiple tables so you will need to create an implicit join.
```sql
FROM table1, table2
```

Or create an explicit join.
```sql
FROM table1 JOIN table2 ON table1_col = table2_col
```

We will focus on explicit joins, lets start with an `INNER JOIN` or `JOIN`.
```sql
SELECT col1, col2, col3
FROM table1
JOIN table2
ON table1_col = table2_col
```

An `INNER JOIN`:
- Selects common rows.
- Deletes uncommon rows.

Moving on to a `LEFT OUTER JOIN` or `LEFT JOIN`.
```sql
SELECT col1, col2, col3
FROM table1
LEFT JOIN table2
ON table1_col = table2_col
```

A `LEFT OUTER JOIN`:
- Selects everything from the left table `table1` but only selects matches from the right table `table2`.
- Empty rows from the right table `table2` are filled with `NULL`.
- Deletes right table `table2` rows that don't match.

A `RIGHT OUTER JOIN` or `RIGHT JOIN` is exactly the opposite of a `LEFT OUTER JOIN`.
```sql
```sql
SELECT col1, col2, col3
FROM table1
RIGHT JOIN table2
ON table1_col = table2_col
```

A `RIGHT OUTER JOIN`:
- Selects everything from the right table `table2` but only selects matches from the left table `table21.
- Empty rows from the left table `table1` are filled with `NULL`.
- Deletes left table `table1` rows that don't match.

Finally the `FULL JOIN`.
```sql
SELECT col1, col2, col3
FROM table1
FULL JOIN table2
ON table1_col = table2_col
```

A `FULL JOIN`:
- Selects everything from both tables.
- Empty rows from both tables are filled with NULL.
- Deletes Nothing.

---

## Where

The `WHERE` clause applies after the `SELECT` clause to apply conditions like a filter.

Apply a basic condition. Can be used with `=`, `<`, `>`, `<=`, `>=`, or `!=`.
```sql
WHERE condition
```

Apply many conditions.
```sql
WHERE condition AND condition
WHERE condition OR condition
```

Return only null rows from a column.
```sql
WHERE col IS NULL
```

Return only full rows from a column.
```sql
WHERE col IS NOT NULL
```

Return rows between both numbers.
```sql
WHERE col BETWEEN int AND int
```

Returns rows with strings that match the string.
```sql
WHERE col LIKE "string"
```

For example. This will return all rows containing "Je" like "Jerry" and "Jess". This is like saying "Je%".
```sql
WHERE col LIKE "Je"
```

Like Operators.
- `%` Matches **any number of characters** including zero.
- `_` Matches **exactly one character**.

---

## Group By

The `GROUP BY` clause rolls up all duplicate rows into one making it easier to work with `SELECT` operators like `AVG` and `MAX`.

Group By one column.
```sql
GROUP BY col
```

Group By several columns. Groups rows that are the same in **both** columns.
```sql
GROUP BY col1, col2, col3
```

---

## Having

The `HAVING` clause applies after the `GROUP BY` clause.

Apply a basic condition. Can be used with `=`, `<`, `>`, `<=`, `>=`, or `!=`.
```sql
WHERE condition
```

Apply many conditions.
```sql
WHERE condition AND condition
WHERE condition OR condition
```

---

## Order By

The `ORDER BY` clause is used to sort the rows of columns.

Sort ascending.
```sql
ORDER BY col
ORDER BY col ASC
```

Sort descending.
```sql
ORDER BY col DESC
```

---

## Limit

The `LIMIT` clause specifies **the maximum number of rows** that should be returned.

Return only a specific number of rows starting from the top of the table.
```sql
LIMIT int
```

Return only a specific number of rows throughout the table. Skips the first number specifies and returns the number of rows the second number specifies.
```sql
LIMIT skip, count
```

For example. This skips the first 2 rows and then returns 3 rows, so it returns rows 3, 4, and 5.
```sql
SELECT *
FROM Students
LIMIT 2, 3;
```

You can also use this syntax which is specific to PostgreSQL. It does the same thing.
```sql
LIMIT count OFFSET start
```

---

## Privileges

Grant a user privileges.
```sql
GRANT privilege ON schema TO user
GRANT privilege ON table TO user
```

Revoke a user privileges.
```sql
REVOKE privilege ON schema TO user
RWVOKE privilege ON table TO user
```

Privileges:
- `ALL` All rights except drop.
- `SELECT` Rights to select.
- `UPDATE` Rights to update columns, needs `SELECT`.
- `DELETE` Rights to delete rows, needs `SELECT`.
- `CREATE` Rights to create a schema or table.
- `USAGE` Rights to access a schema.

---

## Execution Order

Even though we write queries in this order.
```sql
SELECT name, age
FROM Students
WHERE age > 18
GROUP BY age
HAVING COUNT(*) > 1
ORDER BY name
LIMIT 5;
```

**The database executes it in a different logical order**.
1. **FROM** → determines which tables to use, joins them together.
2. **WHERE** → filters rows **before any grouping.**
3. GROUP BY** → groups the filtered rows.
4. **HAVING** → filters groups (like WHERE but for aggregated groups).
5. SELECT** → chooses which columns (or expressions) to return.
6. DISTINCT** → removes duplicate rows (if used).
7. ORDER BY** → sorts the result.
8. LIMIT / OFFSET** → restricts how many rows are returned.

---
