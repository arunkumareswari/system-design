# Day 1: OOP Fundamentals for System Design

Object-Oriented Programming (OOP) is one of the basic building blocks of software design.

When an application becomes large, it usually contains many real-world entities such as users, accounts, payments, orders, products, books, and employees.

OOP provides a structured way to represent these entities in code and define how they interact with each other.

For example, consider a **Library Management System**.

The system may contain:

```text
Library
Book
BookItem
Member
Librarian
Loan
```

Instead of putting all the logic into one large program, OOP helps organize the system into separate classes and objects with clear responsibilities.

---

## 1. Class

A **class is a blueprint or template used to create objects**.

For example:

```python
class Car:
    pass
```

The `Car` class describes what a Car object can contain or do, but it does not represent an actual car yet.

A class can define:

```text
Car
│
├── brand
├── model
├── color
└── drive()
```

Here:

- `brand`, `model`, and `color` represent data.
- `drive()` represents behavior.

The class defines the structure that its objects will follow.

---

## 2. Object

An **object is an instance of a class**.

Once a class has been defined, multiple objects can be created from it.

```python
class Car:
    pass

car1 = Car()
car2 = Car()
```

Here:

```text
Car   → Class
car1  → Object
car2  → Object
```

Both `car1` and `car2` are objects created from the same `Car` class.

The relationship can be understood as:

```text
Class
  ↓
Blueprint
  ↓
Objects
  ├── BMW
  ├── Audi
  └── Tesla
```

A class defines the structure, while each object represents an actual instance with its own state.

---

## 3. Attributes and Methods

Objects generally contain **data** and **behavior**.

### Attributes

Attributes represent the data or state of an object.

### Methods

Methods represent the behavior or actions that an object can perform.

Example:

```python
class Car:

    def __init__(self, brand, color):
        self.brand = brand
        self.color = color

    def drive(self):
        print("Car is driving")
```

Creating an object:

```python
car1 = Car("BMW", "Black")
```

The object now has:

```text
brand = BMW
color = Black
```

and it can perform:

```python
car1.drive()
```

So the basic relationship is:

```text
Class
   ↓
Attributes + Methods
   ↓
Object
```

In System Design, this becomes important when deciding what data belongs to an entity and what operations that entity should be responsible for.

---

# 4. Encapsulation

**Encapsulation means keeping data and the operations that work on that data together, while controlling how the data can be accessed or modified.**

Consider a bank account.

The account balance should not be changed randomly by different parts of the application.

Instead, the `BankAccount` class can control how the balance is updated.

```python
class BankAccount:

    def __init__(self, balance):
        self.__balance = balance

    def deposit(self, amount):
        if amount > 0:
            self.__balance += amount

    def get_balance(self):
        return self.__balance
```

The balance is kept inside the class and is modified through controlled methods.

For example:

```python
account.deposit(5000)

balance = account.get_balance()
```

This allows the class to apply rules such as:

```text
Deposit amount must be greater than 0
```

before changing the balance.

### Why Encapsulation is useful

Encapsulation provides:

- Controlled access to data
- Validation before modifying data
- Better maintainability
- Clear ownership of business rules

A simple way to remember it:

> **Encapsulation controls how data and behavior are accessed.**

---

# 5. Abstraction

**Abstraction means exposing only the necessary functionality while hiding the internal implementation details.**

Consider an ATM.

A user only needs to interact with:

```text
Insert Card
     ↓
Enter PIN
     ↓
Withdraw Money
```

The user does not need to know how the system internally performs:

```text
Authentication
     ↓
Bank Server Communication
     ↓
Account Validation
     ↓
Transaction Processing
     ↓
Database Update
```

All these implementation details can be hidden behind a simple operation such as:

```python
withdraw()
```

The user interacts with the interface without needing to understand the complete internal process.

### Encapsulation vs Abstraction

These concepts are related but solve different problems.

| Concept | Main Focus |
|---|---|
| Encapsulation | Controlling access to data and behavior |
| Abstraction | Hiding unnecessary implementation details |

A simple way to remember:

> **Encapsulation asks: "How do we control access?"**

> **Abstraction asks: "What details can we hide?"**

---

# 6. Inheritance

**Inheritance allows one class to reuse the properties and methods of another class.**

Example:

```python
class Animal:

    def eat(self):
        print("Eating")


class Dog(Animal):

    def bark(self):
        print("Barking")
```

Here, `Dog` inherits from `Animal`.

```text
Animal
   ↑
   |
  Dog
```

A `Dog` object can use both:

```python
dog.eat()
dog.bark()
```

The important idea behind inheritance is the **IS-A relationship**.

For example:

```text
Dog IS-A Animal
Cat IS-A Animal
```

Inheritance can be useful when the child class is genuinely a specialized version of the parent class.

---

# 7. Polymorphism

**Polymorphism allows different classes to provide different implementations of the same interface or method.**

Example:

```python
class Dog:

    def sound(self):
        print("Bark")


class Cat:

    def sound(self):
        print("Meow")
```

Both classes provide the same method:

```python
sound()
```

But each class behaves differently.

```text
Animal
  │
  ├── Dog → sound() → Bark
  │
  └── Cat → sound() → Meow
```

This becomes particularly useful when designing extensible systems.

Consider a payment system:

```text
PaymentMethod
      │
 ┌────┼──────┐
 ↓    ↓      ↓
Card  UPI   Wallet
```

The application can work with a common `PaymentMethod` interface, while `Card`, `UPI`, and `Wallet` can implement their own payment behavior.

This makes it easier to add another payment method later without changing the entire payment system.

### Simple definition

> **Polymorphism = Same interface, different behavior.**

---

# 8. Composition vs Inheritance

One of the important decisions in object-oriented design is choosing between **inheritance** and **composition**.

## Inheritance: IS-A

Inheritance represents an **IS-A** relationship.

```text
Dog IS-A Animal
```

Example:

```python
class Dog(Animal):
    pass
```

The `Dog` class is a specialized type of `Animal`.

---

## Composition: HAS-A

Composition represents a **HAS-A** relationship.

Consider a car:

```text
Car HAS-A Engine
```

A car is not an engine.

Instead, the car contains or uses an engine.

```python
class Car:

    def __init__(self):
        self.engine = Engine()
```

The relationship is:

```text
Inheritance
    ↓
   IS-A

Composition
    ↓
  HAS-A
```

### Why this matters in System Design

Inheritance creates a strong relationship between classes.

Composition allows a class to use other components without becoming tightly tied to their inheritance hierarchy.

A useful rule is:

> Use inheritance when there is a genuine **IS-A** relationship.

> Use composition when an object **HAS-A** or uses another object.

In many practical designs, composition is preferred when flexibility and loose coupling are more important than inheritance.

---

# 9. Association

**Association is a general relationship between two independent objects.**

For example:

```text
Teacher ───── Student
```

A teacher teaches a student.

However, the teacher and student can exist independently.

The relationship can be represented as:

```text
Teacher
   │
   │ teaches
   ↓
Student
```

Association does not necessarily imply ownership.

It simply means that two objects are related or interact with each other.

---

# 10. Aggregation

**Aggregation represents a HAS-A relationship where the contained objects can exist independently of the container.**

For example:

```text
Library
  │
  ├── Book
  ├── Book
  └── Book
```

A library contains books, but a book can exist independently of a particular library.

For example, if a library is closed, the books themselves do not necessarily cease to exist.

So:

```text
Library HAS Books
```

The key idea is:

> **Aggregation represents a weaker form of ownership.**

---

# 11. Composition

**Composition is a stronger form of ownership between objects.**

In composition, the contained object is closely associated with the lifecycle of the parent object.

For example:

```text
House
  │
  ├── Room
  ├── Room
  └── Room
```

A room is considered a component of a particular house.

The important distinction is:

```text
Aggregation
    ↓
Contained objects can exist independently

Composition
    ↓
Strong ownership and lifecycle relationship
```

In System Design, composition is useful when one component owns and manages another component.

---

# 12. Cohesion

**Cohesion describes how closely related the responsibilities of a class or module are.**

A class with high cohesion focuses on one closely related area of responsibility.

For example:

```python
class UserService:

    def create_user():
        pass

    def update_user():
        pass

    def delete_user():
        pass

    def get_user():
        pass
```

All these operations are related to users.

Therefore, the class has high cohesion.

Now consider:

```python
class UserService:

    def create_user():
        pass

    def send_email():
        pass

    def generate_invoice():
        pass

    def resize_image():
        pass

    def calculate_salary():
        pass
```

These responsibilities are unrelated.

This makes the class harder to understand and maintain.

Therefore, good design generally aims for:

> **High cohesion = closely related responsibilities stay together.**

---

# 13. Coupling

**Coupling describes how strongly one component depends on another component.**

Consider:

```text
Service A
   │
   ├────→ Service B
   ├────→ Service C
   └────→ Service D
```

If `Service A` is tightly dependent on all these services, changes in one service may require changes in `Service A`.

This creates high coupling.

A better design tries to reduce unnecessary dependencies between components.

### Why Low Coupling Matters

Low coupling makes a system:

- Easier to modify
- Easier to test
- Easier to maintain
- Easier to extend
- Less affected by changes in other components

A simple rule:

> **Low coupling means components can change with less impact on each other.**

---

# 14. High Cohesion + Low Coupling

Two important design goals are:

```text
High Cohesion
      +
Low Coupling
      ↓
Better Software Design
```

### High Cohesion

Each class or module should have closely related responsibilities.

### Low Coupling

Classes and modules should avoid unnecessary dependencies on each other.

These principles help create software that is easier to understand, test, maintain, and extend.

They will become especially important when designing larger systems with multiple services and components.

---

# Day 1 Mental Model

The concepts can be connected together like this:

```text
                         OOP
                          │
             ┌────────────┼────────────┐
             ↓            ↓            ↓
          Classes       Objects    Relationships
             │
      ┌──────┼──────────────────┐
      ↓      ↓        ↓         ↓
 Encapsulation  Abstraction  Inheritance  Polymorphism


Relationships
      │
      ├── Association
      ├── Aggregation
      └── Composition


Good Design
      │
      ├── High Cohesion
      └── Low Coupling
```

---

# Key Takeaways

The main OOP concepts required as a foundation for System Design are:

| Concept | Meaning |
|---|---|
| **Class** | Blueprint for creating objects |
| **Object** | Instance of a class |
| **Attribute** | Data or state of an object |
| **Method** | Behavior of an object |
| **Encapsulation** | Controls access to data and behavior |
| **Abstraction** | Hides unnecessary implementation details |
| **Inheritance** | Represents an IS-A relationship |
| **Polymorphism** | Same interface with different behavior |
| **Association** | General relationship between objects |
| **Aggregation** | HAS-A relationship with independent lifecycle |
| **Composition** | Strong ownership between objects |
| **Cohesion** | How closely related responsibilities are |
| **Coupling** | How strongly components depend on each other |

The goal of learning OOP for System Design is not simply to memorize definitions.

The important part is understanding how these concepts help answer practical design questions:

- What should be represented as a class?
- What responsibility should each class have?
- How should different objects interact?
- Should two classes use inheritance or composition?
- How can dependencies between components be reduced?
- How can a system be designed so that adding a new feature does not require rewriting existing code?

These questions become increasingly important as the system grows.

---