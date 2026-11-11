# Lab Guide: Containerization & Orchestration for Software Dependability

Welcome to the hands-on lab session on **Containerization**! 

In this session, you will learn how container technologies like **Docker**, **Docker Compose**, and **Podman** contribute to **Software Dependability**—specifically focusing on environment reproducibility, system isolation, fault recovery, and security models.

Before starting this lab, ensure you have completed the following:
- **Lab1_CICD.md**: Continuous Integration & Continuous Delivery (CI/CD) for Software Dependability

---

## 1. Prerequisites & Required Tools

Before starting, ensure you have installed and verified the following tools on your system:

* **Docker Desktop** or **Docker Engine** v24.0+ with the `docker compose` plugin.
* **Git**
* Access to a terminal (Bash, Zsh, or PowerShell).
* Clone the course reference repository:
  ```bash
  git clone https://github.com/dardin88/SorrentoMarina.git
  cd SorrentoMarina
  ```

---

## 2. Stage 1: Theory & Lecture Alignment

Review the provided lecture slides (`Lab1_Containerization.pdf`) and keep the following key architectural concepts in mind:

### Key Dependability Concepts
1. **DevOps & Continuous Architecting:** Transitioning software from monolithic architectures to isolated microservices improves maintainability and deployment independence.
2. **Virtual Machines vs. Containers:** VMs isolate hardware using a hypervisor and full Guest OS, whereas containers isolate application processes on a shared host OS kernel, offering near-zero execution overhead and fast boot times.
3. **Docker Layered Architecture:** Container images consist of stacked, read-only layers with a top writable container layer added at runtime.
4. **Daemon Security vs. Rootless Execution (Docker vs. Podman):**
   * **Docker:** Relies on a centralized, root-privileged daemon (`dockerd`). If the daemon crashes or is compromised, all containers and host privileges are affected.
   * **Podman (Theoretical Audit):** Uses a daemonless architecture (`libpod`) where containers run as direct child processes (`conmon`) under **rootless** user namespaces. This eliminates the central single point of failure (SPOF) and prevents container breakout vulnerabilities from gaining root host privileges.

---

## 3. Stage 2: Single-Container Hands-on & Reference Inspection

In this stage, you will complete the official tutorial and analyze the pre-packaged `SorrentoMarina` repository as a benchmark for container hygiene.

### Step 2.1: Complete the Official Docker Get-Started Tutorial
1. Navigate to the [Official Docker Run-an-App Tutorial](https://docs.docker.com/get-started/tutorials/run-an-app/).
2. Follow the steps to build, run, and modify the sample TODO web application.
3. Observe how changes in source code require rebuilding image layers.

### Step 2.2: Audit `SorrentoMarina` Dockerfile Best Practices
Open the `Dockerfile` inside the cloned `SorrentoMarina` folder and inspect its contents against key Docker hygiene guidelines:

```dockerfile
# 1. Explicit base image version (Avoids unpredictable build behavior from :latest)
FROM openjdk:17-slim

# 2. Define explicit environment parameters
ENV PORT=8080
WORKDIR /app

# 3. Use COPY instead of ADD to prevent unintended archive extraction
COPY target/SorrentoMarina-*.jar app.jar

# 4. Least-Privilege Execution: Run as non-root user for container security
RUN useradd -m appuser && chown -R appuser:appuser /app
USER appuser

EXPOSE 8080

# 5. Exec-form entrypoint definition
CMD ["java", "-jar", "app.jar"]
```

#### Anti-Patterns Avoided in `SorrentoMarina`:
* ❌ **Hardcoding secrets in Dockerfiles or compose files:** API keys, passwords, and database credentials MUST NEVER be committed to source control.
* ❌ **Using `:latest` tags:** Can lead to broken builds when base images update unexpectedly.
* ❌ **Using `ADD` for local files:** `COPY` is [safer and more explicit](https://www.docker.com/blog/docker-best-practices-understanding-the-differences-between-add-and-copy-instructions-in-dockerfiles/)
* ❌ **Running as `root`:** Violates security isolation principles.
* ❌ **Executing `apt-get upgrade` in separate lines:** Bloats layer caches and destroys build determinism.

For a comprehensive overview of Docker antipatterns and best practices, see:
- [Docker Building Best Practices](https://docs.docker.com/build/building/best-practices/)
- [Docker: Build Secrets](https://docs.docker.com/build/building/secrets/)

### Step 2.3: Build & Run `SorrentoMarina` Single Container
Run the following commands in your terminal to build and run the reference application:

```bash
# 1. Build the docker image locally
docker build -t sorrento-marina:1.0 .

# 2. Verify image creation and size
docker images

# 3. Run the container in background (detached) mapping host port 8080
docker run -d -p 8080:8080 --name sorrento-app sorrento-marina:1.0

# 4. Verify running container status
docker ps

# 5. Inspect container metadata and network configuration
docker inspect sorrento-app

# 6. Stop and remove the standalone container
docker stop sorrento-app
docker rm sorrento-app
```

---

## 4. Stage 3: Playing with Multi-Container Docker Compose

Modern systems separate business logic from data storage. In this stage, you will use Docker Compose to manage a multi-container stack (Application + Database).

### Step 3.1: Inspect `docker-compose.yml` & Security Best Practices
Open `docker-compose.yml` in the `SorrentoMarina` repository and observe how services, dependencies, health checks, and volumes are declared.

### Step 3.2: Multi-Container Lifecycle Operations
Execute the Compose stack commands:

```bash
# 1. Start all defined services in background mode
docker compose up -d

# 2. Stream logs from the web service
docker compose logs -f web
```

### Step 3.3: Dependability & Fault Resilience Experiment
Perform this fault injection test to observe data safety and automatic recovery:

1. **Simulate a Database Crash:**
   ```bash
   docker compose stop db
   ```
2. **Observe Web Service Behavior:** Check the web application in your browser or view logs (`docker compose logs web`). Note how the app handles database loss.
3. **Recover Database Service:**
   ```bash
   docker compose start db
   ```
   Notice how the application automatically reconnects once the database health check succeeds.
4. **Test Data Persistence:**
   ```bash
   # Destroy containers and networks
   docker compose down

   # Re-create the stack from volume
   docker compose up -d
   ```
   Verify that your data persists because it is stored in the named volume `db_data`.

---

## 5. Stage 4: Course Project Application (Student Assignment)

Now it is time to apply these containerization practices to your **own web application** developed for your Software Dependability course project!

### Student Requirements

#### Task 4.1: Custom `Dockerfile` Creation
Create a `Dockerfile` in your course project repository. Ensure that the Docker best practices are adopted.

#### Task 4.2: Multi-Container Orchestration (`docker-compose.yml`) with Secrets Management ⚠️
Create a `docker-compose.yml` file that orchestrates:
* Your custom application container.
* At least **one supporting service** (e.g., PostgreSQL, MySQL, Redis, MongoDB).
* A named volume attached to your database service to ensure data durability.
* Health checks (`healthcheck`) and failure recovery policies (`restart: on-failure`).

#### Task 4.3: Containerization and Self-Audit Reports (Submission Deliverable)
1. **Dockerfile and Docker-Compose Code & Hygiene Explanation:** Include your `Dockerfile` and `docker-compose.yml` and list the anti-patterns you avoided.
2. Prepare a short report (5,000 characters max) in Markdown and name it `Docker.md`. The report should be linked from the `README.md` file that should be place in the root of your report.


---

## 6. Stage 5: CI/CD Integration for Containerization

In this stage, you will extend your CI/CD pipeline to include containerization and publishing to DockerHub. This phase builds upon the CI/CD pipeline you created in **Lab1_CICD.md**.

### Step 5.1: Create a Docker Build Workflow

Create a new workflow file in your project repository at `.github/workflows/docker-build.yml`. This workflow should:

1. **Trigger on successful CI/CD pipeline completion**: The workflow should run only if the CI/CD pipeline (from Lab1) completes successfully.
2. **Build the Docker image**: Use the `docker/build-push-action` to build your Docker image.
3. **Log in to DockerHub**: Use the `docker/login-action` to authenticate with DockerHub using secrets stored in your GitHub repository.
4. **Push the Docker image**: Push the built image to your DockerHub repository.

Here is an example workflow based on the reference repository:

```yaml
name: Docker Build

on:
  workflow_run:
    workflows: ["CI/CD Pipeline"]
    types:
      - completed
    branches: [ main ]
  workflow_dispatch:

jobs:
  build-docker:
    runs-on: ubuntu-latest
    name: Build Docker Image
    if: github.event.workflow_run.conclusion == 'success'

    steps:
    - uses: actions/checkout@v5

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

    - name: Build application
      run: mvn clean package -DskipTests

    - name: Set up Docker Buildx
      uses: docker/setup-buildx-action@v3

    - name: Log in to Docker Hub
      uses: docker/login-action@v3
      with:
        username: ${{ secrets.DOCKER_USERNAME }}
        password: ${{ secrets.DOCKER_PASSWORD }}

    - name: Extract metadata
      id: meta
      uses: docker/metadata-action@v5
      with:
        images: ${{ secrets.DOCKER_USERNAME }}/your-repo-name
        tags: |
          type=raw,value=latest
          type=raw,value=version-tag

    - name: Build and push Docker image
      uses: docker/build-push-action@v6
      with:
        context: .
        platforms: linux/amd64,linux/arm64
        push: true
        tags: ${{ steps.meta.outputs.tags }}
        labels: ${{ steps.meta.outputs.labels }}
        cache-from: type=gha
        cache-to: type=gha,mode=max
```

### Step 5.2: Audit the Reference Workflow

Audit the reference workflow at [https://github.com/dardin88/SorrentoMarina/blob/main/.github/workflows/docker-build.yml](https://github.com/dardin88/SorrentoMarina/blob/main/.github/workflows/docker-build.yml) and answer the following questions:

1. **Trigger Mechanism**: How does the workflow ensure it only runs after the CI/CD pipeline succeeds?
2. **Secrets Management**: How are DockerHub credentials securely managed in the workflow?
3. **Multi-Platform Support**: What platforms does the workflow build for, and why is this important?
4. **Caching**: How does the workflow optimize build times using caching?
5. **Metadata Handling**: How are image tags dynamically generated in the workflow?

### Step 5.3: Configure DockerHub Secrets

1. **Create a DockerHub Account**: If you don’t have one, sign up at [DockerHub](https://hub.docker.com/).
2. **Create a Repository**: Create a new repository on DockerHub to store your images.
3. **Add Secrets to GitHub**:
   - Go to your GitHub repository settings.
   - Navigate to **Secrets and variables** > **Actions**.
   - Add the following secrets:
     - `DOCKER_USERNAME`: Your DockerHub username.
     - `DOCKER_PASSWORD`: A personal access token (not your password) with read/write permissions.

### Step 5.4: Test the Workflow

1. **Push Changes**: Commit and push your changes to the `main` branch.
2. **Monitor Workflow**: Go to the **Actions** tab in your GitHub repository to monitor the workflow execution.
3. **Verify Image**: Once the workflow completes, verify that your Docker image is published on DockerHub.

---

## 7. Submission Checklist

- [ ] Completed Docker Get-Started Tutorial.
- [ ] Audited `SorrentoMarina` `Dockerfile` and `docker-compose.yml`.
- [ ] Tested fault tolerance & volume persistence on `SorrentoMarina`.
- [ ] Created `Dockerfile` and `docker-compose.yml` for your own course project.
- [ ] Created `.github/workflows/docker-build.yml` for CI/CD containerization.
- [ ] Audited the reference Docker build workflow and included findings in `Docker.md`.
- [ ] Configured DockerHub secrets and verified image publishing.
- [ ] Included the lab reports concerning containerization and self-audit in the GitHub repo of the project.