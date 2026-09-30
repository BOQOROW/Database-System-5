# Week 5 Database Assignment: Indexing and User Access Control

## Overview

This assignment covers dropping an index, creating a MySQL user, granting privileges, and changing a password. All work was done in the **MySQL command-line shell** against the `Sales` database (classicmodels-style sample data).

**Files**

- `answers.sql`: final queries for Q1 to Q4
- `README.md`: this file (queries, explanations, and shell output)

## Final `answers.sql`

```sql
-- Question 1
-- Remove the IdxPhone index from the customers table
DROP INDEX IdxPhone ON customers;

-- Question 2
-- Create user bob, allowed to connect only from localhost
CREATE USER 'bob'@'localhost' IDENTIFIED BY 'S$cu3r3!';

-- Question 3
-- Give bob only the INSERT privilege on the Sales database (least privilege)
GRANT INSERT ON Sales.* TO 'bob'@'localhost';

-- Question 4
-- Change bob's password
ALTER USER 'bob'@'localhost' IDENTIFIED BY 'P$55!23';
```

## How to run

```bash
mysql -u root -p
```

```sql
USE Sales;
```

Then run the statements above one at a time, or run the whole file from the terminal:

```bash
mysql -u root -p Sales < answers.sql
```

## Question 1: Drop the `IdxPhone` index

**Query**

```sql
DROP INDEX IdxPhone ON customers;
```

**Issue encountered.** The first attempt failed because the sample database does not include this index:

```
mysql> USE Sales;
Database changed
mysql> DROP INDEX IdxPhone ON customers;
ERROR 1091 (42000): Can't DROP 'IdxPhone'; check that column/key exists
```

`SHOW INDEX FROM customers;` showed only two indexes, `PRIMARY` (on `customerNumber`) and `salesRepEmployeeNumber`. The `phone` column exists (`varchar(50)`), so the index was created first so that the drop could be demonstrated.

**Setup step (not part of `answers.sql`)**

```
mysql> CREATE INDEX IdxPhone ON customers (phone);
Query OK, 0 rows affected (0.16 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

**Index list after creating it (3 indexes, `IdxPhone` present)**

```
+-----------+------------+------------------------+--------------+------------------------+-----------+-------------+
| Table     | Non_unique | Key_name               | Seq_in_index | Column_name            | Collation | Cardinality |
+-----------+------------+------------------------+--------------+------------------------+-----------+-------------+
| customers |          0 | PRIMARY                |            1 | customerNumber         | A         |         122 |
| customers |          1 | salesRepEmployeeNumber |            1 | salesRepEmployeeNumber | A         |          16 |
| customers |          1 | IdxPhone               |            1 | phone                  | A         |         121 |
+-----------+------------+------------------------+--------------+------------------------+-----------+-------------+
3 rows in set (0.01 sec)
```

**Drop and verify**

```
mysql> DROP INDEX IdxPhone ON customers;
Query OK, 0 rows affected (0.06 sec)
Records: 0  Duplicates: 0  Warnings: 0
```

```
+-----------+------------+------------------------+--------------+------------------------+-----------+-------------+
| Table     | Non_unique | Key_name               | Seq_in_index | Column_name            | Collation | Cardinality |
+-----------+------------+------------------------+--------------+------------------------+-----------+-------------+
| customers |          0 | PRIMARY                |            1 | customerNumber         | A         |         122 |
| customers |          1 | salesRepEmployeeNumber |            1 | salesRepEmployeeNumber | A         |          16 |
+-----------+------------+------------------------+--------------+------------------------+-----------+-------------+
2 rows in set (0.01 sec)
```

**Result:** `IdxPhone` was removed; only `PRIMARY` and `salesRepEmployeeNumber` remain.

## Question 2: Create user `bob` (localhost only)

**Query**

```sql
CREATE USER 'bob'@'localhost' IDENTIFIED BY 'S$cu3r3!';
```

**Output**

```
mysql> CREATE USER 'bob'@'localhost' IDENTIFIED BY 'S$cu3r3!';
Query OK, 0 rows affected (0.05 sec)
```

**Explanation:** Using `'localhost'` as the host means bob can only connect from the same machine, which is a basic access-control measure.

## Question 3: Grant `INSERT` on the database

**Query**

```sql
GRANT INSERT ON Sales.* TO 'bob'@'localhost';
```

**Output**

```
mysql> GRANT INSERT ON Sales.* TO 'bob'@'localhost';
Query OK, 0 rows affected (0.02 sec)
```

**Explanation:** `Sales.*` applies the privilege to every
