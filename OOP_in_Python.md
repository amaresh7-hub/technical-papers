# Object-Oriented Programming (OOP) in Python

## Detailed Technical Paper and Exam Reference

> **Purpose:** This paper is designed as an open-book examination
> reference. It includes definitions, explanations, syntax, diagrams,
> Python examples, comparisons, common mistakes, and quick revision
> questions. Code examples use Python 3.

------------------------------------------------------------------------

## Table of Contents

1.  Introduction to OOP
2.  Classes and Objects
3.  Attributes and `self`
4.  Constructors and Object Initialization
5.  Instance, Class, and Static Methods
6.  Instance Variables and Class Variables
7.  The Four Pillars of OOP
    -   Encapsulation
    -   Inheritance
    -   Polymorphism
    -   Abstraction
8.  Types of Inheritance
9.  Method Overriding, Overloading, and `super()`
10. Composition and Aggregation
11. Special (Dunder) Methods
12. Properties and Controlled Attribute Access
13. OOP Relationships and Design Choices
14. Advantages and Limitations of OOP
15. Common Exam Questions and Quick Revision

------------------------------------------------------------------------

# 1. Introduction to OOP

### 1.1 What is Object-Oriented Programming?

**Object-Oriented Programming (OOP)** is a programming paradigm in which
a program is designed around *objects*. An object combines data
(attributes) and the operations that work on that data (methods).

For example, a bank account has data such as account holder name and
balance. It also has operations such as deposit, withdraw, and check
balance. These can be represented together in a `BankAccount` class.

### 1.2 Why was OOP introduced?

In a procedural approach, data and functions may be maintained
separately. As a program grows, this can make it harder to organize
related data, reuse code, and control changes.

OOP helps by: - Grouping related data and behavior. - Modeling entities
and relationships. - Reusing and extending existing code. - Separating
responsibilities into classes. - Controlling access to an object's
internal state.

### 1.3 Procedural Programming vs OOP

  -----------------------------------------------------------------------
  Procedural programming              Object-Oriented Programming
  ----------------------------------- -----------------------------------
  Organizes a program mainly around   Organizes a program around classes
  functions/procedures.               and objects.

  Data and functions may be separate. Data and related methods are
                                      grouped in objects.

  Reuse is commonly achieved through  Reuse can use functions,
  functions.                          composition, and inheritance.

  Suitable for small, straightforward Useful for systems with related
  tasks.                              entities and behavior.

  Data protection depends on program  Encapsulation provides controlled
  structure and conventions.          access to object state.
  -----------------------------------------------------------------------

Neither approach is automatically better for every problem. The design
should fit the problem.

### 1.4 Core OOP terminology

  -----------------------------------------------------------------------
  Term                                Meaning
  ----------------------------------- -----------------------------------
  Class                               A definition or blueprint
                                      describing attributes and behavior.

  Object                              A particular instance created from
                                      a class.

  Attribute                           Data associated with an object or
                                      class.

  Method                              A function defined inside a class.

  State                               The current values of an object's
                                      attributes.

  Behavior                            The operations an object can
                                      perform.

  Identity                            What makes an object distinct from
                                      other objects.

  Constructor / initializer           Commonly used term for the
                                      initialization process; in Python,
                                      `__init__()` initializes an
                                      already-created object.
  -----------------------------------------------------------------------

------------------------------------------------------------------------

# 2. Classes and Objects

### 2.1 Class

A **class** is a user-defined type that describes the data and behavior
its instances can have.

### 2.2 Object

An **object** is an instance of a class. Multiple objects can be created
from the same class, and each object can maintain its own instance data.

### 2.3 Example

``` python
class Student:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def display(self):
        print("Name:", self.name)
        print("Age:", self.age)


student1 = Student("Amaresh", 22)
student2 = Student("Rahul", 21)

student1.display()
student2.display()
```

**Output:**

``` text
Name: Amaresh
Age: 22
Name: Rahul
Age: 21
```

In this example: - `Student` is the class. - `student1` and `student2`
refer to two different objects. - `name` and `age` are instance
attributes. - `display()` is an instance method.

### 2.4 Creating objects

General syntax:

``` python
object_name = ClassName(arguments)
```

Example:

``` python
student = Student("Amaresh", 22)
```

### 2.5 Checking object type and identity

``` python
print(type(student))
print(isinstance(student, Student))
```

-   `type(obj)` returns the object's type.
-   `isinstance(obj, ClassName)` checks whether the object is an
    instance of the class or one of its subclasses.

------------------------------------------------------------------------

# 3. Attributes and `self`

### 3.1 What is `self`?

`self` is the conventional name for the first parameter of an instance
method. It refers to the object on which the method is called.

Python passes the instance automatically when an instance method is
called through an object.

``` python
class Employee:
    def __init__(self, name):
        self.name = name

    def display(self):
        print(self.name)


employee = Employee("Amaresh")
employee.display()
```

The call:

``` python
employee.display()
```

is conceptually equivalent to:

``` python
Employee.display(employee)
```

`self` is a convention, not a reserved keyword. However, using the name
`self` is strongly recommended.

### 3.2 Instance attributes

Instance attributes belong to a particular object. Different objects can
have different values.

``` python
class Employee:
    def __init__(self, name, salary):
        self.name = name
        self.salary = salary


e1 = Employee("A", 30000)
e2 = Employee("B", 40000)

print(e1.salary)  # 30000
print(e2.salary)  # 40000
```

------------------------------------------------------------------------

# 4. Constructors and Object Initialization

### 4.1 `__init__()` method

In Python, `__init__()` is an initializer. It runs automatically after a
new instance has been created and is used to initialize its attributes.

``` python
class BankAccount:
    def __init__(self, name, balance):
        self.name = name
        self.balance = balance


account = BankAccount("Amaresh", 1000)
```

### 4.2 Is `__init__()` the actual constructor?

Strictly speaking: - `__new__()` creates and returns a new instance. -
`__init__()` initializes that instance.

For most everyday Python classes, developers mainly work with
`__init__()`.

``` python
class Example:
    def __new__(cls):
        print("Creating object")
        return super().__new__(cls)

    def __init__(self):
        print("Initializing object")


obj = Example()
```

**Output:**

``` text
Creating object
Initializing object
```

### 4.3 Default values in `__init__()`

``` python
class Employee:
    def __init__(self, name, department="IT"):
        self.name = name
        self.department = department


e1 = Employee("Amaresh")
e2 = Employee("Rahul", "HR")
```

------------------------------------------------------------------------

# 5. Types of Methods in Python

Python classes commonly use three method types.

## 5.1 Instance method

-   Receives the instance as the first argument, conventionally `self`.
-   Can read and modify instance attributes.
-   Can also access class attributes through the instance or class.

``` python
class Employee:
    def __init__(self, name):
        self.name = name

    def display_name(self):
        print(self.name)


employee = Employee("Amaresh")
employee.display_name()
```

## 5.2 Class method

-   Declared using `@classmethod`.
-   Receives the class as the first argument, conventionally `cls`.
-   Commonly reads or modifies class-level state.
-   Can be useful for alternative constructors.

``` python
class Employee:
    company = "ABC"

    def __init__(self, name):
        self.name = name

    @classmethod
    def change_company(cls, company_name):
        cls.company = company_name


Employee.change_company("XYZ")
print(Employee.company)
```

### Alternative constructor example

``` python
class Employee:
    def __init__(self, name, salary):
        self.name = name
        self.salary = salary

    @classmethod
    def from_string(cls, text):
        name, salary = text.split(",")
        return cls(name, int(salary))


employee = Employee.from_string("Amaresh,30000")
print(employee.name, employee.salary)
```

## 5.3 Static method

-   Declared using `@staticmethod`.
-   Does not receive `self` or `cls` automatically.
-   Used for a utility operation logically related to the class.

``` python
class Calculator:
    @staticmethod
    def add(a, b):
        return a + b


print(Calculator.add(10, 20))
```

## 5.4 Comparison

  -----------------------------------------------------------------------
  Feature           Instance method   Class method      Static method
  ----------------- ----------------- ----------------- -----------------
  Decorator         None              `@classmethod`    `@staticmethod`

  First automatic   `self`            `cls`             None
  argument                                              

  Main focus        Object/instance   Class             Utility operation

  Can access        Yes               No, not without   No, not without
  instance data                       an instance       an instance
  directly?                                             

  Can access class  Yes               Yes               Only if
  data?                                                 explicitly
                                                        referenced
  -----------------------------------------------------------------------

**Exam point:** A class method is not the same as a static method. A
class method receives the class; a static method receives no automatic
first argument.

------------------------------------------------------------------------

# 6. Instance Variables and Class Variables

## 6.1 Instance variables

Instance variables are associated with individual objects.

## 6.2 Class variables

Class variables are defined in the class body and are shared through the
class unless shadowed by an instance attribute.

``` python
class Employee:
    company = "ABC Technologies"

    def __init__(self, name, salary):
        self.name = name
        self.salary = salary


e1 = Employee("John", 30000)
e2 = Employee("David", 40000)

print(e1.company)
print(e2.company)
```

### Modifying a class variable

``` python
Employee.company = "XYZ Technologies"
print(e1.company)
print(e2.company)
```

Both objects see the updated class attribute unless either has its own
instance attribute named `company`.

### Important mutable class-variable issue

Avoid using a mutable class variable when each object should have its
own list or dictionary.

**Incorrect for independent employee skills:**

``` python
class Employee:
    skills = []
```

All instances share the same list.

**Correct:**

``` python
class Employee:
    def __init__(self):
        self.skills = []
```

Now each object gets a separate list.

------------------------------------------------------------------------

# 7. The Four Pillars of OOP

The four commonly taught pillars are: 1. Encapsulation 2. Inheritance 3.
Polymorphism 4. Abstraction

## 7.1 Encapsulation

### Definition

**Encapsulation** is the bundling of data and the methods that operate
on that data within a class, while providing controlled ways to access
or modify the object's state.

### Why is encapsulation useful?

-   Prevents accidental modification of internal state.
-   Keeps validation close to the data it protects.
-   Provides a clear public interface.
-   Makes implementation changes easier without changing callers.

### Naming conventions in Python

  Form       Meaning
  ---------- ------------------------------------
  `name`     Public attribute
  `_name`    Non-public/protected by convention
  `__name`   Name-mangled attribute

Python does not enforce Java-style private access modifiers. A leading
double underscore triggers name mangling to reduce accidental access and
naming conflicts.

### Example: Bank account

``` python
class BankAccount:
    def __init__(self, name, balance):
        if balance < 100:
            raise ValueError("Minimum opening balance is 100")

        self.name = name
        self.__balance = balance

    def deposit(self, amount):
        if amount <= 0:
            raise ValueError("Amount must be positive")

        self.__balance += amount

    def withdraw(self, amount):
        if amount <= 0:
            raise ValueError("Amount must be positive")

        if self.__balance - amount < 100:
            raise ValueError("Minimum balance must be maintained")

        self.__balance -= amount

    def check_balance(self):
        return self.__balance


account = BankAccount("Amaresh", 1000)
account.deposit(500)
account.withdraw(200)
print(account.check_balance())  # 1300
```

### Name mangling

For an attribute named `__balance` inside `BankAccount`, Python
internally changes the attribute name to `_BankAccount__balance`.

``` python
print(account._BankAccount__balance)  # Technically accessible
```

This demonstrates that double underscore is not a security boundary.

### Encapsulation vs data hiding

-   Encapsulation is the broader design idea of bundling data and
    behavior and controlling access.
-   Data hiding is the practice of restricting or discouraging direct
    access to implementation details.

They are related, but they are not exactly interchangeable terms.

## 7.2 Inheritance

### Definition

**Inheritance** is an OOP mechanism through which a child class derives
attributes and methods from a parent class. The child can reuse, extend,
or override inherited behavior.

### Why use inheritance?

-   Reuse common behavior.
-   Represent an "is-a" relationship.
-   Create specialized classes from a general class.
-   Support method overriding and polymorphism.

### Basic example

``` python
class Animal:
    def eat(self):
        print("Animal is eating")


class Dog(Animal):
    def bark(self):
        print("Dog is barking")


dog = Dog()
dog.eat()
dog.bark()
```

### `super()` function

`super()` provides a way to call methods from the next class in the
method resolution order (MRO). It is commonly used to extend a parent's
initialization or behavior.

``` python
class Person:
    def __init__(self, name):
        self.name = name


class Student(Person):
    def __init__(self, name, roll_number):
        super().__init__(name)
        self.roll_number = roll_number


student = Student("Amaresh", 101)
print(student.name)
print(student.roll_number)
```

### Types of inheritance

#### A. Single inheritance

One child class inherits from one parent class.

``` python
class Animal:
    def eat(self):
        print("Eating")


class Dog(Animal):
    pass
```

Diagram:

``` text
Animal
  |
 Dog
```

#### B. Multiple inheritance

One child class inherits from more than one parent class.

``` python
class Camera:
    def take_photo(self):
        print("Taking photo")


class Phone:
    def make_call(self):
        print("Making call")


class Smartphone(Camera, Phone):
    pass


mobile = Smartphone()
mobile.take_photo()
mobile.make_call()
```

Diagram:

``` text
Camera     Phone
   \        /
   Smartphone
```

#### C. Multilevel inheritance

A class inherits from a child class, creating a chain.

``` python
class Animal:
    def eat(self):
        print("Eating")


class Mammal(Animal):
    def walk(self):
        print("Walking")


class Dog(Mammal):
    def bark(self):
        print("Barking")
```

Diagram:

``` text
Animal
  |
Mammal
  |
 Dog
```

#### D. Hierarchical inheritance

Multiple child classes inherit from the same parent class.

``` python
class Animal:
    def eat(self):
        print("Eating")


class Dog(Animal):
    def bark(self):
        print("Barking")


class Cat(Animal):
    def meow(self):
        print("Meowing")
```

Diagram:

``` text
       Animal
       /    \
     Dog    Cat
```

#### E. Hybrid inheritance

A combination of two or more inheritance patterns.

``` python
class Person:
    def identify(self):
        print("Person")


class Employee(Person):
    def work(self):
        print("Working")


class Student(Person):
    def study(self):
        print("Studying")


class Intern(Employee, Student):
    pass
```

### Method Resolution Order (MRO)

When multiple inheritance is used, Python needs a defined order for
looking up methods. This order is called the **Method Resolution Order
(MRO)**.

``` python
print(Intern.mro())
```

Python uses the C3 linearization algorithm to determine the MRO.
`super()` follows this order; it does not simply mean "call my direct
parent."

## 7.3 Polymorphism

### Definition

**Polymorphism** means "many forms." In OOP, it allows objects of
different classes to be used through a common operation while each
object provides its own behavior.

### A. Method overriding

A child class defines a method with the same name as a method in its
parent class.

``` python
class Character:
    def attack(self):
        print("Basic attack")


class Warrior(Character):
    def attack(self):
        print("Warrior attacks with a sword")


class Mage(Character):
    def attack(self):
        print("Mage attacks with magic")


characters = [Warrior(), Mage()]

for character in characters:
    character.attack()
```

### B. Duck typing

Python often focuses on whether an object supports the required behavior
rather than requiring it to inherit from a particular class.

``` python
class PDF:
    def print_file(self):
        print("Printing PDF")


class WordDocument:
    def print_file(self):
        print("Printing Word document")


def print_document(document):
    document.print_file()


print_document(PDF())
print_document(WordDocument())
```

Both objects work because both provide `print_file()`.

### C. Operator overloading

Operator overloading lets a class define how operators behave for its
objects by implementing special methods.

``` python
class Number:
    def __init__(self, value):
        self.value = value

    def __add__(self, other):
        return Number(self.value + other.value)


a = Number(10)
b = Number(20)
result = a + b

print(result.value)  # 30
```

### D. Method overloading in Python

Traditional compile-time method overloading by defining multiple methods
with the same name and different signatures is not supported in the same
way as languages such as Java. A later definition with the same name
replaces the earlier definition.

Similar behavior can be achieved with: - Default arguments. - `*args`
and `**kwargs`. - Type checks when appropriate. -
`functools.singledispatch` for function-based dispatch.

Example using default arguments:

``` python
class Calculator:
    def add(self, a, b, c=0):
        return a + b + c


calculator = Calculator()
print(calculator.add(10, 20))
print(calculator.add(10, 20, 30))
```

## 7.4 Abstraction

### Definition

**Abstraction** exposes essential operations while hiding unnecessary
implementation details.

A user of an object should know what operation is available without
needing to know every internal step used to perform it.

### Abstract class and abstract method

Python provides the `abc` module.

-   An abstract class is a class that defines an abstraction and may
    contain abstract and concrete methods.
-   An abstract method declares an operation that concrete subclasses
    must implement.
-   A class with unimplemented abstract methods cannot be instantiated.

``` python
from abc import ABC, abstractmethod


class Payment(ABC):
    @abstractmethod
    def pay(self, amount):
        pass


class UpiPayment(Payment):
    def pay(self, amount):
        print(f"Paid Rs. {amount} using UPI")


class CardPayment(Payment):
    def pay(self, amount):
        print(f"Paid Rs. {amount} using Card")


upi = UpiPayment()
upi.pay(500)
```

Trying to instantiate `Payment()` directly raises `TypeError` because
its abstract method has not been implemented.

### Abstraction vs encapsulation

  -----------------------------------------------------------------------
  Abstraction                         Encapsulation
  ----------------------------------- -----------------------------------
  Focuses on what an object exposes.  Focuses on bundling state and
                                      behavior and controlling access.

  Hides unnecessary implementation    Protects object invariants and
  complexity.                         internal representation.

  Commonly represented with abstract  Commonly represented with
  classes and interfaces.             private-by-convention attributes
                                      and public methods/properties.

  Example: a payment interface        Example: a bank account controls
  exposes `pay()`.                    changes to its balance.
  -----------------------------------------------------------------------

------------------------------------------------------------------------

# 8. Method Overriding, Overloading, and `super()`

  -----------------------------------------------------------------------
  Concept                 Meaning                 Python support
  ----------------------- ----------------------- -----------------------
  Overriding              A subclass replaces or  Directly supported.
                          specializes inherited   
                          behavior.               

  Overloading             Same operation name     Not traditional
                          with different          signature-based
                          parameter signatures.   overloading;
                                                  alternatives exist.

  `super()`               Delegates method lookup Directly supported.
                          to the next class in    
                          the MRO.                
  -----------------------------------------------------------------------

### Overriding example

``` python
class Animal:
    def sound(self):
        print("Some sound")


class Dog(Animal):
    def sound(self):
        print("Bark")


Dog().sound()
```

### `super()` example

``` python
class Parent:
    def show(self):
        print("Parent method")


class Child(Parent):
    def show(self):
        super().show()
        print("Child method")


Child().show()
```

------------------------------------------------------------------------

# 9. Composition and Aggregation

Inheritance is not the only way to reuse behavior. Objects can also
collaborate by containing references to other objects.

## 9.1 Composition

Composition is a strong whole-part relationship: an object owns or
manages another object as part of its structure.

``` python
class Engine:
    def start(self):
        print("Engine started")


class Car:
    def __init__(self):
        self.engine = Engine()

    def start_car(self):
        self.engine.start()
        print("Car started")


car = Car()
car.start_car()
```

## 9.2 Aggregation

Aggregation is a weaker whole-part relationship: one object refers to
another object that can exist independently.

``` python
class Teacher:
    def __init__(self, name):
        self.name = name


class Department:
    def __init__(self, teacher):
        self.teacher = teacher


teacher = Teacher("Anita")
department = Department(teacher)
```

The `Teacher` object is created independently and then passed to
`Department`.

## 9.3 Inheritance vs composition

  -----------------------------------------------------------------------
  Inheritance                         Composition
  ----------------------------------- -----------------------------------
  Represents an "is-a" relationship.  Represents a "has-a" or uses-a
                                      relationship.

  Child inherits parent behavior.     One object delegates work to
                                      another object.

  Can create tight coupling to a base Often allows components to be
  class.                              replaced independently.

  Useful for genuine subtypes.        Useful for assembling behavior from
                                      components.
  -----------------------------------------------------------------------

**Design guideline:** Prefer composition when the relationship is
"has-a." Use inheritance when the child truly satisfies the behavioral
contract of the parent.

------------------------------------------------------------------------

# 10. Special (Dunder) Methods

Special methods are methods with double underscores at the beginning and
end of their names. Python calls them for built-in operations.

  Method         Purpose
  -------------- -------------------------------------------
  `__init__()`   Initialize an instance.
  `__str__()`    Human-readable string representation.
  `__repr__()`   Developer-oriented representation.
  `__len__()`    Implements `len(obj)`.
  `__add__()`    Implements `obj1 + obj2`.
  `__eq__()`     Implements equality comparison with `==`.
  `__new__()`    Creates an instance.

Example:

``` python
class Book:
    def __init__(self, title, pages):
        self.title = title
        self.pages = pages

    def __str__(self):
        return f"{self.title} ({self.pages} pages)"

    def __len__(self):
        return self.pages


book = Book("Python Basics", 250)

print(book)
print(len(book))
```

------------------------------------------------------------------------

# 11. Properties and Controlled Attribute Access

Python's `property` mechanism allows attribute-style access while
running getter, setter, or deleter logic.

``` python
class Product:
    def __init__(self, price):
        self.price = price

    @property
    def price(self):
        return self.__price

    @price.setter
    def price(self, value):
        if value < 0:
            raise ValueError("Price cannot be negative")
        self.__price = value


product = Product(500)
print(product.price)

product.price = 700
print(product.price)
```

Properties are useful when validation or computed behavior is needed
without changing the public attribute-style interface.

------------------------------------------------------------------------

# 12. OOP Relationships and Design Choices

  -----------------------------------------------------------------------
  Relationship            Meaning                 Example
  ----------------------- ----------------------- -----------------------
  Inheritance             Is-a                    `Dog` is an `Animal`.

  Composition             Strong has-a / owns-a   `Car` contains an
                                                  `Engine`.

  Aggregation             Weak has-a / refers-to  `Department` refers to
                                                  independently created
                                                  `Teacher` objects.

  Association             Objects know about or   `Doctor` works with
                          collaborate with each   `Patient`.
                          other.                  
  -----------------------------------------------------------------------

These terms describe common modeling patterns. Exact lifecycle ownership
depends on the implementation and domain.

------------------------------------------------------------------------

# 13. Advantages and Limitations of OOP

## Advantages

1.  **Reusability:** Common behavior can be reused through composition
    and inheritance.
2.  **Modularity:** Related data and behavior can be grouped into
    classes.
3.  **Maintainability:** Focused classes can be changed independently.
4.  **Extensibility:** New classes can add specialized behavior.
5.  **Data control:** Encapsulation helps protect object invariants.
6.  **Testability:** Components can be tested in isolation when
    dependencies are designed well.
7.  **Real-world modeling:** Classes can represent domain entities and
    their relationships.

## Limitations

1.  OOP can introduce unnecessary complexity for very small programs.
2.  Poor inheritance hierarchies can create tight coupling.
3.  Excessive class creation can make code harder to follow.
4.  Object-oriented designs may use more memory than simpler
    data-oriented solutions in some situations.
5.  Developers can overuse design patterns when a simple function or
    data structure would be sufficient.

------------------------------------------------------------------------

# 14. Common Exam Questions and Quick Revision

## Short-answer questions

**1. What is OOP?**\
A programming paradigm that organizes software around objects containing
data and behavior.

**2. What is a class?**\
A definition or blueprint for creating objects.

**3. What is an object?**\
An instance of a class.

**4. What is `self`?**\
The conventional first parameter of an instance method, referring to the
current instance.

**5. What is `__init__()`?**\
An initializer automatically called after an instance is created.

**6. What is encapsulation?**\
Bundling data and related methods and controlling access to object
state.

**7. What is inheritance?**\
A mechanism through which a class derives and specializes behavior from
another class.

**8. What is polymorphism?**\
The ability to use a common operation with different object types that
provide different behavior.

**9. What is abstraction?**\
Exposing essential operations while hiding unnecessary implementation
details.

**10. What is method overriding?**\
Providing a subclass-specific implementation of an inherited method.

**11. What is MRO?**\
The method resolution order Python follows when looking up attributes
and methods.

**12. What is composition?**\
Building an object using other objects as components.

**13. What is a class method?**\
A method declared with `@classmethod` that receives the class as `cls`.

**14. What is a static method?**\
A method declared with `@staticmethod` that receives no automatic
instance or class argument.

**15. Does Python support traditional method overloading?**\
Not in the same way as Java; default arguments, `*args`, and other
dispatch techniques can provide similar flexibility.

## Quick comparison sheet

  Topic           Key point
  --------------- ------------------------------------------------
  Class           Blueprint/type definition
  Object          Instance of a class
  `self`          Current instance reference
  `cls`           Current class reference in a class method
  `__init__`      Initializes an instance
  Encapsulation   Bundle and control access to state
  Inheritance     Reuse/specialize behavior
  Polymorphism    Common operation, different behavior
  Abstraction     Expose essentials, hide implementation details
  Overriding      Subclass specializes inherited method
  Composition     Build behavior using collaborating objects
  MRO             Order used for method lookup

## Final exam tip

When an exam asks for an OOP concept, structure the answer as:

1.  Definition.
2.  Why the concept is needed.
3.  Syntax or mechanism.
4.  A short Python code example.
5.  Output or practical use.
6.  One advantage or limitation.

This structure makes answers clear and complete, especially for
questions carrying several marks.
