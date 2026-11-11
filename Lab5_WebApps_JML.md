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

## 6. Stage 5: Course Project Application (Student Task)

Now it's time to apply what you've learned to your course project! Follow these steps to add formal specifications and verify them:

### Step 5.1: Identify Verifiable Components

Review your project and identify the key components that can benefit from formal specifications. When applying formal verification techniques like JML to web applications, focus on:

**Components Suitable for Formal Verification:**
- **Service classes**: Core business logic (e.g., `UserService`, `OrderService`, `PaymentService`)
- **Data validation**: Input validation and data transformation layers
- **State management**: Classes that manage application state (e.g., `SessionManager`, `CacheManager`)
- **Security checks**: Methods that enforce security policies (e.g., `AuthenticationService`, `AuthorizationService`)

**Components to Avoid:**
- Controller classes (these typically delegate to service classes and are less suitable for formal verification)
- View/presentation logic classes (responsible for rendering views or formatting output)
- Third-party integration classes (classes that interact with external APIs or services)

### Step 5.2: Add JML Specifications

For each identified component, add appropriate JML specifications:

1. **Class Invariants**: Define invariants that must always hold true for the object's state
2. **Preconditions**: Specify requirements for method parameters and object state
3. **Postconditions**: Specify guarantees about method behavior and side effects
4. **Pure Methods**: Mark getter methods and other non-modifying methods as pure

**Example for a UserService class:**
```java
public class UserService {
    private Map<String, User> users = new HashMap<>();
    
    /** Class invariant: users map cannot be null. */
    //@ public invariant users != null;
    
    /** Add a user to the service. */
    /*@ public normal_behavior
      @   requires user != null && !username.isEmpty();
      @   assignable users;
      @   ensures users.containsKey(username);
      @   ensures \result == true;
      @*/
    public boolean addUser(String username, User user) {
        users.put(username, user);
        return true;
    }
    
    /** Get a user by username. */
    /*@ public normal_behavior
      @   requires username != null;
      @   assignable \nothing;
      @   ensures \result != null ==> \result.getUsername().equals(username);
      @*/
    public User getUser(String username) {
        return users.get(username);
    }
}
```

### Step 5.3: Configure OpenJML

Set up OpenJML for your project:

1. Add OpenJML to your project's build configuration
2. Configure your IDE to recognize JML annotations
3. Set up appropriate build scripts or Maven/Gradle plugins

### Step 5.4: Static Verification with ESC

Use ESC to statically verify your specifications:

```bash
# Verify a single file
openjml -esc src/main/java/com/example/UserService.java

# Verify all Java files in a directory
find src/main/java -name "*.java" -exec openjml -esc {} \;
```

Address any verification errors by:
- Refining your specifications
- Fixing implementation bugs
- Adjusting invariants or preconditions

### Step 5.5: Runtime Verification with RAC

Use RAC to runtime-verify your specifications:

```bash
# Compile with RAC
mkdir -p target/rac
openjml -rac -d target/rac src/main/java/com/example/UserService.java

# Run with assertions enabled
java -ea -cp target/rac com.example.UserService
```

Test edge cases and error conditions to ensure your specifications hold at runtime.

### Step 5.6: Document Your JML Verification Results

After completing the verification tasks, document your findings in a `verification.md` (maximum **5,000 characters**) file in your project's root directory. This file should include:

- **Verification Setup**: Describe how you set up OpenJML and configured your project for verification.
- **Specifications Added**: List all the preconditions, postconditions, and invariants you added to your project's classes.
- **ESC Results**: Report the results of static verification using ESC. Include any errors or warnings encountered and how you addressed them.
- **RAC Results**: Report the results of runtime verification using RAC. Include any assertion failures and how you debugged them.
- **Verification Success**: Confirm which specifications were successfully verified and which ones failed.
- **Lessons Learned**: Discuss what you learned about writing effective JML specifications and the challenges you encountered.

---

## Submission Checklist

Before submitting your work, ensure you have:
- [ ] Set up OpenJML in your project.
- [ ] Identified verifiable components in your course project.
- [ ] Added preconditions, postconditions, and invariants to your project's core classes.
- [ ] Used ESC to statically verify the specifications.
- [ ] Used RAC to runtime-verify the specifications.
- [ ] Documented your JML verification results in a `verification.md` file (max 5,000 characters).
- [ ] Interpreted verification results and debugged specification errors.
- [ ] Committed the `verification.md` file to your GitHub repository.