# Lab Guide: Continuous Integration & Continuous Delivery (CI/CD)

Welcome to the hands-on lab session on **Continuous Integration and Continuous Delivery (CI/CD)**!

In this session, you will learn how to automate software building, testing, and container verification using **GitHub Actions**. Through the lens of **Software Dependability**, you will explore how automated pipelines guarantee build reproducibility, prevent regression bugs, enforce security policy compliance, and secure sensitive credentials during deployment.

---

## 1. Prerequisites & Required Tools

Before starting, ensure you have configured the following environments and resources:

* **GitHub Account:** Register for student benefits via [GitHub Education](https://github.com/education).
* **GitHub Skills:** Access the hands-on interactive courses at [GitHub Skills](https://learn.github.com/skills).
* **Git**.
* **Course Reference Repository:** Clone or update your local copy of `SorrentoMarina`:
  ```bash
  git clone https://github.com/dardin88/SorrentoMarina.git
  cd SorrentoMarina
  ```

---

## 2. Stage 1: Theory & Lecture Alignment

Review your lecture slides on Continuous Integration and DevOps toolchains, keeping these core dependability principles in mind:

### Key Dependability Concepts in CI/CD

1. **Automated Verification & Fast Feedback Loops:** CI pipelines automatically execute static analysis, unit tests, and integration tests on every commit, reducing the mean time to detect bugs before code hits production.
2. **Ephemeral & Isolated Execution Environments:** Pipeline jobs execute inside fresh, standardized virtual machines (e.g., GitHub-hosted runners like `ubuntu-latest`). This eliminates the "works on my machine" anti-pattern.
3. **Immutability & Continuous Delivery:** Every build artifact (e.g., a tagged Docker image) is built once, verified automatically, and moved through environment gates without modification.
4. **Pipeline Security & Secrets Management:** 
   * **Least Privilege:** CI/CD runners should operate with minimal required privileges (`GITHUB_TOKEN` permissions).
   * **Secrets Isolation:** Plaintext passwords, private keys, and API tokens must **never** be hardcoded into workflow files or source code. They must be stored in encrypted GitHub Repository Secrets.

---

## 3. Stage 2: GitHub Skills & Reference Pipeline Inspection

In this stage, you will complete interactive GitHub training modules and inspect the reference CI/CD configuration in `SorrentoMarina`.

### Step 2.1: Complete GitHub Skills Modules
1. Navigate to [GitHub Skills](https://learn.github.com/skills).
2. Complete the **GitHub Actions: Hello World** and **Build and Test Code** starter skills.
3. Observe how workflows trigger automatically on `push` or `pull_request` events.

### Step 2.2: Audit `SorrentoMarina` GitHub Workflow Architecture
Inspect the `.github/workflows/ci.yml` pipeline file in the reference repository to understand how automated integration and   testing are structured:

```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

permissions:
  contents: read
  pull-requests: write
  checks: write
  pages: write
  id-token: write

jobs:
  test:
    runs-on: ubuntu-latest
    name: Build and Test with Java 21
    
    env:
      DB_TYPE: sqlite
      SQLITE_DB_PATH: /tmp/sorrentodb.sqlite

    steps:
    - name: Checkout code
      uses: actions/checkout@v5

    - name: Set up JDK 21
      uses: actions/setup-java@v4
      with:
        java-version: '21'
        distribution: 'temurin'

    - name: Cache Maven dependencies
      uses: actions/cache@v4
      with:
        path: ~/.m2
        key: ${{ runner.os }}-m2-${{ hashFiles('**/pom.xml') }}
        restore-keys: ${{ runner.os }}-m2

    - name: Setup SQLite database
      run: |
        echo "Setting up SQLite database..."
        sudo apt-get update
        sudo apt-get install -y sqlite3
        
        echo "Creating SQLite database with schema..."
        sqlite3 ${{ env.SQLITE_DB_PATH }} < schema.sql
        
        echo "Verifying database setup..."
        sqlite3 ${{ env.SQLITE_DB_PATH }} "SELECT COUNT(*) FROM LIDO;"
        echo "SQLite database initialized successfully"

    - name: Run tests
      run: mvn clean test
      
    ...

    - name: Build application
      run: mvn package -DskipTests

    - name: Upload WAR artifact
      uses: actions/upload-artifact@v4
      if: success()
      with:
        name: sorrentomarina-${{ github.sha }}
        path: target/SorrentoMarina-1.0.war
        retention-days: 30
```

#### Pipeline Best Practices Observed in `SorrentoMarina`:
* **Pinned Action Versions:** Uses explicit major release tags (e.g., `actions/checkout@v4`) for security and stability.
* **Build Caching:** Uses `cache: maven` to accelerate build times and avoid redundant dependency downloads.
* **Container Verification:** Builds and tests the Docker container in CI prior to merge approval.
* **Secrets Protection:** Injects sensitive database credentials dynamically using `${{ secrets.DB_PASSWORD }}` instead of hardcoded strings.

---

## 4. Stage 3: Course Project Application (Student Assignment)

Now you will build and attach a robust CI/CD pipeline to your **own web application** repository for your Software Dependability course project!

### Student Requirements

#### Task 3.1: Custom GitHub Workflow Creation (`.github/workflows/ci.yml`)
Create a custom workflow file in your project repository under `.github/workflows/ci.yml`. Your pipeline must perform the following stages automatically:

1. **Trigger Rules:** Configure the workflow to run on `push` and `pull_request` to `main`/`master`.
2. **Automated Testing:** Run your application's test suite (e.g., `npm test`, `pytest`, `mvn test`).

#### Task 3.2: CI/CD Documentation & Self-Audit (`CICD.md`)
Create a report named `CICD.md` (maximum **5,000 characters**) in your project repository and link to it from your root `README.md`. Your report must include:

1. **Pipeline Architecture Overview:** Explain the stages of your `.github/workflows/ci.yml` file and describe how your pipeline guarantees build reproducibility and code quality.

---

## 4. Submission Checklist

- [ ] Registered/verified account via GitHub Education.
- [ ] Completed basic GitHub Skills CI/CD courses.
- [ ] Inspected and tested `SorrentoMarina` reference workflow.
- [ ] Created `.github/workflows/ci.yml` in your team's course project repository.
- [ ] Verified that GitHub Actions successfully builds and tests your project on push.
- [ ] Written `CICD.md` (max 5,000 characters) and linked it in your repository `README.md`.