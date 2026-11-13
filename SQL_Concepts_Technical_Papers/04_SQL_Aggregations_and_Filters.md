# SQL Aggregations and Query Filtering

## Abstract

Filtering determines which rows participate in a query, while
aggregation summarizes groups of rows. SQL provides `WHERE`, `HAVING`,
`GROUP BY`, aggregate functions, and ordering/limiting clauses to answer
analytical questions. This paper explains their logical order, NULL
behavior, common patterns, and mistakes.

## 1. Filtering rows with WHERE

`WHERE` filters individual rows before grouping.

``` sql
SELECT employee_id, salary
FROM employees
WHERE salary >= 50000
  AND department_id = 10;
```

Predicates can use comparison operators, `AND`, `OR`, `NOT`, `IN`,
`BETWEEN`, `LIKE`, and `IS NULL`.

``` sql
SELECT *
FROM employees
WHERE department_id IN (10, 20)
  AND hire_date >= DATE '2024-01-01';
```

Date literal syntax differs across database engines. Use parameterized
queries in applications rather than concatenating user input into SQL
strings.

## 2. Aggregate functions

Common aggregate functions: - `COUNT(*)`: counts rows. -
`COUNT(column)`: counts non-NULL values in a column. - `SUM(column)`:
adds non-NULL values. - `AVG(column)`: computes the average of non-NULL
values. - `MIN(column)` and `MAX(column)`: smallest and largest non-NULL
values.

``` sql
SELECT
    COUNT(*) AS employee_count,
    AVG(salary) AS average_salary,
    MIN(salary) AS minimum_salary,
    MAX(salary) AS maximum_salary
FROM employees;
```

Most aggregate functions ignore NULL inputs. `COUNT(*)` counts rows even
if individual columns contain NULL.

## 3. GROUP BY

`GROUP BY` partitions rows into groups with equal grouping-key values,
then aggregates each group.

``` sql
SELECT department_id, COUNT(*) AS employee_count,
       AVG(salary) AS average_salary
FROM employees
GROUP BY department_id;
```

Every selected expression should either be aggregated or be valid under
the database's grouping rules. Avoid relying on permissive
engine-specific behavior.

## 4. HAVING

`HAVING` filters groups after aggregation.

``` sql
SELECT department_id, COUNT(*) AS employee_count
FROM employees
GROUP BY department_id
HAVING COUNT(*) >= 5;
```

Remember: - `WHERE`: filters source rows. - `HAVING`: filters grouped
results.

A condition that does not depend on an aggregate should generally be
placed in `WHERE` so fewer rows need to be grouped.

## 5. Logical query processing order

A useful conceptual order is:

1.  `FROM` and `JOIN`
2.  `WHERE`
3.  `GROUP BY`
4.  `HAVING`
5.  `SELECT`
6.  `DISTINCT`
7.  `ORDER BY`
8.  `LIMIT` / `OFFSET` (or equivalent)

This is a logical model, not necessarily the physical execution order
chosen by the optimizer.

## 6. Conditional aggregation

Conditional aggregation calculates multiple metrics in one pass.

``` sql
SELECT
    department_id,
    COUNT(*) AS total_employees,
    SUM(CASE WHEN salary >= 80000 THEN 1 ELSE 0 END) AS high_earners
FROM employees
GROUP BY department_id;
```

Many engines also support aggregate `FILTER` syntax, but portability
varies.

## 7. DISTINCT

`DISTINCT` removes duplicate result rows.

``` sql
SELECT COUNT(DISTINCT department_id)
FROM employees;
```

Use it intentionally. Adding `DISTINCT` to hide duplicate rows caused by
an incorrect join is not a real fix.

## 8. NULL and three-valued logic

SQL conditions can evaluate to TRUE, FALSE, or UNKNOWN. A `WHERE` clause
keeps only TRUE rows.

Incorrect:

``` sql
WHERE manager_id = NULL
```

Correct:

``` sql
WHERE manager_id IS NULL
```

Use `COALESCE(value, fallback)` when a NULL should be represented by a
chosen fallback. Be careful: replacing NULL with zero can change the
meaning of an average or total.

## 9. Practical analytical examples

### Departments with average salary above a threshold

``` sql
SELECT department_id, AVG(salary) AS avg_salary
FROM employees
WHERE status = 'ACTIVE'
GROUP BY department_id
HAVING AVG(salary) > 60000
ORDER BY avg_salary DESC;
```

### Customers with at least three orders

``` sql
SELECT customer_id, COUNT(*) AS order_count
FROM orders
GROUP BY customer_id
HAVING COUNT(*) >= 3;
```

### Monthly sales

``` sql
SELECT EXTRACT(YEAR FROM order_date) AS sales_year,
       EXTRACT(MONTH FROM order_date) AS sales_month,
       SUM(total_amount) AS revenue
FROM orders
GROUP BY EXTRACT(YEAR FROM order_date),
         EXTRACT(MONTH FROM order_date)
ORDER BY sales_year, sales_month;
```

Date extraction functions vary by database. For large tables, consider
date range predicates that can use an index.

## 10. Common mistakes

-   Using `WHERE COUNT(*) > ...` instead of `HAVING`.
-   Selecting ungrouped columns ambiguously.
-   Confusing `COUNT(*)` with `COUNT(column)`.
-   Forgetting that joins may multiply rows before aggregation.
-   Using `DISTINCT` to conceal incorrect join logic.
-   Treating NULL as zero without a business reason.
-   Grouping by formatted date strings when a date expression is more
    appropriate.

## 11. Conclusion

Filtering and aggregation turn detailed records into useful summaries.
Apply row filters early, group by the correct dimensions, use HAVING for
aggregate conditions, and reason carefully about NULLs and join
cardinality.
