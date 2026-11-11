# Lab Guide: Microbenchmarking with Java Microbenchmark Harness (JMH)

Welcome to the hands-on lab session on **Microbenchmarking with Java Microbenchmark Harness (JMH)**!

In this session, you will learn how to write and run microbenchmarks using JMH to measure the performance of your Java code. You will also explore JU2JMH to convert existing test cases into benchmarks and AMBER to configure warm-up phases. This lab builds on what you learned in **Lab 3: Writing Test Cases with JUnit and Mockito**, where you created comprehensive unit tests.

You can leverage these resources:
- [JMH Official Documentation](https://github.com/openjdk/jmh)
- [JMH Examples](https://github.com/openjdk/jmh/tree/master/jmh-samples/src/main/java/org/openjdk/jmh/samples)
- [Baeldung Guide on JMH](https://www.baeldung.com/java-microbenchmark-harness)
- [JU2JMH GitHub Repository](https://github.com/alniniclas/junit-to-jmh/)
- [AMBER GitHub Repository](https://github.com/AntonioTrovato/AMBER)

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

Review your lecture slides on **Performance Engineering and Microbenchmarking**, keeping these core dependability principles in mind:

### Key Dependability Concepts in Microbenchmarking

1. **Quantitative Performance Analysis:** Microbenchmarks provide precise, repeatable measurements of code performance.
2. **JVM Warm-up & Stable Measurements:** Proper warm-up phases ensure benchmarks measure steady-state performance.
3. **Isolation of Performance Factors:** Well-designed benchmarks isolate specific operations for accurate measurement.
4. **Performance Regression Detection:** Continuous benchmarking helps detect performance regressions in CI/CD pipelines.

### Performance Measurement Best Practices

| Concept | Description | JMH Feature |
|---------|-------------|-------------|
| **Warm-up** | Initial iterations to let JVM optimize code | `@Warmup` annotation |
| **Measurement** | Actual benchmark iterations | `@Measurement` annotation |
| **Forking** | Separate JVM processes for isolation | `@Fork` annotation |
| **Benchmark Mode** | How performance is measured | Throughput, Avg, SingleShot, etc. |
| **State Management** | Shared vs. per-benchmark state | `@State` annotation |

---

## 3. Stage 2: Introduction to JMH

In this stage, you will learn the basics of JMH and set up your first microbenchmark.

### Step 2.1: Add JMH to Your Maven Project

Add the JMH dependency to your `pom.xml` file:

```xml
<dependencies>
    <dependency>
        <groupId>org.openjdk.jmh</groupId>
        <artifactId>jmh-core</artifactId>
        <version>LATEST</version>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>org.openjdk.jmh</groupId>
        <artifactId>jmh-generator-annprocess</artifactId>
        <version>LATEST</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```

### Step 2.2: Create Your First Microbenchmark

Create a benchmark class following this structure:

```java
package it.unisa.dardin88.sorrentomarina.benchmarks;

import org.openjdk.jmh.annotations.*;
import java.util.concurrent.TimeUnit;

@Warmup(iterations = 3, time = 1, timeUnit = TimeUnit.SECONDS)
@Measurement(iterations = 5, time = 2, timeUnit = TimeUnit.SECONDS)
@Fork(1)
@BenchmarkMode(Mode.Throughput)
@OutputTimeUnit(TimeUnit.MILLISECONDS)
@State(Scope.Benchmark)
public class MyFirstBenchmark {

    @Benchmark
    public void testArrayListAdd() {
        java.util.List<String> list = new java.util.ArrayList<>();
        list.add("test");
    }

    @Benchmark
    public void testLinkedListAdd() {
        java.util.List<String> list = new java.util.LinkedList<>();
        list.add("test");
    }
}
```

### Step 2.3: Run the Benchmark

Execute the following Maven command to run your benchmark:

```bash
# Run benchmarks
mvn clean install
java -jar target/benchmarks.jar

# Run specific benchmark
java -jar target/benchmarks.jar MyFirstBenchmark
```

### Step 2.4: Understand Benchmark Results

The output will show:
- **Throughput**: Operations per time unit (higher is better)
- **Average Time**: Average time per operation (lower is better)
- **Score**: Overall performance score
- **Error**: Statistical confidence interval

Example output:
```
Benchmark                    Mode  Cnt     Score    Error  Units
MyFirstBenchmark.testArrayListAdd   thrpt   10  12345.678 ± 123.456  ops/ms
MyFirstBenchmark.testLinkedListAdd  thrpt   10   8765.432 ±  98.765  ops/ms
```

---

## 4. Stage 3: Converting Test Cases to Benchmarks with JU2JMH

In this stage, you will use JU2JMH to convert existing JUnit test cases into JMH benchmarks.

### Step 3.1: Set Up JU2JMH

JU2JMH is a standalone tool that doesn't require Maven/Gradle dependencies. Follow these steps:

1. Clone the JU2JMH repository:
```bash
git clone https://github.com/alniniclas/junit-to-jmh.git
cd junit-to-jmh
```

2. Build the project:
```bash
./gradlew :converter:run --args="-h"
```

This will display the help message and verify the tool is working.

### Step 3.2: Identify Test Cases for Conversion

Look for test cases that:
- Test performance-critical methods
- Have measurable execution time (>1ms)
- Test pure functions: are determistic and do not have side effects 
- Use standard JUnit 4 or JUnit 5 annotations

### Step 3.3: Convert Test to Benchmark

Use JU2JMH's CLI to convert the test case. You need to provide:

1. Source path (where .java test files are located)
2. Class path (where compiled .class files are located)
3. Output path (where benchmark files will be generated)
4. Test class names to convert

```bash
# Convert entire test class
./gradlew :converter:run --args="/path/to/src/test/java /path/to/build/classes/java/test /path/to/src/jmh/java com.example.MyTest"

# Convert specific methods only
./gradlew :converter:run --args="/path/to/src/test/java /path/to/build/classes/java/test /path/to/src/jmh/java com.example.MyTest#testMethod1,testMethod2"

# Convert multiple test classes
./gradlew :converter:run --args="/path/to/src/test/java /path/to/build/classes/java/test /path/to/src/jmh/java com.example.Test1 com.example.Test2"
```

**Example for your project:**
```bash
./gradlew :converter:run --args="src/test/java build/classes/java/test src/jmh/java it.unisa.dardin88.sorrentomarina.models.MyTest"
```

### Step 3.4: Review Converted Benchmarks

JU2JMH will create benchmark classes with names like `MyTest_JU5Benchmark.java` (for JUnit 5) or `MyTest_JU4Benchmark.java` (for JUnit 4).

The generated benchmarks will include:
- Proper JMH annotations (@Benchmark, @Setup, @State, etc.)
- Inlined test logic from the original test
- Setup methods extracted from @Before/@BeforeEach methods
- Clean separation between test setup and benchmark execution

**Example output structure:**
```
src/jmh/java/
  └── it/unisa/dardin88/sorrentomarina/
      └── benchmarks/
          └── MyTest_JU5Benchmark.java
```

You may need to:
- Adjust warm-up and measurement parameters
- Add @DynamicHalt for AMBER integration
- Modify state management if needed
- Clean up any test-specific assertions

---

## 5. Stage 4: Configuring Warm-up with AMBER

In this stage, you will use AMBER to configure advanced warm-up phases for your benchmarks.

### Step 4.1: Add AMBER to Your Maven Project

AMBER is an AI-enabled extension of JMH. To include AMBER in your Maven project, follow these steps:

1. Download the `jmh-core-1.37-all.jar` file from the [AMBER GitHub repository](https://github.com/AntonioTrovato/AMBER)
2. Place the JAR file in your project's `libs` folder (create it if it doesn't exist)
3. Install the JAR file locally using Maven:

```bash
mvn install:install-file -Dfile=libs/jmh-core-1.37-all.jar \
    -DgroupId=org.openjdk.jmh \
    -DartifactId=jmh-core \
    -Dversion=1.37-extended \
    -Dpackaging=jar
```

4. Modify your `pom.xml` file to include the following dependencies and plugins:

```xml
<dependencies>
    <dependency>
        <groupId>org.openjdk.jmh</groupId>
        <artifactId>jmh-core</artifactId>
        <version>1.37-extended</version>
    </dependency>
    <dependency>
        <groupId>org.openjdk.jmh</groupId>
        <artifactId>jmh-generator-annprocess</artifactId>
        <version>1.37</version>
        <scope>provided</scope>
    </dependency>
</dependencies>

<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.8.1</version>
            <configuration>
                <annotationProcessorPaths>
                    <path>
                        <groupId>org.openjdk.jmh</groupId>
                        <artifactId>jmh-generator-annprocess</artifactId>
                        <version>1.37</version>
                    </path>
                </annotationProcessorPaths>
            </configuration>
        </plugin>
        
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-shade-plugin</artifactId>
            <version>3.4.1</version>
            <executions>
                <execution>
                    <phase>package</phase>
                    <goals>
                        <goal>shade</goal>
                    </goals>
                    <configuration>
                        <createDependencyReducedPom>true</createDependencyReducedPom>
                        <transformers>
                            <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                                <mainClass>org.openjdk.jmh.Main</mainClass>
                            </transformer>
                        </transformers>
                    </configuration>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```

### Step 4.2: Configure Warm-up Phases

AMBER allows you to configure complex warm-up scenarios using the `@DynamicHalt` annotation. This annotation enables AI-driven dynamic halt for your benchmarks.

```java
package it.unisa.dardin88.sorrentomarina.benchmarks;

import org.openjdk.jmh.annotations.*;
import java.util.concurrent.TimeUnit;

@Warmup(iterations = 5, time = 2, timeUnit = TimeUnit.SECONDS)
@Measurement(iterations = 10, time = 3, timeUnit = TimeUnit.SECONDS)
@Fork(3)
@BenchmarkMode(Mode.AverageTime)
@OutputTimeUnit(TimeUnit.NANOSECONDS)
@State(Scope.Thread)
@DynamicHalt(model = "oscnn")
public class AMBERWarmupBenchmark {

    private java.util.List<String> list;

    @Setup
    public void setup() {
        list = new java.util.ArrayList<>();
    }

    @Benchmark
    public void testWithAMBERWarmup() {
        for (int i = 0; i < 50; i++) {
            list.add("element" + i);
        }
    }
}
```

### Step 4.3: AMBER Configuration Options

AMBER provides several configuration options through the `@DynamicHalt` annotation:

| Option | Description | Values |
|--------|-------------|--------|
| `model` | AI model for dynamic halt | `oscnn`, `fcn`, `rocket` |
| `host` | Host for the JPT service (default: localhost) | String |
| `port` | Port for the JPT service (default: 50001) | Integer |

The `@DynamicHalt` annotation can be used at both method-level and class-level.

AMBER leverages three AI models for dynamic halt:
- `oscnn`: Online Statistical Control Chart Neural Network
- `fcn`: Fully Connected Network
- `rocket`: Rocket model for time series classification

### Step 4.4: JPT Service Requirement

AMBER requires the JPT (Java Performance TSC) service to be running. Follow these steps to start it:

1. Clone the AMBER repository:
```bash
git clone https://github.com/AntonioTrovato/AMBER.git
cd AMBER/jpt_service
```

2. Build and run the Docker service:
```bash
docker-compose up -d
```

3. Verify the service is running:
```bash
docker ps
```

The service should be available at `localhost:50001` by default.

You can run benchmarks with AMBER in from command line:
```bash
java -jar target/your-project-1.0-SNAPSHOT.jar -hmodel "oscnn"
```

with custom host and port:
```bash
java -jar target/your-project-1.0-SNAPSHOT.jar -hmodel "oscnn" -host "localhost" -hport "50001"
```

For more options, run:
```bash
java -jar target/your-project-1.0-SNAPSHOT.jar -h
```

Furthermore, modern IDEs support running JMH benchmarks directly.

---

## 6. Stage 5: Course Project Application (Student Task)

Now it's time to apply what you've learned to your course project!

### Step 5.1: Add JMH to Your Project

1. Add JMH dependencies to your `pom.xml`
2. Create a `benchmarks` package in your project
3. Write your first microbenchmark for a performance-critical method

### Step 5.2: Convert Test Cases to Benchmarks with JU2JMH

1. Identify performance-critical test cases in your test suite
2. Use JU2JMH to convert these tests to benchmarks
3. Review and refine the generated benchmarks

### Step 5.3: Configure Warm-up with AMBER

1. Add AMBER to your project dependencies
2. Configure warm-up phases for your benchmarks
3. Run benchmarks with different warm-up strategies

### Step 5.4: Run Benchmark Analysis

1. **Identify Performance Bottlenecks**: Find slow methods in your code
2. **Compare Implementations**: Benchmark different approaches to the same problem
3. **Track Performance Changes**: Run benchmarks before and after code changes

### Step 5.5: Integrate with CI/CD

**Important Note:** Microbenchmarks are computationally expensive and should typically be run locally on your development machine rather than in CI/CD pipelines. This ensures consistent hardware configurations and avoids unnecessary CI/CD costs.

However, if you want to run benchmarks in CI/CD for specific scenarios (e.g., performance regression detection), create a separate workflow file `.github/workflows/benchmarks.yml`:

```yaml
name: Microbenchmarks

on:
  schedule:
    # Run weekly to detect performance regressions
    - cron: '0 0 * * 0'
  workflow_dispatch: # Allow manual triggering

jobs:
  benchmark:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Set up JDK 21
        uses: actions/setup-java@v3
        with:
          java-version: '21'
          distribution: 'temurin'
      
      - name: Build with Maven
        run: mvn clean install -DskipTests
      
      - name: Run benchmarks
        run: java -jar target/your-project-1.0-SNAPSHOT.jar -hmodel "oscnn"
      
      - name: Upload benchmark results
        if: always()
        uses: actions/upload-artifact@v4
        with:
          name: benchmark-results
          path: target/jmh-results/
```

**Recommendation:** Run benchmarks locally using:
```bash
mvn clean install
java -jar target/your-project-1.0-SNAPSHOT.jar -hmodel "oscnn"
```

This provides more consistent results and better performance measurements on your local machine.

### Step 5.6: Document Your Benchmarking Strategy

Create a report named `benchmarking.md` (maximum **5,000 characters**) in your project repository and link to it from your root `README.md`. Your report must include:
- Which methods were benchmarked
- Why these methods were chosen
- The warm-up and measurement configuration
- Performance results and analysis
- Any performance improvements made

---

## 7. Submission Checklist

Before submitting your lab, ensure you have completed the following:

- [ ] Added JMH dependencies to your `pom.xml`
- [ ] Created at least three microbenchmarks for performance-critical methods
- [ ] Configured warm-up, measurement, and fork parameters correctly
- [ ] Used JU2JMH to convert at least two test cases to benchmarks
- [ ] Configured warm-up phases using AMBER
- [ ] Ran benchmarks locally and analyzed results
- [ ] Identified performance bottlenecks in your project
- [ ] Documented your benchmarking strategy in `benchmarking.md`