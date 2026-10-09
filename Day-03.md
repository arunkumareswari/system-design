# Day 3: Interfaces & Extensibility

## Introduction

Today, we will apply these concepts using interfaces, composition, dependency injection, and the Strategy Pattern.

The goal is to design a system that allows us to add new functionality without repeatedly modifying existing code.

We will use a **Payment System** as our main example.

## 1. What Is an Interface?

An **interface defines a contract** that specifies what behavior a class should provide, without necessarily defining how that behavior is implemented.

For example, every payment method in our system should provide a `pay()` method.

```python
class PaymentMethod:
    def pay(self, amount):
        pass
```

Here, `PaymentMethod` represents the expected behavior.

It says:

> Every payment method should provide a way to process a payment.

The actual payment logic can be different for each implementation.

**Note:** Python does not have a dedicated `interface` keyword like Java. We can create interface-like contracts using abstract base classes or other approaches.

## 2. Different Implementations

Different payment methods can implement the same behavior in their own way.

### Card Payment

```python
class CardPayment:
    def pay(self, amount):
        print(f"Paid ₹{amount} using Card")
```

### UPI Payment

```python
class UPIPayment:
    def pay(self, amount):
        print(f"Paid ₹{amount} using UPI")
```

### Wallet Payment

```python
class WalletPayment:
    def pay(self, amount):
        print(f"Paid ₹{amount} using Wallet")
```

Each class provides a `pay()` method, but the implementation can differ.

### Architecture

```text
             PaymentMethod
                   |
         +---------+---------+
         |         |         |
         v         v         v
       Card       UPI      Wallet
      Payment   Payment    Payment
```

The important idea is that different implementations can provide the same expected behavior.

## 3. Why Are Interfaces Useful?

Imagine that we are building an `OrderService` for an e-commerce application.

### Bad Design

```python
class OrderService:

    def pay(self, payment_type, amount):

        if payment_type == "card":
            # Card payment logic
            pass

        elif payment_type == "upi":
            # UPI payment logic
            pass

        elif payment_type == "wallet":
            # Wallet payment logic
            pass
```

Initially, this may work well.

However, later we may need to support:

- Net Banking
- Crypto payments
- PayPal
- New payment gateways

Every new payment method could require modifying the existing `OrderService`.

This makes the class harder to maintain as the system grows.

It also conflicts with the **Open/Closed Principle**, because adding new behavior repeatedly requires changes to existing code.

### Better Design

Instead of putting every payment implementation inside `OrderService`, let it depend on a common payment abstraction.

```python
class OrderService:

    def __init__(self, payment_method):
        self.payment_method = payment_method

    def checkout(self, amount):
        self.payment_method.pay(amount)
```

Now, the payment implementation can be provided from outside.

```python
card = CardPayment()

order = OrderService(card)
order.checkout(1000)
```

Output:

```text
Paid ₹1000 using Card
```

We can also use UPI:

```python
upi = UPIPayment()

order = OrderService(upi)
order.checkout(1000)
```

Output:

```text
Paid ₹1000 using UPI
```

The `OrderService` does not need to change when we switch between payment methods.

**Key takeaway:** Interfaces help separate what a component needs from how another component implements it.

## 4. Interface vs Abstract Class

Interfaces and abstract classes help define expected behavior, but they are not exactly the same in every programming language.

### Interface

An interface primarily defines a contract.

```text
PaymentMethod
      |
      v
    pay()
```

It specifies what an implementation should provide.

### Abstract Class

An abstract class can define a contract and also provide shared implementation.

In Python, we can use the `abc` module to create an abstract base class.

```python
from abc import ABC, abstractmethod


class PaymentMethod(ABC):

    @abstractmethod
    def pay(self, amount):
        pass
```

A concrete class must implement the abstract method before it can be instantiated.

```python
class CardPayment(PaymentMethod):

    def pay(self, amount):
        print(f"Paid ₹{amount} using Card")
```

Usage:

```python
payment = CardPayment()
payment.pay(1000)
```

Output:

```text
Paid ₹1000 using Card
```

If a subclass does not implement all required abstract methods, it cannot be instantiated.

### Quick Comparison

| Interface-like contract | Abstract class |
|---|---|
| Defines expected behavior | Defines expected behavior |
| Focuses on a contract | Can include shared implementation |
| Allows different implementations | Can provide common logic to subclasses |
| Python can model it with an ABC | Python supports it through `ABC` |

**Remember:** The exact distinction depends on the programming language.

## 5. Strategy Pattern

The **Strategy Pattern** is a behavioral design pattern that allows us to define multiple interchangeable implementations of an operation.

Instead of hardcoding one algorithm, we can select the appropriate strategy when needed.

For example, an application may support different payment strategies:

```text
             PaymentStrategy
                    |
          +---------+---------+
          |         |         |
          v         v         v
         Card      UPI      Wallet
       Payment   Payment    Payment
```

Each payment class represents a different strategy.

### Implementation

```python
class PaymentStrategy:

    def pay(self, amount):
        raise NotImplementedError


class CardPayment(PaymentStrategy):

    def pay(self, amount):
        print(f"Paid ₹{amount} using Card")


class UPIPayment(PaymentStrategy):

    def pay(self, amount):
        print(f"Paid ₹{amount} using UPI")


class WalletPayment(PaymentStrategy):

    def pay(self, amount):
        print(f"Paid ₹{amount} using Wallet")
```

Now we can create a checkout class that accepts any payment strategy.

```python
class Checkout:

    def __init__(self, payment_strategy):
        self.payment_strategy = payment_strategy

    def pay(self, amount):
        self.payment_strategy.pay(amount)
```

Usage:

```python
checkout = Checkout(UPIPayment())
checkout.pay(1500)
```

Output:

```text
Paid ₹1500 using UPI
```

To use card payment:

```python
checkout = Checkout(CardPayment())
checkout.pay(1500)
```

Output:

```text
Paid ₹1500 using Card
```

The `Checkout` class remains unchanged. Only the selected strategy changes.

### When Should We Use the Strategy Pattern?

Use the Strategy Pattern when one operation has multiple interchangeable behaviors or algorithms.

Examples include:

**Payment processing**

```text
Card | UPI | Wallet
```

**Navigation**

```text
Car | Bike | Walking
```

**Discount calculation**

```text
Regular | Festival | Premium
```

**Notifications**

```text
Email | SMS | Push Notification
```

The pattern helps avoid large conditional statements when behaviors need to be selected or extended independently.

## 6. Composition

Composition means building a class using another object as one of its components.

It is commonly described as a **HAS-A relationship**.

For example:

```text
Checkout
    |
    | HAS-A
    v
PaymentStrategy
```

A checkout object has a payment strategy. It does not need to inherit the implementation of a particular payment method.

### Inheritance-Based Approach

```python
class Checkout(CardPayment):
    pass
```

This tightly associates `Checkout` with the card payment implementation.

It becomes difficult to switch payment methods without changing the design.

### Composition-Based Approach

```python
class Checkout:

    def __init__(self, payment_strategy):
        self.payment_strategy = payment_strategy
```

Now `Checkout` contains a reference to a payment strategy.

```python
checkout = Checkout(CardPayment())
```

Or:

```python
checkout = Checkout(UPIPayment())
```

### Why Is Composition Useful?

- It reduces unnecessary inheritance.
- It allows components to be replaced independently.
- It supports flexible designs.
- It makes testing easier by allowing substitute implementations.

**Key takeaway:** Prefer composition when a class needs to use another object's behavior without becoming a specialized version of that object.

## 7. Dependency Injection

Dependency Injection (DI) is a technique in which an object receives its dependencies from outside rather than creating them internally.

Consider this example.

### Without Dependency Injection

```python
class Checkout:

    def __init__(self):
        self.payment_strategy = UPIPayment()
```

Here, `Checkout` creates a specific payment implementation.

If we want to use a different payment method, we need to modify the class or introduce additional selection logic.

### With Dependency Injection

```python
class Checkout:

    def __init__(self, payment_strategy):
        self.payment_strategy = payment_strategy
```

Now the dependency is provided externally.

```python
payment = UPIPayment()
checkout = Checkout(payment)

checkout.pay(1000)
```

We can supply a different implementation without modifying `Checkout`.

### Dependency Injection Flow

```text
Create Payment Implementation
           |
           v
     Pass to Checkout
           |
           v
    Checkout uses it
```

Dependency Injection helps reduce tight coupling and makes it easier to test a component with different implementations.

### Dependency Inversion vs Dependency Injection

These concepts are related, but they are not the same.

- **Dependency Inversion Principle (DIP):** A design principle that encourages depending on abstractions rather than concrete implementations.
- **Dependency Injection (DI):** A technique for supplying dependencies from outside a class.

DI can help implement DIP, but using DI alone does not automatically guarantee that the design follows DIP.

## 8. Putting Everything Together

Let's combine the concepts into one design.

### Architecture

```text
                  Checkout
                      |
                      | Depends on
                      v
              PaymentStrategy
                      |
          +-----------+-----------+
          |           |           |
          v           v           v
     CardPayment  UPIPayment  WalletPayment
```

### Complete Example

```python
from abc import ABC, abstractmethod


class PaymentStrategy(ABC):

    @abstractmethod
    def pay(self, amount):
        pass


class CardPayment(PaymentStrategy):

    def pay(self, amount):
        print(f"Paid ₹{amount} using Card")


class UPIPayment(PaymentStrategy):

    def pay(self, amount):
        print(f"Paid ₹{amount} using UPI")


class WalletPayment(PaymentStrategy):

    def pay(self, amount):
        print(f"Paid ₹{amount} using Wallet")


class Checkout:

    def __init__(self, payment_strategy):
        self.payment_strategy = payment_strategy

    def pay(self, amount):
        self.payment_strategy.pay(amount)


# Select UPI as the payment strategy
payment = UPIPayment()
checkout = Checkout(payment)

checkout.pay(2000)
```

Output:

```text
Paid ₹2000 using UPI
```

### How the Design Works

1. `PaymentStrategy` defines the contract.
2. `CardPayment`, `UPIPayment`, and `WalletPayment` implement the contract.
3. `Checkout` depends on the abstraction rather than a specific implementation.
4. Dependency Injection provides the selected payment strategy.
5. Composition allows `Checkout` to use the strategy without inheriting from it.
6. The Strategy Pattern makes the payment behavior interchangeable.

When a new payment method is added, we can create another implementation of `PaymentStrategy` and pass it to `Checkout`.

The existing checkout logic does not need to change.

## 9. How Does This Apply to System Design?

Consider an e-commerce application that initially supports UPI payments.

As the business grows, it needs to support card payments, wallets, and multiple payment gateways.

A tightly coupled implementation may require changes to the checkout logic whenever a new method is added.

With an abstraction-based design, the system can introduce new payment implementations independently.

The same approach can be applied to:

- Notification services
- File storage providers
- Authentication methods
- Shipping providers
- Discount engines
- Logging systems

This is useful when system requirements evolve and components need to be replaced, extended, or tested independently.

## 10. Interview Question

**Question:** Tomorrow, the application needs to support Net Banking. How would you extend the existing payment system?

**Weak approach:**

Add another `if-elif` condition inside the checkout logic.

**Better approach:**

Create a `NetBankingPayment` class that implements the `PaymentStrategy` abstraction. Then provide an instance of that class to `Checkout` through Dependency Injection.

```python
class NetBankingPayment(PaymentStrategy):

    def pay(self, amount):
        print(f"Paid ₹{amount} using Net Banking")
```

Usage:

```python
checkout = Checkout(NetBankingPayment())
checkout.pay(2500)
```

Output:

```text
Paid ₹2500 using Net Banking
```

The existing `Checkout` class remains unchanged.

**Interview answer:**

"I would introduce a new `NetBankingPayment` implementation of the payment strategy abstraction. Since `Checkout` depends on the abstraction rather than a concrete payment class, I can add the new payment method without modifying the existing checkout logic."

## Day 3 Mental Model

```text
Interface
    |
    v
Defines a contract

Composition
    |
    v
Builds a class using other objects

Dependency Injection
    |
    v
Provides dependencies from outside

Strategy Pattern
    |
    v
Makes behavior interchangeable

Extensibility
    |
    v
Adds new behavior with minimal changes
```

## Key Takeaways

- **Interfaces** define contracts between components.
- **Abstract classes** can define contracts and shared behavior.
- **Composition** allows a class to use another object's functionality.
- **Dependency Injection** provides dependencies from outside.
- **Strategy Pattern** supports interchangeable behaviors.
- **OCP** encourages extending systems without repeatedly modifying stable code.
- **DIP** encourages depending on abstractions instead of concrete implementations.

These concepts work together to make software easier to maintain, test, and extend.

> **Good design allows new functionality to be added without making existing components unnecessarily complex.**

---

