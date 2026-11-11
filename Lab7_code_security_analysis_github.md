# Lab Guide: Code Security Analysis Integrated in GitHub

Welcome to the hands-on lab session on **Code Security Analysis Integrated in GitHub**!

In this session, you will learn how to leverage GitHub's built-in security features to analyze your code for vulnerabilities, enforce security policies, and maintain a secure development lifecycle. You will explore GitHub's Code Security and Analysis tools, including Code Scanning, Secret Scanning, Dependabot, and GitHub Advanced Security.

You can leverage these resources:
- [GitHub Code Security and Analysis Documentation](https://docs.github.com/en/code-security)
- [GitHub Advanced Security Documentation](https://docs.github.com/en/get-started/learning-about-github/about-github-advanced-security)
- [GitHub Skills: Code Security and Analysis](https://learn.github.com/skills)

Before starting this lab, ensure you have completed the following:
- **Lab1_CICD.md**: Continuous Integration & Continuous Delivery (CI/CD) for Software Dependability

---

## 1. Prerequisites & Required Tools
Before starting, ensure you have configured the following environments and resources:

* **GitHub Account:** Register for student benefits via [GitHub Education](https://github.com/education).
* **GitHub Skills:** Access the hands-on interactive courses at [GitHub Skills](https://learn.github.com/skills).
* **Git**
* **Course Reference Repository:** Clone or update your local copy of `SorrentoMarina`:
  ```bash
  git clone https://github.com/dardin88/SorrentoMarina.git
  cd SorrentoMarina
  ```

---

## 2. Stage 1: Theory & Lecture Alignment

Review your lecture slides on **Code Security and Analysis**, keeping these core security principles in mind:

### Key Security Concepts

1. **Shift Left Security:** Integrate security analysis early in the development lifecycle to catch vulnerabilities before they reach production.

2. **Automated Security Scanning:** Use automated tools to detect common vulnerabilities and coding errors in your codebase.

3. **Secret Detection:** Identify and prevent hardcoded secrets (passwords, API keys, tokens) from being committed to your repository.

4. **Dependency Management:** Monitor your project's dependencies for known vulnerabilities and ensure they are updated regularly.

5. **Code Quality Enforcement:** Enforce security policies and coding standards through automated checks and quality gates.

---

## 3. Stage 2: GitHub Skills & Security Features Exploration

In this stage, you will complete interactive GitHub training modules and explore GitHub's security features.

### Step 2.1: Complete GitHub Skills Modules

1. Navigate to [GitHub Skills](https://learn.github.com/skills).

2. Complete the following **Code Security and Analysis** modules:
   - [Secure your repository supply chain](https://github.com/skills/secure-repository-supply-chain)
   - [Introduction to CodeQL](https://github.com/skills/introduction-to-codeql)
   - [Introduction to Security Scanning](https://github.com/skills/introduction-to-secret-scanning)
   - [Ship with Quality](https://github.com/skills/ship-with-quality)

3. Observe how these features integrate seamlessly into the GitHub development workflow.

### Step 2.2: Enabling GitHub Advanced Security

For this lab, we will focus on the free features available for public repositories:

1. **Enable Code Scanning:**
   - Go to your repository settings
   - Navigate to **Code and automation** > **Code security and analysis**
   - Enable **Code scanning**

2. **Enable Secret Scanning:**
   - In the same settings section, enable **Secret scanning**

3. **Enable Dependabot:**
   - Go to **Code and automation** > **Dependabot**
   - Enable **Dependabot alerts** and **Dependabot security updates**

---

## 4. Stage 3: Code Scanning with GitHub Actions

In this stage, you will set up code scanning using GitHub Actions to detect vulnerabilities in your code.

### Step 3.1: Understand Code Scanning

Code scanning uses static analysis tools to detect vulnerabilities in your code. GitHub supports several analysis tools, including:

- **CodeQL:** GitHub's advanced static analysis tool
- **Third-party tools:** Such as SonarCloud, Semgrep, and others

### Step 3.2: Set Up Code Scanning with CodeQL

Create a workflow file to enable CodeQL analysis:

```yaml
name: CodeQL Analysis

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  analyze:
    name: Analyze
    runs-on: ubuntu-latest
    permissions:
      actions: read
      contents: read
      security-events: write

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Initialize CodeQL
        uses: github/codeql-action/init@v3
        with:
          languages: java
          queries: security-and-quality

      - name: Build project
        run: mvn clean compile

      - name: Perform CodeQL Analysis
        uses: github/codeql-action/analyze@v3
        with:
          category: "/language:java"
```

### Step 3.3: Configure CodeQL Analysis

The CodeQL action supports several configuration options:

| Parameter | Description | Default |
|-----------|-------------|---------|
| `languages` | Languages to analyze | All detected languages |
| `queries` | CodeQL query suites to run | `security-extended` |
| `packaged-queries` | Additional query suites | None |
| `config-file` | Custom CodeQL configuration | None |
| `threads` | Number of threads to use | Auto-detected |

### Step 3.4: Run Code Scanning

1. Commit the workflow file to your repository
2. Push the changes to GitHub
3. Navigate to the **Security** tab in your repository
4. Click on **Code scanning** to view the results

### Step 3.5: Review Code Scanning Alerts

Code scanning alerts will appear in the **Security** tab. Each alert includes:

- **Severity:** High, Medium, Low, or Note
- **Description:** Explanation of the vulnerability
- **Location:** File and line number where the vulnerability was detected
- **Path:** Code path that leads to the vulnerability
- **Remediation:** Suggested fix for the vulnerability

### Step 3.6: Fixing Code Scanning Alerts

1. **Review the alert details** to understand the vulnerability
2. **Apply the suggested remediation** or implement your own fix
3. **Commit the changes** to your repository
4. **Re-run the analysis** to verify the fix

---

## 5. Stage 4: Secret Scanning

In this stage, you will set up secret scanning to detect hardcoded secrets in your code.

### Step 4.1: Understand Secret Scanning

Secret scanning detects hardcoded secrets (API keys, tokens, passwords) in your code and repository history. GitHub supports:

- **Push protection:** Blocks pushes containing secrets
- **Secret scanning alerts:** Notifies you when secrets are detected
- **Partner integrations:** Automatically revokes exposed secrets with partner services

### Step 4.2: Enable Secret Scanning

Secret scanning is enabled by default for public repositories. For private repositories, you need to:

1. Go to **Settings** > **Code security and analysis**
2. Enable **Secret scanning**
3. (Optional) Enable **Push protection** to block pushes containing secrets

### Step 4.3: Configure Custom Patterns

You can configure custom patterns to detect specific types of secrets:

1. Go to **Settings** > **Code security and analysis**
2. Click on **Secret scanning**
3. Click on **Add custom pattern**
4. Enter a name and pattern for your custom secret type

### Step 4.4: Review Secret Scanning Alerts

Secret scanning alerts appear in the **Security** tab under **Secret scanning**. Each alert includes:

- **Secret type:** Type of secret detected (e.g., AWS access key, GitHub token)
- **Location:** File and line number where the secret was detected
- **Partner:** Partner service that can revoke the secret (if applicable)
- **Status:** Open, resolved, or revoked

### Step 4.5: Remediating Secret Scanning Alerts

1. **Rotate the secret:** Generate a new secret and update all services using it
2. **Remove the secret from code:** Delete the hardcoded secret from your repository
3. **Mark as false positive:** If the alert is a false positive, mark it as such
4. **Revoke the secret:** If a partner integration is available, revoke the secret through the partner service

---

## 6. Stage 5: Dependabot for Dependency Management

In this stage, you will set up Dependabot to monitor your project's dependencies for vulnerabilities and updates.

### Step 5.1: Understand Dependabot

Dependabot automatically:

- Checks your dependencies for known vulnerabilities
- Creates pull requests to update outdated dependencies
- Provides security advisories for vulnerable dependencies

### Step 5.2: Enable Dependabot

Dependabot is enabled by default for public repositories. For private repositories, you need to:

1. Go to **Code and automation** > **Dependabot**
2. Enable **Dependabot alerts** and **Dependabot security updates**

### Step 5.3: Configure Dependabot

Create a `dependabot.yml` file in your repository's `.github` directory:

```yaml
version: 2

updates:
  - package-ecosystem: "maven"
    directory: "/"
    schedule:
      interval: "daily"
    open-pull-requests-limit: 10
    reviewers:
      - "your-username"
    assignees:
      - "your-username"
    commit-message:
      prefix: "deps:"
      include: "scope"
```

### Step 5.4: Review Dependabot Alerts

Dependabot alerts appear in the **Security** tab under **Dependabot**. Each alert includes:

- **Dependency:** Name and version of the vulnerable dependency
- **Vulnerability:** CVE ID and severity
- **Affected versions:** Range of affected versions
- **Patched versions:** Available fixed versions
- **Recommendation:** Suggested action to remediate the vulnerability

### Step 5.5: Managing Dependabot Pull Requests

Dependabot creates pull requests to update dependencies:

1. **Review the pull request:** Check the changes and ensure they don't break your project
2. **Test the changes:** Run your test suite to verify the update doesn't introduce regressions
3. **Merge the pull request:** Once verified, merge the pull request to update the dependency

### Step 5.6: Suppressing Dependabot Alerts

If you need to suppress a Dependabot alert:

1. Go to the alert in the **Security** tab
2. Click on **Dismiss**
3. Provide a reason for dismissing the alert
4. (Optional) Set an expiration date for the dismissal

---

## 7. Stage 6: Course Project Application (Student Assignment)

Now it's time to apply what you've learned to your course project!

### Step 6.1: Set Up Code Scanning

1. Create a `.github/workflows/codeql-analysis.yml` file in your project repository
2. Configure CodeQL to analyze your Java code
3. Set up appropriate quality gates for code scanning alerts

### Step 6.2: Enable Secret Scanning

1. Enable secret scanning in your repository settings
2. (Optional) Configure custom patterns for secrets specific to your project
3. Set up push protection to block pushes containing secrets

### Step 6.3: Configure Dependabot

1. Create a `.github/dependabot.yml` file to configure Dependabot
2. Set up appropriate schedules for dependency updates
3. Configure reviewers and assignees for Dependabot pull requests

### Step 6.4: Integrate Security Checks into CI/CD

Update your CI/CD pipeline to include security checks:

```yaml
name: CI/CD Pipeline with Security Checks

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest
    name: Build and Test
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up JDK 21
        uses: actions/setup-java@v4
        with:
          java-version: '21'
          distribution: 'temurin'

      - name: Run tests
        run: mvn clean test

  security:
    needs: test
    runs-on: ubuntu-latest
    name: Security Checks
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Initialize CodeQL
        uses: github/codeql-action/init@v3
        with:
          languages: java
          queries: security-and-quality

      - name: Build project
        run: mvn clean compile

      - name: Perform CodeQL Analysis
        uses: github/codeql-action/analyze@v3
        with:
          category: "/language:java"

      - name: Check for Dependabot alerts
        run: |
          ALERTS=$(gh api repos/${{ github.repository }}/dependabot/alerts --jq 'length')
          if [ "$ALERTS" -gt 0 ]; then
            echo "Dependabot alerts found!"
            exit 1
          fi
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

### Step 6.5: Document Your Security Strategy

Create a report named `security.md` (maximum **5,000 characters**) in your project repository and link to it from your root `README.md`. Your report must include:

1. **Code Scanning Configuration:** Explain the CodeQL workflow configuration, including languages analyzed, query suites used, and any custom configurations.

2. **Secret Scanning Setup:** Describe how secret scanning is configured, including any custom patterns and push protection settings.

3. **Dependabot Configuration:** Explain the Dependabot configuration, including update schedules, package ecosystems, and any custom settings.

4. **Security Integration:** Describe how security checks are integrated into your CI/CD pipeline and any quality gates or policies enforced.

5. **Security Policy:** Define your project's security policy, including:
   - How vulnerabilities are prioritized and remediated
   - The process for reviewing and merging Dependabot pull requests
   - How secrets are managed and rotated
   - Any additional security measures specific to your project

6. **Security Metrics:** Include metrics such as:
   - Number of code scanning alerts (by severity)
   - Number of secret scanning alerts
   - Number of Dependabot alerts (by severity)
   - Time to remediate vulnerabilities

---

## 8. Submission Checklist

Before submitting your lab, ensure you have completed the following:

- [ ] Completed GitHub Skills Code Security and Analysis modules
- [ ] Enabled Code Scanning in your project repository
- [ ] Created `.github/workflows/codeql-analysis.yml` workflow file
- [ ] Enabled Secret Scanning in your repository settings
- [ ] Configured Dependabot with `.github/dependabot.yml`
- [ ] Integrated security checks into your CI/CD pipeline
- [ ] Reviewed and addressed any code scanning alerts
- [ ] Reviewed and addressed any secret scanning alerts
- [ ] Reviewed and addressed any Dependabot alerts
- [ ] Documented your security strategy in `security.md` (max 5,000 characters)
- [ ] All security checks pass in your CI/CD pipeline
