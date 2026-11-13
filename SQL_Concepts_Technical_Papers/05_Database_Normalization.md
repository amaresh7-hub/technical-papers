# Database Normalization and Relational Schema Design

## Abstract

Normalization is the process of structuring relational tables to reduce
redundancy and prevent insertion, update, and deletion anomalies. This
paper introduces functional dependencies and the major normal
forms---1NF, 2NF, 3NF, and BCNF---along with decomposition principles,
practical examples, and denormalization trade-offs.

## 1. Why normalization matters

Suppose a table stores student details alongside course details:

  student_id   student_name   course_id   course_name   instructor
  ------------ -------------- ----------- ------------- ------------
  1            Asha           C10         SQL           Ravi
  1            Asha           C20         Networks      Meera
  2            Bala           C10         SQL           Ravi

Student and course facts are repeated. Updating the instructor for C10
requires changing multiple rows. If one row is missed, the table becomes
inconsistent.

These are classic anomalies: - **Update anomaly:** the same fact must be
updated in multiple places. - **Insertion anomaly:** a fact cannot be
stored without an unrelated fact. - **Deletion anomaly:** deleting one
fact accidentally removes another fact.

## 2. Functional dependencies

A functional dependency X → Y means each value of X determines exactly
one value of Y within the relation's rules.

Examples: - `student_id → student_name` -
`course_id → course_name, instructor` -
`(student_id, course_id) → enrollment_date`

A candidate key is a minimal set of attributes that uniquely identifies
a row. A primary key is the candidate key selected as the main
identifier.

## 3. First Normal Form (1NF)

A relation is in 1NF when each attribute contains a single value from
its domain and rows are represented consistently. Avoid storing lists or
repeating groups in one cell.

Poor design: \| student_id \| phone_numbers \| \|---\|---\| \| 1 \|
9876, 8765 \|

Better design:

``` sql
CREATE TABLE student_phones (
    student_id INT NOT NULL,
    phone_number VARCHAR(20) NOT NULL,
    PRIMARY KEY (student_id, phone_number)
);
```

"Atomic" depends on the operations and domain. A full name may be one
value if the system never needs to query its components; it should be
separated if the application needs first and last names independently.

## 4. Second Normal Form (2NF)

A relation is in 2NF if it is in 1NF and every non-prime attribute
depends on the whole candidate key, not merely part of a composite
candidate key.

For an enrollment table with key `(student_id, course_id)`,
`student_name` depends only on `student_id`, while `course_name` depends
only on `course_id`. These are partial dependencies.

Decompose into: - `Students(student_id, student_name)` -
`Courses(course_id, course_name, instructor)` -
`Enrollments(student_id, course_id, enrollment_date)`

If a table's candidate keys are all single-column, it cannot have a
partial dependency on part of a composite key.

## 5. Third Normal Form (3NF)

A relation is in 3NF if it is in 2NF and non-key attributes do not
depend transitively on a candidate key. Formal definition: for every
non-trivial functional dependency X → A, either X is a superkey or A is
a prime attribute.

Example: `employee_id → department_id` and
`department_id → department_name`. Therefore, employee_id transitively
determines department_name. Store department details separately.

``` sql
CREATE TABLE departments (
    department_id INT PRIMARY KEY,
    department_name VARCHAR(100) NOT NULL
);

CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    employee_name VARCHAR(100) NOT NULL,
    department_id INT REFERENCES departments(department_id)
);
```

## 6. Boyce-Codd Normal Form (BCNF)

A relation is in BCNF if every determinant of a non-trivial functional
dependency is a superkey. BCNF is stricter than 3NF.

A relation can satisfy 3NF but fail BCNF when a dependency has a
determinant that is not a superkey and the dependent attribute is prime.
BCNF decomposition may not preserve every functional dependency, so
design requires care.

## 7. Lossless decomposition and dependency preservation

A good decomposition should ideally satisfy: - **Lossless join:**
joining decomposed tables reconstructs exactly the original relation
without spurious rows or lost information. - **Dependency
preservation:** constraints can be checked on decomposed tables without
needing to join them.

Losslessness is essential. Dependency preservation is highly desirable,
but BCNF decomposition does not always preserve all dependencies.

## 8. Higher normal forms

Fourth Normal Form (4NF) addresses non-trivial multivalued dependencies.
Fifth Normal Form (5NF) addresses join dependencies and decompositions
that cannot be further losslessly decomposed. These forms matter in
specialized schemas; most transactional application design focuses on
3NF or BCNF.

## 9. Denormalization

Denormalization intentionally introduces redundancy to improve read
performance or simplify reporting. Examples include: - Cached aggregate
totals. - Precomputed reporting tables. - Materialized views. -
Duplicated descriptive attributes in analytical models.

Denormalization creates consistency responsibilities: the system must
keep duplicated values synchronized. Measure performance before
denormalizing.

## 10. Design workflow

1.  Identify entities and relationships.
2.  Define candidate keys and business uniqueness rules.
3.  List functional dependencies.
4.  Normalize to a suitable target form.
5.  Add primary and foreign key constraints.
6.  Validate with representative inserts, updates, and deletes.
7.  Measure actual query performance.
8.  Denormalize only for a demonstrated requirement.

## 11. Conclusion

Normalization is not an academic exercise; it is a method for preserving
data integrity and reducing maintenance errors. Understand keys and
dependencies, decompose carefully, and treat denormalization as a
measured performance decision rather than a shortcut.
