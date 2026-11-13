# SQL Triggers: Automatic Database Actions

## Abstract

A trigger is database code that executes automatically in response to a
specified event, such as an insert, update, or delete. Triggers can
enforce or audit certain rules close to the data, but they can also hide
side effects and complicate application behavior. This paper explains
trigger timing, row-level and statement-level execution, practical
examples, risks, and alternatives.

## 1. What is a trigger?

A trigger is associated with a table, view, or database event. It fires
when the configured event occurs and its condition is satisfied.

Common trigger events include: - `INSERT` - `UPDATE` - `DELETE`

Common timing categories: - `BEFORE`: executes before the triggering
operation is completed. - `AFTER`: executes after the operation. -
`INSTEAD OF`: replaces an operation, commonly used with views in
supporting engines.

Exact trigger capabilities and syntax are database-specific.

## 2. Row-level and statement-level triggers

A row-level trigger executes once for each affected row. A
statement-level trigger executes once for the SQL statement, even if
many rows are affected.

For an update affecting 1,000 rows, a row-level trigger may execute
1,000 times. This can have significant performance implications.

## 3. Audit trigger example

The following is PostgreSQL-style illustrative SQL:

``` sql
CREATE TABLE employee_audit (
    audit_id BIGSERIAL PRIMARY KEY,
    employee_id INT NOT NULL,
    old_salary NUMERIC(12, 2),
    new_salary NUMERIC(12, 2),
    changed_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP
);

CREATE OR REPLACE FUNCTION log_salary_change()
RETURNS TRIGGER AS $$
BEGIN
    IF OLD.salary IS DISTINCT FROM NEW.salary THEN
        INSERT INTO employee_audit
            (employee_id, old_salary, new_salary)
        VALUES
            (OLD.employee_id, OLD.salary, NEW.salary);
    END IF;

    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trg_salary_audit
AFTER UPDATE OF salary ON employees
FOR EACH ROW
EXECUTE FUNCTION log_salary_change();
```

This records old and new salary values when a salary actually changes.
`OLD`, `NEW`, function syntax, and `IS DISTINCT FROM` behavior are
PostgreSQL-specific or have engine-specific equivalents.

## 4. BEFORE trigger example

A `BEFORE INSERT` trigger can normalize or validate values before
storage. For example, an engine may permit setting `NEW.email` to a
normalized value. Whether the trigger can modify `NEW`, and how it
returns a row, depends on the database.

Prefer declarative constraints for rules such as `NOT NULL`, uniqueness,
and simple checks. They are easier for the optimizer and database users
to understand.

## 5. Trigger execution and transactions

Trigger work usually participates in the transaction that caused it. If
the triggering statement or transaction rolls back, trigger changes
generally roll back too, subject to engine-specific behavior and
external side effects.

A trigger that writes to an audit table can therefore provide
transactional auditing. A trigger that calls an external service
introduces failure and consistency problems and is generally a poor
design.

## 6. Uses of triggers

-   Audit selected changes.
-   Maintain derived values when no better declarative mechanism exists.
-   Enforce complex cross-row rules when constraints are insufficient
    and concurrency is handled correctly.
-   Support compatibility behavior during schema migrations.
-   Implement database-local event capture.

## 7. Risks and disadvantages

-   Hidden side effects make application behavior harder to understand.
-   Trigger chains can cause unexpected execution order or recursion.
-   Row-level triggers can slow bulk operations.
-   Debugging and testing become more difficult.
-   Business rules can become duplicated across application code and
    triggers.
-   A trigger may not safely enforce a concurrency-sensitive invariant
    unless locking or isolation is designed correctly.

## 8. Trigger versus constraint versus application logic

  -----------------------------------------------------------------------
  Mechanism                           Best suited for
  ----------------------------------- -----------------------------------
  Constraint                          Declarative row/key/referential
                                      rules

  Trigger                             Automatic database-side action tied
                                      to a data event

  Application logic                   Workflows, user-facing decisions,
                                      external integrations

  Stored procedure                    Explicitly invoked database-side
                                      operation
  -----------------------------------------------------------------------

Use the simplest mechanism that correctly enforces the rule.

## 9. Operational best practices

1.  Name triggers clearly and document their side effects.
2.  Keep trigger code short and deterministic.
3.  Avoid network calls and other external side effects.
4.  Test multi-row statements, rollback behavior, and recursion.
5.  Monitor trigger execution time.
6.  Define how schema migrations update triggers.
7.  Ensure permissions and audit retention meet requirements.

## 10. Conclusion

Triggers can be useful when an action must occur automatically close to
database changes. They should not become a hidden substitute for
application architecture. Prefer constraints for declarative integrity,
explicit application workflows for business processes, and triggers only
when their automatic database-local behavior provides a clear advantage.
