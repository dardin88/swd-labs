# Lab Guide: JML and OpenJML for Web Applications

Welcome to Lab 5! In this lab, you will learn how to apply **JML (Java Modeling Language)** specifications to web applications. You will add **preconditions**, **postconditions**, and **invariants** to Java classes and verify them using **OpenJML**.

You can leverage these resource:
- [OpenJML Installation Guide](https://www.openjml.org/installation.html)
- [OpenJML Tutorial](https://www.openjml.org/tutorial/)
- [JML Language Specification](https://www.openjml.org/jmlspecs/)

---

## 1. Prerequisites & Required Tools

Before starting this lab, ensure you have:
- Java Development Kit (JDK) installed
- OpenJML installed and available in your PATH
- Basic understanding of Java classes and methods

## 2. Stage 1: Setting Up OpenJML

To use OpenJML, you need to have it installed and available in your PATH. Follow the instructions at [https://www.openjml.org/installation.html](https://www.openjml.org/installation.html) to set up OpenJML.

## 3. Stage 2: Understanding JML Specifications

JML provides several key constructs for specifying behavior:

#### Preconditions (`requires`)
Preconditions specify the conditions that must be true before a method is called.

**Example:**
```java
/*@ public normal_behavior
  @   requires amount >= 0;  // Precondition: amount must be non-negative
  @   assignable balance;
  @   ensures balance == \old(balance) + amount;
  @*/
public void deposit(int amount) {
    balance += amount;
}
```

#### Postconditions (`ensures`)
Postconditions specify the conditions that must be true after a method completes.

**Example:**
```java
/*@ public normal_behavior
  @   requires amount >= 0 && amount <= balance;
  @   assignable balance;
  @   ensures balance == \old(balance) - amount;  // Postcondition: balance is reduced by amount
  @*/
public void withdraw(int amount) {
    balance -= amount;
}
```

#### Class Invariants (`invariant`)
Class invariants are conditions that must always hold true for the object's state.

**Example:**
```java
public class BankAccount {
    private int balance;

    /** Class invariant: the balance cannot be negative. */
    //@ public invariant balance >= 0;

    public BankAccount() {
        this.balance = 0;
    }
    
    // ... rest of the class
}
```

## 4. Stage 3: Using ESC and RAC

OpenJML provides two main modes for verification:

- **ESC (Escape Analysis)**: Static checking mode that verifies specifications without executing the code.
  ```bash
  openjml -esc User.java
  ```

- **RAC (Runtime Assertion Checking)**: Runtime checking mode that instruments the bytecode to check specifications at runtime.
  ```bash
  mkdir -p bin
  openjml -rac -d bin User.java
  java -ea -cp bin User
  ```

## 5. Stage 4: Example - Adding Specifications to a Web Application

Consider a simple `User` class for a web application:

```java
public class User {
    private String username;
    private String email;
    private int loginCount;

    public User(String username, String email) {
        this.username = username;
        this.email = email;
        this.loginCount = 0;
    }

    public String getUsername() {
        return username;
    }

    public String getEmail() {
        return email;
    }

    public int getLoginCount() {
        return loginCount;
    }

    public void login() {
        loginCount++;
    }

    public void logout() {
        if (loginCount > 0) {
            loginCount--;
        }
    }
}
```

#### Tasks
1. Add a **class invariant** to ensure `loginCount` is always non-negative.
2. Add a **precondition** to the `logout` method to ensure it is only called when the user is logged in.
3. Add a **postcondition** to the `login` method to ensure `loginCount` is incremented by 1.
4. Add a **postcondition** to the `logout` method to ensure `loginCount` is decremented by 1.
5. Use **ESC** to statically verify the specifications.
6. Use **RAC** to runtime-verify the specifications.

#### Solution
```java
public class User {
    private String username;
    private String email;
    private int loginCount;

    /** Class invariant: loginCount cannot be negative. */
    //@ public invariant loginCount >= 0;

    public User(String username, String email) {
        this.username = username;
        this.email = email;
        this.loginCount = 0;
    }

    /** Pure method to get the username. */
    /*@ public normal_behavior
      @   ensures \result == username;
      @   assignable \nothing;
      @*/
    public String getUsername() {
        return username;
    }

    /** Pure method to get the email. */
    /*@ public normal_behavior
      @   ensures \result == email;
      @   assignable \nothing;
      @*/
    public String getEmail() {
        return email;
    }

    /** Pure method to get the login count. */
    /*@ public normal_behavior
      @   ensures \result == loginCount;
      @   assignable \nothing;
      @*/
    public int getLoginCount() {
        return loginCount;
    }

    /** Increment the login count. */
    /*@ public normal_behavior
      @   assignable loginCount;
      @   ensures loginCount == \old(loginCount) + 1;
      @*/
    public void login() {
        loginCount++;
    }

    /** Decrement the login count if the user is logged in. */
    /*@ public normal_behavior
      @   requires loginCount > 0;  // Precondition: user must be logged in
      @   assignable loginCount;
      @   ensures loginCount == \old(loginCount) - 1;  // Postcondition: loginCount is decremented
      @*/
    public void logout() {
        if (loginCount > 0) {
            loginCount--;
        }
    }
}
```

## 6. Stage 5: Software Components Suitable for Formal Verification in Web Applications

When applying formal verification techniques like JML to web applications, focus on:

- **Business logic classes**: Classes that encapsulate core business rules (e.g., `UserService`, `OrderService`).
- **Data validation**: Methods that validate user input or ensure data integrity.
- **State management**: Classes that manage application state (e.g., `SessionManager`, `CacheManager`).
- **Security checks**: Methods that enforce security policies (e.g., `AuthenticationService`, `AuthorizationService`).

Avoid formal verification for:
- **Controller classes**: These typically delegate to service classes and are less suitable for formal verification.
- **View/presentation logic**: Classes responsible for rendering views or formatting output.
- **Third-party integrations**: Classes that interact with external APIs or services.

## Submission Checklist

Before submitting your work, ensure you have:
- [ ] Set up OpenJML in your project.
- [ ] Added preconditions, postconditions, and invariants to the `User` class.
- [ ] Used ESC to statically verify the specifications.
- [ ] Used RAC to runtime-verify the specifications.
- [ ] Identified software components suitable for formal verification in web applications.
- [ ] Interpreted verification results and debugged specification errors.
- [ ] Documented your approach and findings in a `verification.md` file (max 5,000 characters).