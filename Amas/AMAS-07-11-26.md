# Python, Git, Linux CLI, and OOP    -   AMAS

Short, clear answers with practical examples.

## 1. What is the difference between a list and a set in Python?

-   **List:** Ordered, allows duplicate values, and supports indexing.
-   **Set:** Unordered, stores only unique values, and does not support
    indexing.

``` python
numbers = [1, 2, 2, 3]
unique_numbers = {1, 2, 2, 3}

print(numbers)         # [1, 2, 2, 3]
print(unique_numbers)  # {1, 2, 3}
```

Use a list when order and duplicates matter. Use a set when you need
unique values or fast membership checks.

## 2. What is the difference between `==` and `is` in Python?

-   `==` checks whether two objects have equal values.
-   `is` checks whether two references point to the exact same object.

``` python
a = [1, 2]
b = [1, 2]

print(a == b)  # True
print(a is b)  # False
```

Use `is` commonly for identity checks such as `value is None`.

## 3. What command lists remote Git branches?

``` bash
git branch -r
```

To list both local and remote branches:

``` bash
git branch -a
```

## 4. What is the difference between `kill` and `kill -9` in Linux?

-   `kill PID` sends **SIGTERM (15)** by default. It asks the process to
    stop and allows it to clean up.
-   `kill -9 PID` sends **SIGKILL (9)**. It forces the process to stop
    immediately and cannot be caught or ignored.

Try `kill PID` first. Use `kill -9 PID` only when the process does not
respond.

## 5. How can we check hidden-file permissions in Linux?

Use `ls -la` to display hidden files (names beginning with `.`) along
with their permissions.

``` bash
ls -la
```

To check one hidden file:

``` bash
ls -l .filename
```

## 6. Why do we use the `self` keyword in Python OOP?

`self` refers to the current object (instance) of a class. It lets an
instance method access or modify that object's attributes and call its
methods.

``` python
class Student:
    def __init__(self, name):
        self.name = name

    def introduce(self):
        print(self.name)

student = Student("Ravi")
student.introduce()
```

`self` is a convention, not a reserved keyword, but it should be used
for the first parameter of instance methods.

## 7. How can we iterate through a dictionary using both keys and values?

Use the dictionary's `.items()` method.

``` python
student = {"name": "Ravi", "age": 21}

for key, value in student.items():
    print(key, value)
```

## 8. What is the difference between `chmod` and `chown` in Linux?

-   `chmod` changes a file's **permissions** (read, write, execute).
-   `chown` changes a file's **owner** or group.

Examples:

``` bash
chmod 755 script.sh
sudo chown user:group file.txt
```

## 9. What is an object in OOP?

An **object** is an instance of a class. It has data (attributes) and
behavior (methods).

``` python
class Car:
    def start(self):
        print("Car started")

my_car = Car()  # my_car is an object
my_car.start()
```

Here, `Car` is the class and `my_car` is its object.

## 10. What command sorts files by modification time?

``` bash
ls -lt
```

This lists the most recently modified files first. To show the oldest
first:

``` bash
ls -ltr
```

## 11. How can we check the permissions of a specific file?

Use `ls -l` followed by the file name:

``` bash
ls -l file.txt
```

For detailed file information, including numeric permissions:

``` bash
stat file.txt
```

## 12. What is the use of the `top` command in Linux?

`top` displays live system and process information, including:

-   CPU and memory usage.
-   Running processes.
-   Process IDs (PIDs).
-   System load.

``` bash
top
```

Press `q` to exit.

## 13. Does Python support compilation and polymorphism?

**Compilation:** Yes. Python typically compiles source code into
bytecode, which the Python virtual machine executes. It is not usually
compiled to a standalone machine-code executable in the same way as C or
C++.

**Polymorphism:** Yes. Different objects can respond to the same method
or operation in their own way.

``` python
class Dog:
    def speak(self):
        print("Bark")

class Cat:
    def speak(self):
        print("Meow")

for animal in [Dog(), Cat()]:
    animal.speak()
```

Python also supports polymorphism through inheritance, method
overriding, and duck typing.

## 14. How can we check the remote origin in Git?

Use:

``` bash
git remote -v
```

It displays the configured remote names and their fetch/push URLs. To
see details about `origin`:

``` bash
git remote show origin
```

## 15. What is the difference between the working directory and the staging area in Git?

-   **Working directory:** The files where you make changes.
-   **Staging area:** The area where you select changes that will go
    into the next commit.

Typical workflow:

``` bash
# Make changes to files
git add file.py       # Move selected changes to staging
git commit -m "Add feature"  # Save staged changes in a commit
```

In short: **Working directory → Staging area → Repository (commit).**

## 16. What is PEP 8 in Python?

PEP 8 is Python's official style guide. It provides conventions for
writing readable and consistent Python code.

Examples include:

-   Use 4 spaces for indentation.
-   Use `snake_case` for function and variable names.
-   Use `PascalCase` for class names.
-   Keep lines reasonably short (the guide recommends a maximum of 79
    characters for code).

Following PEP 8 makes code easier to read and maintain.

## 17. How can we check whether the internet is working using the CLI?

Try these commands:

**1. Test connectivity to an IP address:**

``` bash
ping -c 4 8.8.8.8
```

**2. Test DNS and website connectivity:**

``` bash
ping -c 4 google.com
```

**3. Check an HTTPS connection:**

``` bash
curl -I https://www.google.com
```

If the IP test works but the domain test fails, there may be a DNS
issue. If both fail, your device may not have internet connectivity or
the network may be blocking the tests.
