# SOLID Principles in Python

## Detailed Technical Paper and Exam Reference

> **Purpose:** This paper is designed for open-book examinations and
> interview revision. It explains each SOLID principle, the design
> problem it addresses, incorrect and improved Python examples,
> practical benefits, comparisons, common mistakes, and likely exam
> questions.

------------------------------------------------------------------------

## Table of Contents

1.  Introduction to SOLID
2.  Overview of the Five Principles
3.  Single Responsibility Principle (SRP)
4.  Open/Closed Principle (OCP)
5.  Liskov Substitution Principle (LSP)
6.  Interface Segregation Principle (ISP)
7.  Dependency Inversion Principle (DIP)
8.  Dependency Injection
9.  How the SOLID Principles Work Together
10. SOLID in Real-World Applications
11. Advantages and Limitations
12. Common Mistakes
13. Exam Questions and Quick Revision

------------------------------------------------------------------------

# 1. Introduction to SOLID

### 1.1 What is SOLID?

**SOLID** is an acronym for five object-oriented software design
principles. They guide developers in organizing classes and dependencies
so that software is easier to understand, maintain, test, and extend.

The five principles are:

-   **S** -- Single Responsibility Principle
-   **O** -- Open/Closed Principle
-   **L** -- Liskov Substitution Principle
-   **I** -- Interface Segregation Principle
-   **D** -- Dependency Inversion Principle

The acronym was popularized by Robert C. Martin, commonly known as Uncle
Bob.

### 1.2 Why are SOLID principles needed?

As applications grow, poorly designed code can develop problems such
as: - Classes doing too many unrelated tasks. - Changes in one feature
breaking another feature. - Large conditional statements that must be
edited for every new feature. - Child classes that cannot safely replace
their parent classes. - Classes depending directly on specific
implementations. - Difficult unit testing due to tightly coupled
components.

SOLID provides design guidance to reduce these problems.

### 1.3 Overview table

  -----------------------------------------------------------------------
  Letter                  Principle               Main idea
  ----------------------- ----------------------- -----------------------
  S                       Single Responsibility   A class should have one
                          Principle               reason to change.

  O                       Open/Closed Principle   Open for extension,
                                                  closed for
                                                  modification.

  L                       Liskov Substitution     Subtypes should be
                          Principle               substitutable for their
                                                  base types.

  I                       Interface Segregation   Prefer small, specific
                          Principle               interfaces.

  D                       Dependency Inversion    Depend on abstractions,
                          Principle               not concrete
                                                  implementations.
  -----------------------------------------------------------------------

------------------------------------------------------------------------

# 2. Single Responsibility Principle (SRP)

## 2.1 Definition

**A class should have one reason to change.**

This means a class should have one focused responsibility. It should not
combine unrelated operations that change for different reasons.

The principle does not mean a class can have only one method. A class
may contain several methods as long as they support the same
responsibility.

## 2.2 Why is SRP needed?

When a single class handles multiple responsibilities: - A change to one
responsibility can affect unrelated behavior. - The class becomes harder
to test. - The class becomes harder to understand. - Different parts of
the application become unnecessarily dependent on it.

## 2.3 Problematic implementation

``` python
class Employee:
    def __init__(self, name, salary):
        self.name = name
        self.salary = salary

    def calculate_salary(self):
        return self.salary

    def save_to_database(self):
        print("Saving employee to database")

    def generate_report(self):
        print("Generating employee report")
```

### What is wrong?

The class handles: 1. Employee information. 2. Salary calculation. 3.
Database persistence. 4. Report generation.

These responsibilities may change independently. Therefore, the class
has multiple reasons to change.

## 2.4 Improved implementation

``` python
class Employee:
    def __init__(self, name, salary):
        self.name = name
        self.salary = salary


class SalaryCalculator:
    def calculate(self, employee):
        return employee.salary


class EmployeeRepository:
    def save(self, employee):
        print("Saving employee to database")


class EmployeeReport:
    def generate(self, employee):
        print("Generating employee report")
```

Now each class has one focused responsibility.

## 2.5 Real-world example

In a banking application: - `BankAccount` manages account data and
account operations. - `TransactionRepository` stores transaction
records. - `StatementGenerator` generates account statements. -
`NotificationService` sends transaction notifications.

Keeping these responsibilities separate makes each component easier to
modify and test.

## 2.6 Benefits

-   Better readability.
-   Easier testing.
-   Reduced coupling.
-   More focused classes.
-   Changes are localized to the responsible component.

**Exam keyword:** One reason to change.

------------------------------------------------------------------------

# 3. Open/Closed Principle (OCP)

## 3.1 Definition

**Software entities should be open for extension but closed for
modification.**

A class should allow new behavior to be introduced without repeatedly
changing its existing, stable implementation.

This does not mean existing code can never be changed. It means a design
should support likely extensions without unnecessary modifications to
stable code.

## 3.2 Why is OCP needed?

Imagine a payment system that supports UPI and card payments. Later,
cash and wallet payments are added.

If every new payment type requires editing a large conditional
statement, the existing code becomes increasingly difficult to maintain.

## 3.3 Problematic implementation

``` python
class PaymentProcessor:
    def process(self, payment_type, amount):
        if payment_type == "UPI":
            print(f"Processing UPI payment: {amount}")

        elif payment_type == "CARD":
            print(f"Processing card payment: {amount}")

        elif payment_type == "CASH":
            print(f"Processing cash payment: {amount}")
```

Every new payment type requires modifying `PaymentProcessor`.

## 3.4 Improved implementation

Use an abstraction and let each payment type implement its own behavior.

``` python
from abc import ABC, abstractmethod


class Payment(ABC):
    @abstractmethod
    def pay(self, amount):
        pass


class UpiPayment(Payment):
    def pay(self, amount):
        print(f"Processing UPI payment: {amount}")


class CardPayment(Payment):
    def pay(self, amount):
        print(f"Processing card payment: {amount}")


class CashPayment(Payment):
    def pay(self, amount):
        print(f"Processing cash payment: {amount}")


class PaymentProcessor:
    def process(self, payment, amount):
        payment.pay(amount)


processor = PaymentProcessor()

processor.process(UpiPayment(), 500)
processor.process(CardPayment(), 1000)
processor.process(CashPayment(), 200)
```

## 3.5 How does this follow OCP?

-   `PaymentProcessor` works with the `Payment` abstraction.
-   Each payment type provides its own implementation.
-   A new payment type can be added by creating another subclass.
-   The processor does not need a new conditional branch for every
    payment type.

For example:

``` python
class WalletPayment(Payment):
    def pay(self, amount):
        print(f"Processing wallet payment: {amount}")
```

The existing `PaymentProcessor` can process this new implementation
without modification.

## 3.6 Real-world applications

-   Adding new payment providers.
-   Supporting new notification channels.
-   Adding new report formats.
-   Supporting new shipping methods.
-   Adding new discount strategies.

## 3.7 Benefits

-   Easier feature extension.
-   Less modification of stable code.
-   Better use of polymorphism.
-   Reduced risk of breaking existing behavior.

**Exam keyword:** Extend without repeatedly modifying existing code.

------------------------------------------------------------------------

# 4. Liskov Substitution Principle (LSP)

## 4.1 Definition

**Objects of a subtype should be usable wherever objects of the base
type are expected without breaking the correctness of the program.**

A child class must honor the behavioral expectations established by its
parent class.

In simple terms, inheritance should represent a valid behavioral
relationship, not just a shared name or property.

## 4.2 Why is LSP needed?

A child class may inherit methods that it cannot correctly support. If
code expects the parent class's behavior, substituting such a child
object can cause errors or unexpected results.

## 4.3 Problematic implementation

``` python
class Bird:
    def fly(self):
        print("Bird is flying")


class Penguin(Bird):
    def fly(self):
        raise NotImplementedError("Penguins cannot fly")


def make_bird_fly(bird):
    bird.fly()


penguin = Penguin()
make_bird_fly(penguin)
```

### What is wrong?

-   `Bird` promises that its objects can fly.
-   `Penguin` inherits from `Bird`.
-   A penguin cannot satisfy the expected flying behavior.
-   Replacing a `Bird` object with a `Penguin` object breaks the
    function's expectation.

This violates LSP.

## 4.4 Improved implementation

Separate the common bird behavior from the flying behavior.

``` python
class Bird:
    def eat(self):
        print("Bird is eating")


class FlyingBird(Bird):
    def fly(self):
        print("Bird is flying")


class Sparrow(FlyingBird):
    def fly(self):
        print("Sparrow is flying")


class Penguin(Bird):
    def swim(self):
        print("Penguin is swimming")


def feed_bird(bird):
    bird.eat()


sparrow = Sparrow()
penguin = Penguin()

feed_bird(sparrow)
feed_bird(penguin)
```

Now: - Both `Sparrow` and `Penguin` support the behavior promised by
`Bird`. - Only flying birds inherit from `FlyingBird`. - The design does
not promise that every bird can fly.

## 4.5 Important behavioral rules

A well-designed subtype should: - Preserve the expectations of its base
type. - Not strengthen preconditions in a way that rejects valid
parent-class inputs. - Not weaken postconditions in a way that breaks
expected results. - Preserve important invariants of the parent type. -
Avoid unexpected exceptions for operations the parent contract promises
to support.

## 4.6 Real-world example

Suppose a system has a `ReadOnlyFile` type. A subclass should not
unexpectedly reject reading operations that the base type promises.
Similarly, a payment method should honor the behavior expected by the
payment abstraction.

## 4.7 Benefits

-   Reliable inheritance.
-   Predictable behavior.
-   Fewer runtime surprises.
-   Better class hierarchies.
-   Safer polymorphism.

**Exam keyword:** Child objects must safely replace parent objects.

------------------------------------------------------------------------

# 5. Interface Segregation Principle (ISP)

## 5.1 Definition

**Clients should not be forced to depend on methods they do not use.**

Instead of one large interface containing many unrelated operations,
create smaller and more focused interfaces.

Python does not have a dedicated `interface` keyword. Abstract base
classes or structural interfaces such as `typing.Protocol` can be used
to describe contracts.

## 5.2 Why is ISP needed?

A large interface can force classes to implement operations they do not
support. This leads to: - Empty method implementations. - Unnecessary
exceptions. - Unrelated dependencies. - Difficult maintenance.

## 5.3 Problematic implementation

``` python
from abc import ABC, abstractmethod


class Machine(ABC):
    @abstractmethod
    def print_document(self):
        pass

    @abstractmethod
    def scan_document(self):
        pass

    @abstractmethod
    def fax_document(self):
        pass


class BasicPrinter(Machine):
    def print_document(self):
        print("Printing document")

    def scan_document(self):
        raise NotImplementedError

    def fax_document(self):
        raise NotImplementedError
```

### What is wrong?

A basic printer can print but cannot scan or fax. However, the large
`Machine` abstraction forces it to provide all three methods.

## 5.4 Improved implementation

Create smaller interfaces based on capabilities.

``` python
from abc import ABC, abstractmethod


class Printer(ABC):
    @abstractmethod
    def print_document(self):
        pass


class Scanner(ABC):
    @abstractmethod
    def scan_document(self):
        pass


class Fax(ABC):
    @abstractmethod
    def fax_document(self):
        pass


class BasicPrinter(Printer):
    def print_document(self):
        print("Printing document")


class MultiFunctionPrinter(Printer, Scanner, Fax):
    def print_document(self):
        print("Printing document")

    def scan_document(self):
        print("Scanning document")

    def fax_document(self):
        print("Sending fax")
```

Now: - `BasicPrinter` implements only printing. - `MultiFunctionPrinter`
implements printing, scanning, and faxing. - Neither class is forced to
implement irrelevant operations.

## 5.5 Real-world applications

-   Printers with different capabilities.
-   Payment services that support different operations.
-   User roles with different permissions.
-   Storage providers with different capabilities.
-   Notification services with different delivery methods.

## 5.6 Benefits

-   Small and focused interfaces.
-   Fewer unnecessary methods.
-   Reduced coupling.
-   Easier implementation and testing.
-   Better flexibility.

**Exam keyword:** Do not force clients to depend on methods they do not
use.

------------------------------------------------------------------------

# 6. Dependency Inversion Principle (DIP)

## 6.1 Definition

The Dependency Inversion Principle has two main statements:

1.  High-level modules should not depend on low-level modules. Both
    should depend on abstractions.
2.  Abstractions should not depend on details. Details should depend on
    abstractions.

A high-level module contains important business logic. A low-level
module provides implementation details such as database access, email
delivery, or external services.

## 6.2 Why is DIP needed?

If high-level business logic directly creates and depends on a concrete
implementation: - Replacing that implementation becomes difficult. -
Unit testing becomes harder. - Changes in low-level details can affect
high-level logic. - Components become tightly coupled.

## 6.3 Problematic implementation

``` python
class EmailService:
    def send_email(self, message):
        print("Sending email:", message)


class Notification:
    def __init__(self):
        self.email_service = EmailService()

    def send(self, message):
        self.email_service.send_email(message)


notification = Notification()
notification.send("Hello")
```

### What is wrong?

`Notification` directly creates `EmailService`. It is tightly coupled to
email delivery and cannot easily switch to SMS or another service.

## 6.4 Improved implementation

Depend on an abstraction and provide the concrete service from outside.

``` python
from abc import ABC, abstractmethod


class MessageService(ABC):
    @abstractmethod
    def send(self, message):
        pass


class EmailService(MessageService):
    def send(self, message):
        print("Sending email:", message)


class SMSService(MessageService):
    def send(self, message):
        print("Sending SMS:", message)


class Notification:
    def __init__(self, service):
        self.service = service

    def send(self, message):
        self.service.send(message)


email_notification = Notification(EmailService())
sms_notification = Notification(SMSService())

email_notification.send("Hello through email")
sms_notification.send("Hello through SMS")
```

## 6.5 How does this follow DIP?

-   `Notification` is the high-level module.
-   `EmailService` and `SMSService` are low-level implementations.
-   `MessageService` is the abstraction.
-   Both concrete services implement the abstraction.
-   `Notification` depends on the abstraction rather than directly
    creating a specific service.

## 6.6 Real-world applications

-   Switching between database providers.
-   Switching between email and SMS services.
-   Replacing payment gateways.
-   Using mock services during unit testing.
-   Integrating external APIs.

## 6.7 Benefits

-   Loose coupling.
-   Easier testing.
-   Replaceable implementations.
-   Better flexibility.
-   Separation of business logic from infrastructure details.

**Exam keyword:** Depend on abstractions, not concrete implementations.

------------------------------------------------------------------------

# 7. Dependency Injection

Dependency Injection (DI) is a technique in which an object receives the
dependencies it needs from outside instead of creating them internally.

DI is commonly used to implement DIP, but the two terms are not
identical: - **DIP** is a design principle. - **DI** is a technique for
supplying dependencies.

## 7.1 Constructor injection

``` python
class Notification:
    def __init__(self, service):
        self.service = service
```

The dependency is supplied when the object is created:

``` python
notification = Notification(EmailService())
```

## 7.2 Benefits of dependency injection

-   Makes dependencies visible.
-   Allows implementations to be replaced.
-   Simplifies unit testing.
-   Reduces direct object construction inside business logic.

------------------------------------------------------------------------

# 8. How the SOLID Principles Work Together

The five principles address different but related design problems.

Consider a banking application:

  -----------------------------------------------------------------------
  Principle                           Possible application
  ----------------------------------- -----------------------------------
  SRP                                 Separate account operations,
                                      transaction storage, and statement
                                      generation.

  OCP                                 Add new payment methods without
                                      repeatedly changing the payment
                                      processor.

  LSP                                 Ensure every account subtype
                                      behaves according to the account
                                      contract.

  ISP                                 Separate deposit, withdrawal, and
                                      reporting capabilities where
                                      clients need different operations.

  DIP                                 Make banking services depend on
                                      repository or payment abstractions
                                      rather than specific database or
                                      gateway classes.
  -----------------------------------------------------------------------

### Combined design idea

``` text
                 High-level banking service
                           |
                           v
                    Abstraction/API
                     /          \
                    v            v
             Bank repository   Payment service
                    |            |
                    v            v
               MySQL store    UPI / Card
```

The diagram is conceptual: the key idea is that high-level business
logic should communicate through stable abstractions rather than being
tied to particular infrastructure implementations.

------------------------------------------------------------------------

# 9. SOLID in Real-World Applications

## 9.1 Banking systems

-   SRP: separate account management, transaction history, and
    notifications.
-   OCP: add new transaction or payment types.
-   LSP: account subtypes preserve the behavior promised by their base
    contract.
-   ISP: separate capabilities for account operations and reporting.
-   DIP: depend on repository abstractions rather than a specific
    database.

## 9.2 E-commerce applications

-   SRP: separate cart, pricing, order, and shipping responsibilities.
-   OCP: add new discount strategies.
-   LSP: shipping implementations follow the expected shipping contract.
-   ISP: separate payment, tracking, and cancellation capabilities.
-   DIP: order processing depends on payment and shipping abstractions.

## 9.3 Notification systems

-   SRP: separate message formatting and message delivery.
-   OCP: add new channels such as SMS or push notifications.
-   LSP: each channel honors the common delivery contract.
-   ISP: define only the delivery capabilities needed by a client.
-   DIP: notification logic depends on a message-delivery abstraction.

------------------------------------------------------------------------

# 10. Advantages and Limitations of SOLID

## Advantages

1.  **Maintainability:** Focused components are easier to understand and
    modify.
2.  **Extensibility:** New behavior can often be added with fewer
    changes to stable code.
3.  **Testability:** Dependencies can be replaced with test doubles or
    mocks.
4.  **Loose coupling:** Components depend less on implementation
    details.
5.  **Reusability:** Well-designed components can be used in different
    parts of an application.
6.  **Reliability:** Behavioral contracts make inheritance and
    polymorphism more predictable.

## Limitations and cautions

1.  Applying every principle to every small program can create
    unnecessary classes and abstractions.
2.  Too many interfaces can make a simple design harder to understand.
3.  OCP requires developers to anticipate likely extension points; not
    every possible future change can be predicted.
4.  SOLID does not replace testing, clear requirements, or good domain
    modeling.
5.  A design should be improved when a real maintenance or extension
    problem appears, not merely to increase the number of classes.

**Important:** SOLID principles are design guidelines, not rigid rules
that must be applied mechanically.

------------------------------------------------------------------------

# 11. Common Mistakes in SOLID Questions

  -----------------------------------------------------------------------
  Common mistake                      Correct understanding
  ----------------------------------- -----------------------------------
  SRP means one method per class.     SRP means one focused
                                      responsibility or reason to change.

  OCP means never modify existing     OCP encourages extension without
  code.                               unnecessary changes to stable code.

  LSP means every child must have     A child must preserve the
  exactly the same methods.           behavioral contract expected from
                                      its base type.

  ISP means every class needs an      ISP means clients should not depend
  interface.                          on irrelevant methods.

  DIP and DI are the same.            DIP is a principle; DI is one
                                      technique that can help implement
                                      it.

  An abstract class and an interface  They are related concepts, but
  are always identical.               Python's abstract base classes can
                                      also contain state and concrete
                                      behavior.

  SOLID always requires more classes. Use abstractions when they solve a
                                      real design problem.
  -----------------------------------------------------------------------

------------------------------------------------------------------------

# 12. Common Exam Questions and Answers

## Short-answer questions

### 1. What is SOLID?

SOLID is a set of five object-oriented design principles that improve
maintainability, extensibility, testability, and flexibility.

### 2. Who popularized SOLID?

Robert C. Martin, also known as Uncle Bob, popularized the SOLID
acronym.

### 3. What is SRP?

A class should have one reason to change, meaning it should have one
focused responsibility.

### 4. What is OCP?

Software entities should be open for extension but closed for
modification.

### 5. What is LSP?

A subtype should be usable wherever its base type is expected without
breaking program correctness.

### 6. What is ISP?

Clients should not be forced to depend on methods they do not use.

### 7. What is DIP?

High-level and low-level modules should depend on abstractions rather
than directly depending on one another.

### 8. What is dependency injection?

A technique in which an object receives its dependencies from outside
instead of constructing them internally.

### 9. Which SOLID principle helps add new payment methods without changing the payment processor?

Open/Closed Principle.

### 10. Which principle prevents a class from having too many unrelated responsibilities?

Single Responsibility Principle.

### 11. Which principle is concerned with correct inheritance behavior?

Liskov Substitution Principle.

### 12. Which principle encourages smaller interfaces?

Interface Segregation Principle.

### 13. Which principle reduces dependency on concrete classes?

Dependency Inversion Principle.

## Comparison table for quick revision

  -----------------------------------------------------------------------
  Principle                           Main question to ask
  ----------------------------------- -----------------------------------
  SRP                                 Does this class have more than one
                                      reason to change?

  OCP                                 Can I add a new behavior without
                                      repeatedly modifying stable code?

  LSP                                 Can this subtype safely replace its
                                      base type?

  ISP                                 Is this client forced to depend on
                                      operations it does not use?

  DIP                                 Does high-level logic depend
                                      directly on concrete implementation
                                      details?
  -----------------------------------------------------------------------

## Suggested structure for a 5- or 10-mark exam answer

For each SOLID principle, write:

1.  **Full form:** Write the principle's complete name.
2.  **Definition:** State the formal definition.
3.  **Purpose:** Explain why the principle is needed.
4.  **Violation:** Show a short example of poor design.
5.  **Solution:** Show the improved design.
6.  **Benefits:** Mention two or three advantages.
7.  **Application:** Give one real-world example.

This structure makes an answer complete and easy to evaluate.

------------------------------------------------------------------------

# 13. Final Quick Revision Sheet

  Letter   Full form                         Remember
  -------- --------------------------------- -----------------------------------------
  S        Single Responsibility Principle   One reason to change
  O        Open/Closed Principle             Extend without unnecessary modification
  L        Liskov Substitution Principle     Child can safely replace parent
  I        Interface Segregation Principle   Small, focused interfaces
  D        Dependency Inversion Principle    Depend on abstractions

### One-line memory aid

-   **S:** One class, one focused responsibility.
-   **O:** Add new behavior without repeatedly editing stable code.
-   **L:** Subtypes must honor parent behavior.
-   **I:** Do not force unused methods on clients.
-   **D:** High-level logic should not depend on concrete details.

## Conclusion

SOLID principles provide a practical foundation for designing
object-oriented software. They help developers manage responsibilities,
extend functionality, maintain correct inheritance behavior, create
focused interfaces, and reduce tight coupling.

When applied thoughtfully, these principles improve the long-term
quality of software systems. They are particularly useful in large
applications where requirements evolve and different components must be
developed, tested, and maintained independently.
