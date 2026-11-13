# SQL Joins: Combining Related Data Across Tables

## Abstract

SQL joins combine rows from multiple tables based on a relationship or
matching condition. Joins are central to relational database querying
because normalized schemas store related facts separately. This paper
explains join semantics, common join types, NULL behavior, self joins,
multi-table joins, query pitfalls, and performance considerations.

## 1. Relational background

Suppose a database stores employees and departments separately.

``` sql
CREATE TABLE departments (
    department_id INT PRIMARY KEY,
    department_name VARCHAR(80) NOT NULL
);

CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    department_id INT,
    manager_id INT,
    FOREIGN KEY (department_id) REFERENCES departments(department_id)
);
```

`employees.department_id` references `departments.department_id`. A join
uses this relationship to produce a combined result.

## 2. INNER JOIN

An `INNER JOIN` returns only rows for which the join condition matches.

``` sql
SELECT e.employee_id, e.first_name, d.department_name
FROM employees AS e
INNER JOIN departments AS d
    ON e.department_id = d.department_id;
```

Employees without a matching department are excluded. Departments with
no employees are also excluded.

## 3. LEFT JOIN

A `LEFT JOIN` returns every row from the left table and matching rows
from the right table. If there is no match, right-side columns are
filled with `NULL`.

``` sql
SELECT e.employee_id, e.first_name, d.department_name
FROM employees AS e
LEFT JOIN departments AS d
    ON e.department_id = d.department_id;
```

Use it when the left-side entities must remain in the result, including
those with no related row.

### Finding rows with no match

``` sql
SELECT e.employee_id, e.first_name
FROM employees AS e
LEFT JOIN departments AS d
    ON e.department_id = d.department_id
WHERE d.department_id IS NULL;
```

## 4. RIGHT JOIN and FULL OUTER JOIN

A `RIGHT JOIN` preserves all rows from the right table. It is logically
equivalent to swapping table order and using a `LEFT JOIN`.

A `FULL OUTER JOIN` preserves unmatched rows from both sides, filling
missing columns with `NULL`. Support varies by database engine. MySQL,
for example, does not provide native `FULL OUTER JOIN`; a carefully
constructed `UNION` of left and right unmatched results can emulate it.

## 5. CROSS JOIN

A `CROSS JOIN` produces the Cartesian product: every row from the first
table paired with every row from the second.

If table A has 4 rows and table B has 3 rows, the result has 12 rows. It
is useful for generating combinations, but accidental cross joins can
produce huge results.

``` sql
SELECT s.size_name, c.color_name
FROM sizes AS s
CROSS JOIN colors AS c;
```

## 6. SELF JOIN

A self join joins a table to itself. It is useful for hierarchical or
peer relationships.

``` sql
SELECT
    e.first_name AS employee,
    m.first_name AS manager
FROM employees AS e
LEFT JOIN employees AS m
    ON e.manager_id = m.employee_id;
```

The aliases represent two logical roles for the same physical table.
Read the condition as: "the employee's manager_id matches the manager
row's employee_id."

## 7. Joining more than two tables

``` sql
SELECT
    e.first_name,
    d.department_name,
    l.city
FROM employees AS e
JOIN departments AS d
    ON e.department_id = d.department_id
JOIN locations AS l
    ON d.location_id = l.location_id;
```

Each join adds a relationship. Confirm that each condition connects the
intended keys; joining unrelated columns can silently produce incorrect
combinations.

## 8. ON versus WHERE

`ON` defines how rows match during the join. `WHERE` filters the result
after the join operation's logical row construction.

This difference matters with outer joins:

``` sql
-- Keeps all employees; only matching Sales department rows are attached.
SELECT e.first_name, d.department_name
FROM employees e
LEFT JOIN departments d
    ON e.department_id = d.department_id
   AND d.department_name = 'Sales';

-- Removes employees without a Sales department match.
SELECT e.first_name, d.department_name
FROM employees e
LEFT JOIN departments d
    ON e.department_id = d.department_id
WHERE d.department_name = 'Sales';
```

The second query effectively behaves like an inner join for this
condition because unmatched rows have `NULL`, and `NULL = 'Sales'` is
not true.

## 9. Joining on non-key columns

Joins can use any appropriate predicate, but key-based joins are usually
safer. Joining on names is fragile because names may repeat, change, or
differ in formatting. Prefer stable primary and foreign keys.

## 10. Common errors

-   Missing join condition, creating a Cartesian product.
-   Joining primary key to the wrong foreign key.
-   Forgetting that one-to-many joins multiply rows.
-   Using `COUNT(*)` when counting matched child records is intended.
-   Filtering the right table in `WHERE` and unintentionally removing
    unmatched left rows.
-   Assuming `NULL` equals `NULL`; SQL uses three-valued logic.

## 11. Performance considerations

Index join keys where useful, especially foreign keys and frequently
joined lookup columns. The optimizer may choose nested-loop, hash, or
merge join strategies depending on the engine and data. Inspect
execution plans rather than assuming a particular join algorithm.

## 12. Conclusion

Joins reconstruct related information from normalized tables. Select the
join type based on which unmatched rows must remain, write explicit
predicates, understand row multiplication and NULL behavior, and
validate results with representative data.
