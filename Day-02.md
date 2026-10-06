# Day 2: SOLID Principles

SOLID principles are a set of five design principles that help us write code that is:

- Maintainable
- Flexible
- Extensible
- Easier to test
- Easier to modify

The five SOLID principles are:

```text
S → Single Responsibility Principle
O → Open/Closed Principle
L → Liskov Substitution Principle
I → Interface Segregation Principle
D → Dependency Inversion Principle
```

Instead of trying to memorize them, the easiest way to understand SOLID is:

```text
Bad Design
    ↓
What is the problem?
    ↓
SOLID Principle
    ↓
Better Design
```

---

## 1. Single Responsibility Principle (SRP)

### Definition
A class should have one responsibility and one primary reason to change.

This does not mean a class should contain only one method.  
It means the class should have one cohesive responsibility.

### ❌ Bad Design
Imagine a `User` class handling everything:

```python
class User:
    def create_user(self):
        pass

    def save_to_database(self):
        pass

    def send_email(self):
        pass

    def generate_report(self):
        pass
```

This class is responsible for:

```text
User
 ├── Create user
 ├── Database operations
 ├── Email
 └── Report generation
```

### What is the problem?
- If database logic changes, `User` changes.
- If the email provider changes, `User` changes.
- If the report format changes, `User` changes.

The class has too many responsibilities.

### ✅ Better Design
Separate the responsibilities:

```python
class UserService:
    def create_user(self):
        pass

class UserRepository:
    def save(self, user):
        pass

class EmailService:
    def send(self, email):
        pass

class ReportService:
    def generate(self):
        pass
```

Now each class has a focused responsibility:

```text
UserService
    ↓
UserRepository

EmailService

ReportService
```

### Interview Point
SRP does not mean one class should have only one method. It means the class should have one cohesive responsibility and one primary reason to change.

### SRP in a Library System
A Library system could easily become a large class:

```text
Library
 ├── addBook()
 ├── removeBook()
 ├── issueBook()
 ├── returnBook()
 ├── sendEmail()
 ├── calculateFine()
 ├── saveToDatabase()
 └── generateReport()
```

This creates a class with too many responsibilities.

A better design could be:

```text
LibraryService
    ↓
BookService

LoanService
    ↓
FineCalculator

NotificationService

BookRepository
```

Each component focuses on a specific responsibility.

---

## 2. Open/Closed Principle (OCP)

### Definition
Software entities should be open for extension but closed for modification.

In simple terms:  
Add new behavior without repeatedly modifying existing, stable code.

### ❌ Bad Design
Consider a payment service:

```python
class PaymentService:
    def pay(self, payment_type):
        if payment_type == "card":
            # Card payment logic
            pass
        elif payment_type == "upi":
            # UPI payment logic
            pass
```

Now suppose we want to add:
```text
Wallet
```

We have to modify the existing class:

```python
elif payment_type == "wallet":
    # Wallet payment logic
    pass
```

Every new payment type requires changes to the existing class.

### ✅ Better Design
Create a common payment abstraction:

```text
        PaymentMethod
             │
      ┌──────┼──────┐
      ↓      ↓      ↓
    Card    UPI   Wallet
```

Example:

```python
class PaymentMethod:
    def pay(self, amount):
        pass

class CardPayment(PaymentMethod):
    def pay(self, amount):
        pass

class UPIPayment(PaymentMethod):
    def pay(self, amount):
        pass

class WalletPayment(PaymentMethod):
    def pay(self, amount):
        pass
```

Now a new payment method can be added:

```python
class CryptoPayment(PaymentMethod):
    def pay(self, amount):
        pass
```

The existing payment implementations do not need to be changed.

### Key Idea
```text
Existing behavior
       ↓
Keep stable

New behavior
       ↓
Add through extension
```

---

## 3. Liskov Substitution Principle (LSP)

LSP is one of the concepts that beginners often find confusing.

### Definition
A child class should be able to replace its parent class without breaking the expected behavior of the system.

### ❌ Problematic Example
Consider:

```text
Bird
 ├── Sparrow
 └── Penguin
```

Suppose the parent class has:

```python
class Bird:
    def fly(self):
        pass
```

Now:

```python
class Penguin(Bird):
    def fly(self):
        raise Exception("Penguin cannot fly")
```

The problem is that the parent class expects all `Bird` objects to support `fly()`.  
But `Penguin` cannot satisfy that behavior.  
So the inheritance relationship is questionable.

### ✅ Better Design
Separate the general concept from the capability:

```text
Bird
 ├── Sparrow
 └── Penguin

FlyingBird
 └── Sparrow
```

Now:

```text
Bird
 ├── Sparrow
 └── Penguin

FlyingBird
 └── Sparrow
```

The design does not force every bird to support flying.

### Easy Rule
A child class should not break the behavior expected from its parent.

---

## 4. Interface Segregation Principle (ISP)

### Definition
Clients should not be forced to depend on methods they do not need.

In simple terms:  
Prefer small, focused interfaces instead of one large interface.

### ❌ Bad Design
Consider:

```python
class Worker:
    def work(self):
        pass

    def eat(self):
        pass
```

For a human worker:
- `work()` → Yes
- `eat()`  → Yes

But for a robot:
- `work()` → Yes
- `eat()`  → No

If the robot implements the entire interface, it is forced to provide an unnecessary `eat()` method.

### ✅ Better Design
Separate the interfaces:

```text
Workable
   │
   └── work()

Eatable
   │
   └── eat()
```

A human can implement both:

```text
Human
 ├── Workable
 └── Eatable
```

A robot only needs:

```text
Robot
 └── Workable
```

### Key Idea
Do not force a class to implement methods that it does not need.

---

## 5. Dependency Inversion Principle (DIP)

DIP is one of the most important SOLID principles for system design.

### Definition
High-level modules should not directly depend on low-level implementations. Both should depend on abstractions.

### ❌ Bad Design
Consider:

```python
class OrderService:
    def __init__(self):
        self.payment = StripePayment()
```

Here:

```text
OrderService
      ↓
StripePayment
```

`OrderService` is tightly coupled to `StripePayment`.  
If we want to change Stripe to Razorpay, the `OrderService` code needs to change.

### ✅ Better Design
Introduce an abstraction:

```text
        PaymentMethod
             ↑
      ┌──────┴──────┐
      │             │
StripePayment   RazorpayPayment
      ↑
      │
OrderService
```

Example:

```python
class OrderService:
    def __init__(self, payment):
        self.payment = payment
```

Now we can provide different implementations:

```python
stripe = StripePayment()
order = OrderService(stripe)
```

Or:

```python
razorpay = RazorpayPayment()
order = OrderService(razorpay)
```

`OrderService` does not need to know which payment implementation is being used.

### Dependency Injection
Dependency Inversion is closely related to Dependency Injection.

Suppose:

```text
OrderService
      ↓
PaymentService
```

`OrderService` needs `PaymentService`.

Instead of creating the dependency inside the class:

```python
class OrderService:
    def __init__(self):
        self.payment = StripePayment()
```

We provide the dependency from outside:

```python
class OrderService:
    def __init__(self, payment_service):
        self.payment_service = payment_service
```

This is called:
```text
Dependency Injection
```

The dependency is supplied from outside instead of being created directly inside the class.

---

## SOLID Mental Model

The five principles can be remembered like this:

```text
S → One responsibility
O → Extend without repeatedly modifying existing code
L → Child should safely replace parent
I → Keep interfaces small and focused
D → Depend on abstractions, not concrete implementations
```

---

## SOLID Interview Shortcut

- **S** → One Job
- **O** → Extend
- **L** → Substitute
- **I** → Small Interfaces
- **D** → Abstraction

---

## Why SOLID Matters in System Design

SOLID principles are especially useful when designing systems that are expected to grow.

For example, consider a system that initially supports:
```text
Card Payment
```

Later, it needs:
```text
UPI
Wallet
Net Banking
Razorpay
Stripe
```

A tightly coupled design may require changes across existing classes.  
A SOLID-based design allows new behavior to be added with fewer changes to existing code.

This improves:
- Maintainability
- Extensibility
- Testability
- Reusability
- Flexibility
- Separation of responsibilities

---

## Day 2 Mental Model

Think about SOLID as five design questions:

- **S** → Does this class have too many responsibilities?
- **O** → Can I add new behavior without constantly modifying existing code?
- **L** → Can the child safely replace the parent?
- **I** → Is this interface forcing unnecessary methods?
- **D** → Am I depending on an implementation instead of an abstraction?

If we ask these questions while designing classes and components, the code becomes easier to maintain as the system grows.

---

## Key Takeaways

- SRP keeps responsibilities focused.
- OCP makes systems easier to extend.
- LSP keeps inheritance behavior consistent.
- ISP keeps interfaces small and focused.
- DIP reduces tight coupling through abstractions.
- Dependency Injection helps provide dependencies from outside.
- SOLID principles help create maintainable and extensible software designs.

---

## Day 2 Summary

```text
SOLID
 │
 ├── S → Single Responsibility
 │
 ├── O → Open/Closed
 │
 ├── L → Liskov Substitution
 │
 ├── I → Interface Segregation
 │
 └── D → Dependency Inversion
```

Good design is not just about making code work. It is about making the code easier to change when requirements evolve.
