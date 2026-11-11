# Lab Guide: Writing Test Cases with JUnit and Mockito

Welcome to the hands-on lab session on **Writing Test Cases with JUnit and Mockito**!

In this session, you will learn how to write effective unit tests using JUnit and Mockito, with the assistance of AI agents like GitHub Copilot. You will apply the FIRST principles of test-driven development to ensure your tests are Fast, Independent, Repeatable, Self-validating, and Thorough. 

You can leverage these resources:
- [JUnit 6 User Guide](https://junit.org/junit5/docs/current/user-guide/)
- [Mockito Documentation](https://site.mockito.org/)

Before starting this lab, ensure you have completed the following:
- **Lab1_CICD.md**: Continuous Integration & Continuous Delivery (CI/CD) for Software Dependability

---

## 1. Prerequisites & Required Tools

Before starting, ensure you have configured the following environments and resources:

* **GitHub Account:** Register for student benefits via [GitHub Education](https://github.com/education).
* **GitHub Skills:** Access the hands-on interactive courses at [GitHub Skills](https://learn.github.com/skills).
* **Git & Java Development Kit (JDK) 21**.
* **IntelliJ IDEA or VS Code** with Java extensions installed.
* **GitHub Copilot** configured in your IDE.
* **Course Reference Repository:** Clone or update your local copy of `SorrentoMarina`:
  ```bash
  git clone https://github.com/dardin88/SorrentoMarina.git
  cd SorrentoMarina
  ```

---

## 2. Stage 1: Theory & Lecture Alignment

Review your lecture slides on **Unit Testing and Test-Driven Development**, keeping these core dependability principles in mind:

### Key Dependability Concepts in Testing

1. **Automated Verification & Fast Feedback Loops:** Unit tests automatically verify individual components, providing immediate feedback when code changes.
2. **Isolation & Deterministic Execution:** Tests should run in isolation without side effects, ensuring consistent results.
3. **Regression Prevention:** Comprehensive test suites prevent bugs from re-appearing in future code changes.
4. **Test Security & Data Integrity:** Tests should not expose sensitive data or modify production systems.

### FIRST Principles of Test-Driven Development

According to test-driven development principles, each test case should be developed aiming at five characteristics:

- **Fast**: It should exercise the production code as quickly as possible.
- **Independent**: It should not have dependencies on other tests.
- **Repeatable**: It should be repeated every time the production code is modified and should be deterministic (not flaky).
- **Self-validating**: No manual inspection is required to check whether the test has passed or failed.
- **Thorough**: It should cover every use case scenario and NOT just aim for 100% coverage.

---

## 3. Stage 2: GitHub Skills & Reference Test Inspection

In this stage, you will complete interactive GitHub training modules and inspect the reference test configuration in `SorrentoMarina`.

### Step 2.1: Audit `SorrentoMarina` Test Architecture

Inspect the test structure in the `SorrentoMarina` repository to understand how unit tests are organized.
Please consider that the examples were developed with JUnit 5. However, JUnit 6 is backward compatible with these tests.

```
src/test/java/theboys/sorrentomarina/
├── models/
│   ├── AnnuncioTest.java          # Tests for Annuncio model
│   ├── LidoTest.java             # Tests for Lido model
│   ├── OmbrelloneTest.java       # Tests for Ombrellone model
│   └── ... (other model tests)
├── managers/
│   └── ... (manager tests)
├── actions/
│   └── ... (action tests)
└── utils/
    └── ... (utility tests)
```

### Step 2.2: Examine Reference Test Implementation

Open `AnnuncioTest.java` and observe how tests follow the FIRST principles:

```java
package theboys.sorrentomarina.models;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import theboys.sorrentomarina.models.modelsAnnuncio.Annuncio;

import static org.junit.jupiter.api.Assertions.*;

/**
 * @author theboys
 */

public class AnnuncioTest {

  private Annuncio annuncio;

  /**
   * Metodo che inizializza un annuncio
   *
   */
  @BeforeEach
  public void init() {
    annuncio= new Annuncio(1, "Riapertura", "Grande riapertura il giorno 9 Giugno", 
                           "C:\\Users\\User\\Desktop\\Uni\\IS\\foto1.jpg", 1);
  }

  /**
   * Test per il metodo getId
   *
   */
  @Test
  public void getIdTest() {
    int id = annuncio.getId();
    assertEquals(1, id, "Should return 1");
  }

  /**
   * Test per il metodo getTitolo
   *
   */
  @Test
  public void getTitoloTest() {
    String titolo = annuncio.getTitolo();
    assertEquals("Riapertura", titolo, "Should return Riapertura");
  }

  /**
   * Test per il metodo create
   */
  @Test
  public void createTest(){
    Annuncio a = new Annuncio("asc", "adc", "acs", 1);
    assertNotNull(a);
  }

  /**
   * Test per il metodo toString
   */
  @Test
  public void toStringTest(){
    String result = annuncio.toString();
    assertEquals(result, "Annuncio[id=1,titolo=Riapertura,contenuto=Grande riapertura il giorno 9 Giugno,foto=C:\\Users\\User\\Desktop\\Uni\\IS\\foto1.jpg,id_Lido=1]");
  }
}
```

Key observations:
- **Fast**: Tests use `@BeforeEach` to set up test data efficiently
- **Independent**: Each test method is self-contained and doesn't depend on others
- **Repeatable**: Tests use fixed values and don't rely on external resources
- **Self-validating**: Assertions automatically determine pass/fail status
- **Thorough**: Tests cover getters, setters, creation, and toString() method

Let's also examine `LidoTest.java` for a more complex model:

```java
package theboys.sorrentomarina.models;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import theboys.sorrentomarina.models.modelsLido.Lido;

import static org.junit.jupiter.api.Assertions.assertEquals;

/**
 * @author theboys
 */
public class LidoTest {

  private Lido lido;

  /**
   * Metodo che inizializza un lido
   *
   */
  @BeforeEach
  public void init() {
    lido = new Lido(1, "Marinella", "Via Colombo", "082743123", 
                    "marinella@tiscali.it", "logo/logo", 10.5F, 2, 2, 1,1);
  }

  /**
   * test per il metodo getId
   */
  @Test
  public void getIdTest(){
    int id = lido.getId();
    assertEquals(1, id, "Should return 1");
  }

  /**
   * test per il metodo getNome
   */
  @Test
  public void getNomeTest(){
    String nome = lido.getNome();
    assertEquals("Marinella", nome, "Should return Marinella");
  }

  /**
   * test per il metodo setNome
   */
  @Test
  public void setNomeTest(){
    lido.setNome("Antonio");
    String nome = lido.getNome();
    assertEquals("Antonio", nome, "Should return Antonio");
  }

  /**
   * test per il metodo toString
   */
  @Test
  public void toStringTest(){
    String result = lido.toString();
    assertEquals(result, "Lido[id=1, nome=Marinella, indirizzo=Via Colombo, telefono=082743123, email=marinella@tiscali.it, prezzo_singolo=10.5]");
  }
}
```

This test demonstrates:
- Testing of a more complex model with multiple properties
- Use of `@BeforeEach` for efficient test setup
- Testing both getters and setters
- Clear assertion messages for debugging

---

## 4. Stage 3: IDE Setup & Dependency Configuration

### Step 3.1: Configure JUnit and Mockito Dependencies

Ensure you have the necessary dependencies in your `pom.xml` (Maven) or `build.gradle` (Gradle).

#### Maven Configuration:

```xml
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter</artifactId>
    <version>6.1.3</version>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-junit-jupiter</artifactId>
    <version>5.24.0</version>
    <scope>test</scope>
</dependency>
```

#### Gradle Configuration:

```groovy
testImplementation 'org.junit.jupiter:junit-jupiter:6.1.3'
testImplementation 'org.mockito:mockito-junit-jupiter:5.24.0'
```

### Step 3.2: Configure IDE for Testing
1. **Install your IDE**: Download and install the latest version from [JetBrains](https://www.jetbrains.com/idea/) or [VS Code](https://code.visualstudio.com/).
2. **Enable GitHub Copilot**: Follow the [official guide](https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/set-up-copilot) to set up GitHub Copilot in IntelliJ IDEA or VS Code.
3. **Run Tests**: Use the built-in test runner in IntelliJ IDEA or VS Code to execute your tests.
4. **Analyze your Tests**: [Chat with GitHub Copilot](https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/chat-with-copilot) to understand more about your test cases.

---

## 5. Stage 4: Course Project Application (Student Task)

Now it's time to apply what you've learned to your course project! Follow these steps to integrate testing into your project:

### Step 4.1: Identify Testable Components and Write Tests

Review your project and identify the key components that need unit tests:
- Service classes
- Utility functions
- Business logic
- Data transformation layers

For each component, write tests following the FIRST principles.

### Step 4.2: Use AI Agents

Leverage GitHub Copilot or other AI tools to help generate test cases, but always:
- Review the generated tests carefully
- Add missing edge cases
- Ensure tests follow the FIRST principles
- Verify test names are descriptive

The tests can use either JUnit 5 or JUnit 6 syntax since both work with the JUnit 6.1.3 dependency.

### Step 4.3: Mock External Dependencies with Mockito 5

Use Mockito 5 to mock any external services, databases, or APIs your project depends on.

### Step 4.4: Achieve Meaningful Coverage

Aim for comprehensive test coverage, focusing on:
- Critical paths in your application
- Edge cases and error conditions
- Business rules and validation logic
- Integration points between components

### Step 4.5: Integrate with CI/CD

Ensure your tests run automatically as part of your CI/CD pipeline and integrate with your existing workflow in `.github/workflows/ci.yml`.

```yaml
# Example GitHub Actions workflow
name: CI/CD Pipeline

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest
    name: Run Unit Tests
    
    steps:
    - name: Checkout code
      uses: actions/checkout@v5

    - name: Set up JDK 21
      uses: actions/setup-java@v4
      with:
        java-version: '21'
        distribution: 'temurin'

    - name: Run tests
      run: mvn test

    - name: Upload test results
      uses: actions/upload-artifact@v4
      if: always()
      with:
        name: test-results
        path: target/surefire-reports/
```

### Step 4.6: Document Your Testing Strategy

Add a README section explaining your testing approach, and create a report named `testing.md` (maximum **5,000 characters**) in your project repository and link to it from your root `README.md`.

---

## 6. Submission Checklist

Before submitting your lab, ensure you have completed the following:

- [ ] All unit tests follow the FIRST principles (Fast, Independent, Repeatable, Self-validating, Thorough)
- [ ] Tests are written using JUnit 6.1.3 and Mockito 5.24.0
- [ ] All critical components of your project have corresponding unit tests
- [ ] Tests are properly mocked where external dependencies are involved
- [ ] Test coverage is meaningful (focus on critical paths, not just percentage)
- [ ] Tests run successfully in your CI/CD pipeline
- [ ] Test documentation is included in your project's README
- [ ] All tests pass without errors or warnings
- [ ] Code is properly formatted and follows Java conventions
- [ ] No hardcoded secrets or sensitive data in test files