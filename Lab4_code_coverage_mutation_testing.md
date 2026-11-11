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
* **Maven** (this guide uses Maven, but similar steps apply to Gradle).
* **IntelliJ IDEA or VS Code** with Java extensions installed.
* **Course Reference Repository:** Clone or update your local copy of `SorrentoMarina`:
  ```bash
  git clone https://github.com/dardin88/SorrentoMarina.git
  cd SorrentoMarina
  ```

---

### Gradle Quick Reference

If you're using **Gradle** instead of Maven, here's a quick reference:
* [Jacoco Plugin for Gradle](https://docs.gradle.org/current/userguide/jacoco_plugin.html)
* [PiTest Plugin for Gradle](https://gradle-pitest-plugin.solidsoft.info)

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
            <version>0.8.12</version>
            <executions>
                <execution>
                    <id>prepare-agent</id>
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

### Step 2.3: Adding Quality Gates to JaCoCo

**Quality gates** are thresholds that enforce minimum quality standards. When coverage falls below these thresholds, your build will fail automatically.

#### Understanding Quality Gates

| Metric | Minimum Threshold | Description |
|--------|-------------------|-------------|
| **Line Coverage** | 80% | % of code lines executed by tests |
| **Branch Coverage** | 75% | % of decision outcomes tested |
| **Class Coverage** | 100% | All classes must have at least one test |

#### Adding JaCoCo Quality Gates to pom.xml

Add the following code to your JaCoCo plugin configuration to enforce quality gates:

```xml
<plugin>
    <groupId>org.jacoco</groupId>
    <artifactId>jacoco-maven-plugin</artifactId>
    <version>0.8.12</version>
    <executions>
        <execution>
            <id>prepare-agent</id>
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
        <execution>
            <id>check</id>
            <goals>
                <goal>check</goal>
            </goals>
            <phase>verify</phase>
            <configuration>
                <rules>
                    <rule>
                        <element>PACKAGE</element>
                        <limits>
                            <limit>
                                <counter>LINE</counter>
                                <value>COVEREDRATIO</value>
                                <minimum>0.80</minimum>
                            </limit>
                            <limit>
                                <counter>BRANCH</counter>
                                <value>COVEREDRATIO</value>
                                <minimum>0.75</minimum>
                            </limit>
                            <limit>
                                <counter>CLASS</counter>
                                <value>COVEREDRATIO</value>
                                <minimum>1.0</minimum>
                            </limit>
                        </limits>
                    </rule>
                </rules>
            </configuration>
        </execution>
    </executions>
</plugin>
```

#### Running JaCoCo Quality Gates

To run JaCoCo with quality gates:

```bash
# Run tests with coverage and quality gates
mvn clean verify

# This will fail if coverage is below the defined thresholds!
```

#### Understanding Quality Gate Failures

If coverage falls below the thresholds, you'll see an error like:

```
[ERROR] RULEViolation: Package has 0.72 branch coverage ratio which is below 0.75
```

**To fix:**
1. Review the JaCoCo HTML report: `target/site/jacoco/index.html`
2. Add tests for uncovered code paths
3. Re-run `mvn verify`

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
            <version>1.16.0</version>
            <dependencies>
                <dependency>
                    <groupId>org.pitest</groupId>
                    <artifactId>pitest-junit5-plugin</artifactId>
                    <version>1.2.1</version>
                </dependency>
            </dependencies>
            <configuration>
                <targetClasses>
                    <param>it.unisa.dardin88.sorrentomarina.*</param>
                </targetClasses>
                <targetTests>
                    <param>it.unisa.dardin88.sorrentomarina.*</param>
                </targetTests>
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

By default, PITest uses a standard set of mutation operators. You can customize which operators are used by adding a `<mutators>` configuration to your `pom.xml`.

#### Available Mutation Operators

PITest includes various mutation operators that simulate different types of bugs. You can find the complete list of mutation operators in the [official PITest documentation](https://pitest.org/quickstart/mutation_operators/).

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
    <version>1.16.0</version>
    <dependencies>
        <dependency>
            <groupId>org.pitest</groupId>
            <artifactId>pitest-junit5-plugin</artifactId>
            <version>1.2.1</version>
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

### Step 3.3: Adding Quality Gates to PITest

PITest includes quality gate thresholds that enforce minimum mutation scores:

#### Understanding Quality Gate Thresholds

| Metric | Minimum Threshold | Description |
|--------|-------------------|-------------|
| **Mutation Score** | 80% | % of mutations killed by tests |
| **Coverage Threshold** | 80% | % of code covered by tests |

#### Adding PITest Quality Gates to pom.xml

Add these configuration parameters to your PITest plugin:

```xml
<plugin>
    <groupId>org.pitest</groupId>
    <artifactId>pitest-maven</artifactId>
    <version>1.16.0</version>
    <dependencies>
        <dependency>
            <groupId>org.pitest</groupId>
            <artifactId>pitest-junit5-plugin</artifactId>
            <version>1.2.1</version>
        </dependency>
    </dependencies>
    <configuration>
        <targetClasses>
            <param>it.unisa.dardin88.sorrentomarina.*</param>
        </targetClasses>
        <targetTests>
            <param>it.unisa.dardin88.sorrentomarina.*</param>
        </targetTests>
        <mutationThreshold>80</mutationThreshold>
        <coverageThreshold>80</coverageThreshold>
        <timeoutConstant>5000</timeoutConstant>
        <timeoutFactor>1.5</timeoutFactor>
        <outputFormats>
            <outputFormat>HTML</outputFormat>
            <outputFormat>XML</outputFormat>
        </outputFormats>
        <!-- Optional: Exclusions from mutation testing
        <excludedClasses>
            <param>*.entity.*</param>
            <param>*.dto.*</param>
        </excludedClasses>
        -->
        <!-- Optional: Custom mutation operators
        <mutators>
            <mutator>CONDITIONALS_BOUNDARY</mutator>
            <mutator>NEGATE_CONDITIONALS</mutator>
        </mutators>
        -->
    </configuration>
</plugin>
```

#### Running PITest with Quality Gates

```bash
# Run mutation testing with quality gates
mvn clean verify
# or specifically
mvn pitest:mutationCoverage
```

The build will fail if the mutation score falls below 80%.

#### Understanding Quality Gate Failures

If the mutation score falls below the threshold, you'll see an error:

```
[ERROR] Mutation coverage is 75%, which is below the threshold of 80%
```

### Step 3.4: Run PITest Locally

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

- name: Run JaCoCo quality gates
  run: mvn verify
  # This will fail if coverage is below thresholds!

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

- name: Run PITest with quality gates
  run: mvn verify
  # This will fail if mutation score is below 80%
```

**Note**: Be careful about computing mutation score in your CI/CD pipeline as you may quickly exceed your GitHub Actions quota. Consider using a separate GitHub Actions workflow for mutation testing.

### Step 4.3: Configure CodeCov Badges

Add badges to your repository's README.md:

```markdown
[![Code Coverage](https://codecov.io/gh/yourusername/SorrentoMarina/branch/main/graph/badge.svg?token=YOUR_TOKEN)](https://codecov.io/gh/yourusername/SorrentoMarina)
[![Mutation Score](https://badge.pitest.org/api/v1/project/yourusername/SorrentoMarina.svg)](https://badge.pitest.org/api/v1/project/yourusername/SorrentoMarina)
```

---

## 6. Stage 5: Course Project Application (Student Task)

Now it's time to apply what you've learned to your course project!

### Step 5.1: Configure JaCoCo with Quality Gates

1. Add JaCoCo plugin to your `pom.xml`
2. Include the quality gates configuration
3. Run `mvn clean verify` to enforce quality gates
4. Generate coverage analysis and identify classes/methods with low coverage
5. Develop tests to improve coverage

### Step 5.2: Configure PITest with Quality Gates

1. Add PITest plugin to your `pom.xml`
2. Include quality gates
3. Run `mvn pitest:mutationCoverage`
4. Identify survived mutations that need better tests
5. Develop tests to improve coverage

### Step 5.3: Set Up CI/CD Integration

1. Create a CodeCov account and generate a token
2. Add the token to GitHub Secrets
3. Update your CI/CD pipeline with coverage and mutation testing steps

### Step 5.4: Analyze and Improve

1. **Review Coverage Report**: Identify classes/methods with low coverage
2. **Review Mutation Report**: Focus on survived mutations
3. **Write Additional Tests**: Add tests for uncovered code paths
4. **Iterate**: Re-run quality gates to track improvements

### Step 5.5: Document Your Coverage

Create a report named `coverage.md` (maximum **5,000 characters**) in your project repository and link to it from your root `README.md`. Your report must include:

1. **Coverage Configuration**: Explain the JaCoCo plugin configuration in your `pom.xml`, including version, executions, and quality gate thresholds.

2. **Coverage Analysis**: Describe the coverage results, including:
   - Line coverage percentage
   - Branch coverage percentage
   - Classes with lowest coverage
   - Methods not covered by tests

3. **Coverage Improvement Plan**: Based on your analysis, describe which areas need additional test coverage and how you plan to improve coverage in future iterations.

### Step 5.6: Document Your Mutation Score

Create a report named `mutation.md` (maximum **5,000 characters**) in your project repository and link to it from your root `README.md`. Your report must include:

1. **Mutation Testing Configuration**: Explain the PITest plugin configuration, including target classes, target tests, quality gates, and output formats.

2. **Mutation Analysis**: Describe the mutation testing results, including:
   - Mutation score
   - Number of killed vs. survived mutations
   - Common patterns in survived mutations
   - Areas with low mutation coverage

3. **Mutation Score Improvement Plan**: Based on your analysis, describe which areas need additional mutation score and how you plan to improve mutation score in future iterations.

---

## 7. Submission Checklist

Before submitting your lab, ensure you have completed the following:

- [ ] Added JaCoCo plugin with quality gates to `pom.xml`
- [ ] Configured coverage thresholds
- [ ] Generated and analyzed JaCoCo coverage report locally
- [ ] Added PITest plugin with quality gates to `pom.xml`
- [ ] Configured mutation threshold
- [ ] Generated and analyzed PITest mutation report locally
- [ ] Created CodeCov account and generated token
- [ ] Added CODECOV_TOKEN to GitHub Secrets
- [ ] Updated CI/CD pipeline with coverage and mutation testing steps
- [ ] Verified pipeline runs successfully
- [ ] Reviewed coverage report and identified areas for improvement
- [ ] Reviewed mutation report and focused on survived mutations
- [ ] Documented coverage strategy in `coverage.md` (max 5,000 characters)
- [ ] Documented mutation testing strategy in `mutation.md` (max 5,000 characters)
- [ ] Added coverage and mutation badges to README.md
- [ ] All tests pass in the pipeline (quality gates should pass too!)