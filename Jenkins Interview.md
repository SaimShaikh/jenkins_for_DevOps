
## Q1. What is Jenkins?

Jenkins is an open-source CI/CD automation tool used to build, test, and deploy applications automatically.

It helps automate repetitive tasks, detect bugs early, speed up software delivery, and integrate well with DevOps tools like Git, Docker, and Kubernetes.

---

## Q2. Jenkins vs GitHub Actions

Jenkins is a self-hosted CI/CD tool that provides high flexibility and customization, while GitHub Actions is a GitHub-native CI/CD service that is easy to set up and tightly integrated with GitHub repositories.

Jenkins is commonly used in enterprise environments, whereas GitHub Actions is preferred for simpler, GitHub-centric workflows.

---

## Q3. Continuous Integration, Continuous Delivery, and Continuous Deployment

**Continuous Integration (CI):**
Automatically builds and tests code whenever changes are merged.

**Continuous Delivery (CD):**
Ensures that the application is always ready for deployment, but the release is done manually.

**Continuous Deployment:**
Automatically deploys every successful change to production without manual intervention.

---

## Q4. Jenkins Backup Strategy

Jenkins backup strategy mainly focuses on backing up the Jenkins home directory, which contains all jobs, pipelines, plugins, and configurations.

Backups are automated using cron jobs or plugins and stored on external storage.

In production, Jenkins is often deployed with persistent volumes or Kubernetes to ensure data safety and quick recovery in case of failures.

---

## Q5. If Jenkins Crashes

If Jenkins crashes, CI/CD pipelines stop temporarily, but running applications are not affected.

To handle this:

* Restart the Jenkins service
* Restore from backups if required
* Use persistent storage for Jenkins data

In production, Jenkins is often deployed with high availability using Kubernetes and Jenkins agents to avoid single points of failure.

---

## Q6. Jobs in Jenkins

Jenkins jobs are automated tasks that perform various operations in the software development lifecycle, such as building, testing, and deploying applications.

### 1️⃣ Freestyle Job (Basic Jenkins Job)

**What it is:**
Freestyle job is the oldest and simplest Jenkins job. It is fully UI-based, so you don’t need to write code.

**When it is used:**

* For simple tasks
* When automation logic is small
* Good for beginners

**What you can do:**

* Pull code from Git
* Run shell commands
* Build a project
* Copy files or artifacts

**Limitations:**

* Not good for complex CI/CD
* Hard to manage large pipelines

**Interview answer:**
"Freestyle job is a basic Jenkins job configured using the UI, mainly used for simple build or script execution tasks."

---

### 2️⃣ Pipeline Job (Most Important)

**What it is:**
Pipeline job is a modern Jenkins job where the whole CI/CD flow is written as code using a Jenkinsfile.

**Why it is important:**

* Everything is version-controlled
* Easy to manage large pipelines
* Industry standard for DevOps

**What it supports:**

* Multiple stages (build, test, deploy)
* Error handling
* Parallel execution

**Where Jenkinsfile lives:**

* Inside the Git repository

**Interview answer:**
"Pipeline job uses a Jenkinsfile to define CI/CD steps as code, making the pipeline scalable and maintainable."

---

### 3️⃣ Multibranch Pipeline Job

**What it is:**
Multibranch Pipeline job automatically creates separate pipelines for each Git branch.

**Why it is useful:**

* No need to create jobs manually
* Jenkins detects new branches automatically
* Each branch runs its own Jenkinsfile

**Best use case:**

* Git workflows with main, dev, and feature branches

**Interview answer:**
"Multibranch pipeline automatically runs Jenkins pipelines for each Git branch using the Jenkinsfile present in that branch."

---

### 4️⃣ Maven Job

**What it is:**
Maven job is a Jenkins job specially designed for Java projects that use Maven.

**Why it is used:**

* Built-in Maven support
* Easy Java project builds
* Handles dependencies automatically

**Common command:**

```bash
mvn clean install
```

**Interview answer:**
"Maven job is used to build Java applications using Maven with built-in dependency management."

---

### 5️⃣ Folder Job

**What it is:**
Folder job is not a build job. It is used only to organize Jenkins jobs.

**Why it is needed:**

* Large Jenkins servers have many jobs
* Helps keep jobs clean and structured

**Example usage:**

* Separate folders for Dev, QA, and Prod

**Interview answer:**
"Folder job is used to organize Jenkins jobs into logical groups for better management."

---

### 6️⃣ External Job

**What it is:**
External job is a job where Jenkins does not control the execution. Another system triggers and manages it.

**When it is used:**

* Integration with legacy systems

**Reality check:**

* Rare in modern CI/CD
* Mostly theoretical / legacy

**Interview answer:**
"External job is controlled by external systems, and Jenkins only monitors its execution."

---

## Q7. What is Jenkins Pipeline?

A Jenkins pipeline is an automated workflow that defines the steps required to build, test, and deploy an application.

It is written as code using a Jenkinsfile, allowing better automation, version control, and consistency in CI/CD processes.

---

## Q8. Declarative vs Scripted Pipeline

Jenkins supports two types of pipelines: Declarative and Scripted.

* Declarative pipelines follow a structured syntax and are easier to read and maintain.
* Scripted pipelines use Groovy scripting and provide more flexibility but are more complex.

In real projects, Declarative pipelines are generally preferred.

---

## Q9. Jenkins Multibranch Pipeline

Jenkins Multibranch Pipeline automatically creates and manages pipelines for each branch in a Git repository.

It uses the Jenkinsfile present in each branch and supports feature branches, pull requests, and Git-based workflows.

---

## Q10. Jenkins Multi-Agent Architecture

Jenkins Multi-Agent architecture uses multiple agent nodes to execute jobs while the Jenkins controller manages scheduling and coordination.

Benefits:

* Parallel builds
* Better performance
* Scalability
* Fault tolerance

In modern setups, agents are dynamically created using Docker or Kubernetes.

---

## Q11. What is a Build Artifact?

A build artifact in Jenkins is the output generated after a build process, such as:

* JAR file
* Docker image
* Packaged application

Artifacts ensure consistency across environments and are used for deployment.

---

## Q12. What is a Build Pipeline?

A build pipeline is an automated sequence of steps that takes source code, builds it, runs tests, and produces a deployable artifact.

It reduces manual errors and speeds up software delivery in DevOps workflows.

---

## Q13. Types of Credentials in Jenkins

### 1️⃣ Username and Password

* Used for GitHub, GitLab, servers, databases
* Most commonly used

### 2️⃣ SSH Username with Private Key

* Used for Git over SSH and server access
* Very common in DevOps pipelines

### 3️⃣ Secret Text

* Used for API tokens, access tokens, webhook secrets

### 4️⃣ Secret File

* Used for .pem files, .json keys, certificates

### 5️⃣ Certificate

* Used for secure enterprise authentication
* Less common but good to know

---

## Q14. Jenkins Shared Library

Jenkins Shared Library stores reusable pipeline logic in a central repository so multiple pipelines can use the same code.

This reduces duplication and improves maintainability.

---

## Q15. SonarQube Quality Gate

"SonarQube Quality Gate checks code quality. If the code is bad, Jenkins stops the pipeline."

It checks:

* Bugs
* Security vulnerabilities
* Code coverage
* Code duplication

If issues exceed the allowed threshold → **Pipeline FAILS**.

---

## Q16. How to Reduce SonarQube Scanning Time in Jenkins

* Skip unnecessary files
* Scan only new code
* Run scans on important branches only
* Use a well-sized SonarQube server

Bad configuration is the main reason for slow scans.

---

## Q17. What is a Webhook?

A webhook is a way for one tool to automatically inform another tool when an event occurs.

Example:
"A webhook automatically triggers a Jenkins job when code is pushed to Git."

---


