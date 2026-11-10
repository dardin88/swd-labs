# Lab Guide: Code Coverage and Mutation Testing for Software Dependability

Welcome to the hands-on lab session on **Code Coverage and Mutation Testing**!

In this session, you will learn how to measure and improve your test suite's effectiveness using **JaCoCo** for code coverage analysis and **PITest** for mutation testing. You will also integrate these metrics into your **CI/CD pipeline** using **CodeCov** for visualization and tracking. This lab builds on what you learned in **Lab 3: Writing Test Cases with JUnit and Mockito**, where you created comprehensive unit tests.

You can leverage these resource:
- [JaCoCo Documentation](https://www.jacoco.org/jacoco/)
- [PITest Documentation](https://pitest.org/)
- [CodeCov Documentation](https://docs.codecov.com/)
- [Baeldung Guide on Mutation Testing](https://www.baeldung.com/java-mutation-testing-with-pitest)

---

## 1. Prerequisites & Required Tools

Before starting, ensure you have configured the following environments and resources:

* **GitHub Account:** Register for student benefits via [GitHub Education](https://github.com/education).
* **GitHub Skills:** Access the hands-on interactive courses at [GitHub Skills](https://learn.github.com/skills).
* **Git & Java Development Kit (JDK) 21**.
* **Maven** or **Gradle**.
* **IntelliJ IDEA or VS Code** with Java extensions installed.
* **Course Reference Repository:** Clone or update your local copy of `SorrentoMarina`:
  ```bash
  git clone https://github.com/dardin88/SorrentoMarina.git
  cd SorrentoMarina
  ```

---

## 2. Stage 1: Theory & Lecture Alignment

Review your lecture slides on **Code Coverage and Mutation Testing**, keeping these core dependability principles in mind:

### Key Dependability Concepts

1. **Test Effectiveness Measurement:** Code coverage metrics help identify untested code paths, ensuring comprehensive validation.
2. **Mutation Testing:** Simulates bugs by modifying code (mutations) to verify if tests can detect them, providing deeper insight into test quality.
3. **Continuous Quality Monitoring:** Integrating coverage and mutation metrics into CI/CD enables ongoing quality assessment.
4. **Visualization & Reporting:** Clear reports help developers understand testing gaps and focus improvement efforts.

### Code Coverage vs. Mutation Testing

| Metric | Purpose | Tool |
|--------|---------|------|
| **Code Coverage** | Measures what code is executed by tests | JaCoCo |
| **Mutation Score** | Measures how well tests detect artificial bugs | PITest |
| **Line Coverage** | % of lines executed | JaCoCo |
| **Branch Coverage** | % of decision outcomes tested | JaCoCo |
| **Mutation Score** | % of mutations killed by tests | PITest |

---

## 3. Stage 2: Local Code Coverage Analysis with JaCoCo

In this stage, you will configure and run JaCoCo locally to analyze your test coverage.

### Step 2.1: Add JaCoCo to Your Maven Project

Add the JaCoCo Maven plugin to your `pom.xml` file:

```xml
<build>
    <plugins>
        <!-- JaCoCo Plugin for code coverage -->
        <plugin>
            <groupId>org.jacoco</groupId>
            <artifactId>jacoco-maven-plugin</artifactId>
            <version>LATEST</version>
            <executions>
                <execution>
                    <goals>
                        <goal>prepare-agent</goal>
                    </goals>
                </execution>
                <execution>
                    <id>report</id>
                    <phase>test</phase>
                    <goals>
                        <goal>report</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```

### Step 2.2: Run JaCoCo Locally

Execute the following Maven command to run tests with coverage:

```bash
# Run tests with coverage
mvn clean test

# Generate coverage report
mvn jacoco:report

# View the report
open target/site/jacoco/index.html
```

The report will open in your default browser, showing:
- Line coverage (green = covered, red = uncovered)
- Branch coverage
- Method coverage
- Class coverage

---

## 4. Stage 3: Mutation Testing with PITest

In this stage, you will use PITest to perform mutation testing on your code.

### Step 3.1: Add PITest to Your Maven Project

Add the PITest Maven plugin to your `pom.xml` file:

```xml
<build>
    <plugins>
        <!-- PITest Plugin for mutation testing -->
        <plugin>
            <groupId>org.pitest</groupId>
            <artifactId>pitest-maven</artifactId>
            <version>LATEST</version>
            <dependencies>
                <dependency>
                    <groupId>org.pitest</groupId>
                    <artifactId>pitest-junit5-plugin</artifactId>
                    <version>LATEST</version>
                </dependency>
            </dependencies>
            <configuration>
                <targetClasses>
                    <param>it.unisa.dardin88.sorrentomarina.*</param>
                </targetClasses>
                <targetTests>
                    <param>it.unisa.dardin88.sorrentomarina.*</param>
                </targetTests>
                <!-- Optional: Customize mutation operators
                <mutators>
                    <mutator>ALL_MUTATORS</mutator>
                </mutators>
                -->
                <verbose>true</verbose>
                <outputFormats>
                    <outputFormat>HTML</outputFormat>
                    <outputFormat>XML</outputFormat>
                </outputFormats>
            </configuration>
        </plugin>
    </plugins>
</build>
```

### Step 3.2: Configuring Mutation Operators in PITest

By default, PITest uses a standard set of mutation operators. You can customize which operators are used by adding a `<mutationThreshold>` configuration to your `pom.xml`.

#### Available Mutation Operators

PITest includes various mutation operators that simulate different types of bugs. You can find the complete list of mutation operators in the official PITest documentation:

**[PITest Mutation Operators Documentation](https://pitest.org/quickstart/mutation_operators/)**

Common mutation operators include:
- **Conditionals Boundary** - Changes relational operators (<, >, <=, >=)
- **Conditional Negator** - Changes conditionals (&&, ||)
- **Invert Negatives** - Inverts negative numbers
- **Increments** - Increments variables
- **Math** - Returns zero for mathematical operations
- **Negate Conditionals** - Inverts conditional expressions
- **Return Empty Values** - Returns default values from methods
- **Void Method Calls** - Removes void method calls
- **Remove Increments** - Removes increment operations

#### Example Configuration: Specifying Custom Mutation Operators

To configure specific mutation operators, add the `<mutators>` configuration to your `pom.xml`:

```xml
<plugin>
    <groupId>org.pitest</groupId>
    <artifactId>pitest-maven</artifactId>
    <version>LATEST</version>
    <dependencies>
        <dependency>
            <groupId>org.pitest</groupId>
            <artifactId>pitest-junit5-plugin</artifactId>
            <version>LATEST</version>
        </dependency>
    </dependencies>
    <configuration>
        <targetClasses>
            <param>it.unisa.dardin88.sorrentomarina.*</param>
        </targetClasses>
        <targetTests>
            <param>it.unisa.dardin88.sorrentomarina.*</param>
        </targetTests>
        <mutators>
            <mutator>CONDITIONALS_BOUNDARY</mutator>
            <mutator>NEGATE_CONDITIONALS</mutator>
            <mutator>INVERT_NEGS</mutator>
            <mutator>MATH</mutator>
        </mutators>
        <verbose>true</verbose>
        <outputFormats>
            <outputFormat>HTML</outputFormat>
            <outputFormat>XML</outputFormat>
        </outputFormats>
    </configuration>
</plugin>
```

---

### Step 3.3: Run PITest Locally

Execute the following Maven command to run mutation testing:

```bash
# Run mutation testing
mvn pitest:mutationCoverage
```

The report is located in `target/pit-reports/index.html` and will show:
- **Survived Mutations**: Mutations not detected by any test (red flag - tests need improvement)
- **Killed Mutations**: Mutations detected by tests (green - good!)
- **Timed Out Mutations**: Mutations that timed out during testing
- **No Coverage Mutations**: Mutations in code not covered by tests
- **Source Code View**: Click on classes to see specific mutations

---

## 5. Stage 4: CI/CD Integration with CodeCov

In this stage, you will integrate code coverage and mutation testing into your CI/CD pipeline using CodeCov.

### Step 4.1: Set Up CodeCov

1. **Create a CodeCov Account**:
   - Go to [CodeCov](https://codecov.io/)
   - Sign up with your GitHub account
   - Add your `SorrentoMarina` repository

2. **Generate a CodeCov Token**:
   - In your repository settings on CodeCov
   - Copy the token for later use

3. **Add Token to GitHub Secrets**:
   - Go to your GitHub repository > Settings > Secrets > Actions
   - Add a new secret named `CODECOV_TOKEN` with your CodeCov token

### Step 4.2: Update Your CI/CD Pipeline

Add the following steps to your `.github/workflows/ci.yml` file (after the test step):

```yaml
- name: Generate JaCoCo report
  run: mvn jacoco:report

- name: Generate test report
  uses: dorny/test-reporter@v2
  if: success() || failure()
  with:
    name: Maven Test Results
    path: target/surefire-reports/*.xml
    reporter: java-junit
    fail-on-error: false

- name: Upload coverage to Codecov
  uses: codecov/codecov-action@v4
  if: success() || failure()
  with:
    token: ${{ secrets.CODECOV_TOKEN }}
    file: ./target/site/jacoco/jacoco.xml
    flags: unittests
    name: codecov-umbrella
    fail_ci_if_error: false

- name: Upload test results to Codecov
  if: ${{ !cancelled() }}
  uses: codecov/test-results-action@v1
  with:
    token: ${{ secrets.CODECOV_TOKEN }}
    file: ./target/surefire-reports/TEST-*.xml

- name: Run PiTest mutation testing
  run: mvn org.pitest:pitest-maven:mutationCoverage
  continue-on-error: true

- name: Prepare reports for GitHub Pages
  if: github.ref == 'refs/heads/main' && always()
  run: |
    mkdir -p github-pages/PiTest
    mkdir -p github-pages/Jacoco
    if [ -d "target/pit-reports" ]; then
      cp -r target/pit-reports/* github-pages/PiTest/ || echo "No PiTest reports found"
    fi
    if [ -d "target/site/jacoco" ]; then
      cp -r target/site/jacoco/* github-pages/Jacoco/ || echo "No JaCoCo reports found"
    fi

- name: Deploy reports to GitHub Pages
  uses: actions/upload-pages-artifact@v3
  if: github.ref == 'refs/heads/main' && always()
  with:
    path: ./github-pages

- name: Deploy to GitHub Pages
  uses: actions/deploy-pages@v4
  if: github.ref == 'refs/heads/main' && always()
  id: deployment
```

### Step 4.3: Configure CodeCov Badges

Add badges to your repository's README.md:

```markdown
[![Code Coverage](https://codecov.io/gh/yourusername/SorrentoMarina/branch/main/graph/badge.svg?token=YOUR_TOKEN)](https://codecov.io/gh/yourusername/SorrentoMarina)
[![Mutation Score](https://badge.pitest.org/api/v1/project/yourusername/SorrentoMarina.svg)](https://badge.pitest.org/api/v1/project/yourusername/SorrentoMarina)
```

---

## 6. Stage 5: Course Project Application (Student Task)

Now it's time to apply what you've learned to your course project!

### Step 5.1: Configure JaCoCo and PITest

1. Add JaCoCo and PITest plugins to your `pom.xml`
2. Run coverage analysis locally to establish a baseline
3. Run mutation testing to identify test gaps

### Step 5.2: Set Up CI/CD Integration

1. Create a CodeCov account and generate a token
2. Add the token to GitHub Secrets
3. Update your CI/CD pipeline with the coverage and mutation testing steps
4. Push changes to trigger the pipeline

### Step 5.3: Analyze and Improve

1. **Review Coverage Report**: Identify classes/methods with low coverage
2. **Review Mutation Report**: Focus on survived mutations
3. **Write Additional Tests**: Add tests for uncovered code paths
4. **Iterate**: Re-run coverage and mutation testing to track improvements

### Step 5.4: Set Quality Gates

Add quality gates to your pipeline (optional but recommended):

```yaml
- name: Enforce coverage thresholds
  run: |
    COVERAGE=$(grep -oP '\d+(?=%)' target/site/jacoco/index.html | head -1)
    if [ "$COVERAGE" -lt 80 ]; then
      echo "Code coverage is below 80%: $COVERAGE%"
      exit 1
    fi
```

---

## 7. Submission Checklist

Before submitting your lab, ensure you have completed the following:

- [ ] Added JaCoCo plugin to `pom.xml`
- [ ] Generated and analyzed JaCoCo coverage report locally
- [ ] Added PITest plugin to `pom.xml`
- [ ] Generated and analyzed PITest mutation report locally
- [ ] Created CodeCov account and generated token
- [ ] Added CODECOV_TOKEN to GitHub Secrets
- [ ] Updated CI/CD pipeline with coverage and mutation testing steps
- [ ] Verified pipeline runs successfully
- [ ] Reviewed coverage report and identified areas for improvement
- [ ] Reviewed mutation report and focused on survived mutations
- [ ] Added quality gates (optional but recommended)
- [ ] All tests pass in the pipeline

---

## 8. Troubleshooting

### Common Issues and Solutions

**JaCoCo Issues:**
- *Error: JaCoCo agent not found*: Ensure the plugin is properly configured in `pom.xml`
- *No coverage data*: Run `mvn clean test` before generating the report
- *Partial coverage*: Check if all test classes are included in the test execution

**PITest Issues:**
- *Out of memory*: Increase heap size with `-Xmx2g` option
- *Slow execution*: Focus on specific packages with `targetClasses` configuration
- *No mutations*: Verify your code has testable logic (mutations work on conditional statements, arithmetic, etc.)

**CodeCov Issues:**
- *Upload failed*: Verify the CODECOV_TOKEN is correct in GitHub Secrets
- *No data*: Check that `jacoco.xml` is generated in the correct location
- *Permission denied*: Ensure the repository is properly connected to CodeCov

**Performance Tips:**
- Run PITest on specific packages rather than the entire codebase
- Use `continue-on-error: true` for PITest in CI to avoid blocking the pipeline
- Schedule mutation testing to run nightly or on specific branches
