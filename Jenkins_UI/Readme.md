
# Jenkins UI 

---

## Jenkins Main Dashboard

### What is the Jenkins Dashboard?

The Jenkins dashboard is your **main control center** - the first page you see after logging into Jenkins.

### What You'll See:

- **New Item Button**: Create new jobs/projects
- **All Jobs List**: View all your build projects
- **Build Status**: See which jobs are passing or failing
- **Jenkins Version**: Check your Jenkins version
- **Manage Jenkins**: Configure system settings, plugins, users
- **My Views**: Organize jobs into custom views
- **Build Queue**: Jobs waiting to be executed
- **Executor Status**: Running and idle executors

### Why You Use It?

- Central point to **manage all your CI/CD automation**
- Quick overview of **project health**
- Easy access to **create and configure jobs**
- Monitor **build status in real-time**

---

## 🏢 Jobs & Projects

### What are Jenkins Jobs?

A **Job** (also called a Project) is the **smallest unit of work in Jenkins**[44]. It represents a single automated task like building code, running tests, or deploying applications.

### Why Use Jobs?

- Each job is **independent and reusable**
- Jobs can be **triggered manually or automatically**
- Easy to **organize and manage** related tasks
- Can be **chained together** in pipelines

### Job Types Available:

| Job Type | What It Does | When To Use |
|----------|-------------|------------|
| **Freestyle Project** | Traditional job using UI for configuration | Simple builds, beginners, quick setup |
| **Pipeline** | Code-based workflow in a Jenkinsfile | Complex workflows, version-controlled builds |
| **Multi-Configuration Project** | Runs same build with different configurations | Testing on multiple platforms (Windows/Linux) |
| **Folder** | Organizes jobs hierarchically | Large teams, multiple projects |
| **Multi-Branch Pipeline** | Automatically creates jobs per branch | Git repo with multiple branches |
| **Organization Folder** | Scans entire GitHub/Bitbucket organization | Large organizations with many repos |

---

## 🔌 Plugins

### What are Jenkins Plugins?

**Plugins** are **add-ons that extend Jenkins functionality**. They're like apps for Jenkins that add new capabilities.

### Why Use Plugins?

- **Extend Jenkins** beyond built-in features
- **Integrate with external tools** (GitHub, Slack, Docker, AWS, etc.)
- **Add new job types** and build steps
- **Customize Jenkins** for your needs
- **Over 2,000 plugins** available for free[52]

### Popular Jenkins Plugins:

| Plugin Name | What It Does | Example Use |
|-------------|-------------|------------|
| **Git Plugin** | Integrates with GitHub/GitLab | Fetch source code from repositories |
| **Docker Plugin** | Run builds in Docker containers | Build and test Java apps in Docker |
| **Pipeline Plugin** | Enable Jenkins Pipeline support | Create complex multi-stage pipelines |
| **Blue Ocean Plugin** | Modern UI for pipelines | Better visualization of pipeline runs |
| **Kubernetes Plugin** | Run dynamic agents on Kubernetes | Scale builds on K8s clusters |
| **Amazon EC2 Plugin** | Use AWS EC2 as build agents | Run builds on cloud VMs |
| **Email Extension Plugin** | Send custom email notifications | Notify team on build success/failure |
| **Slack Plugin** | Send Slack notifications | Post build status to Slack channel |
| **Timestamper Plugin** | Add timestamps to build logs | Track when each build step runs |
| **Credentials Binding Plugin** | Securely use credentials in builds | Pass API keys without exposing them |

### How to Install Plugins:

1. Click **Manage Jenkins** on the left sidebar
2. Click **Manage Plugins**
3. Go to **Available** tab
4. Search for the plugin you want
5. Click **Install**
6. Restart Jenkins when prompted

---

## ⚙️ Free Style Projects

### What is a Freestyle Project?

A **Freestyle Project** is the **simplest and most flexible** job type in Jenkins. You configure everything through the **web UI without writing code**.

### Why Use Freestyle?

✅ **Easy to start** - No scripting required  
✅ **Powerful** - Highly customizable  
✅ **Great for beginners** - Simple UI interface  
✅ **Flexible** - Add any build step you need  
✅ **Reusable** - Can be copied and modified easily  

### When to Use Freestyle?

- Simple build tasks (compile, test, deploy)
- One-off automation projects
- Quick prototyping
- Jobs that don't require version control of pipeline

### When NOT to Use Freestyle?

- Very complex workflows - Use **Pipeline** instead
- Need version control of build steps - Use **Pipeline** instead
- Want to reuse logic across jobs - Use **Pipeline** instead

### Creating a Freestyle Job:

1. Click **New Item**
2. Enter job name
3. Select **Freestyle project**
4. Click **OK**
5. Configure sections:
   - General (description, parameters)
   - Source Code Management (where to fetch code)
   - Build Triggers (when to build)
   - Build Environment (setup before build)
   - Build Steps (what to execute)
   - Post-Build Actions (after build completes)

---

## 🔄 Pipeline

### What is a Jenkins Pipeline?

A **Pipeline** is a **way to define your entire CI/CD workflow as code** (in a file called `Jenkinsfile`).

### Why Use Pipeline?

✅ **Version Controlled** - Pipeline code in Git repository  
✅ **Complex Workflows** - Support multi-stage, parallel execution  
✅ **Reusable** - Can be shared across teams  
✅ **Better Visualization** - See entire workflow at a glance  
✅ **More Powerful** - Advanced scripting capabilities  

### When to Use Pipeline?

- Complex CI/CD workflows
- Want pipeline code versioned in Git
- Need parallel execution of stages
- Teams working on enterprise applications

### Pipeline Syntax:

```groovy
pipeline {
  agent any
  
  stages {
    stage('Build') {
      steps {
        echo 'Building application...'
        sh 'mvn clean compile'
      }
    }
    
    stage('Test') {
      steps {
        echo 'Running tests...'
        sh 'mvn test'
      }
    }
    
    stage('Deploy') {
      steps {
        echo 'Deploying...'
        sh './deploy.sh'
      }
    }
  }
  
  post {
    always {
      echo 'Pipeline finished'
    }
  }
}
```

### Pipeline vs Freestyle:

| Feature | Freestyle | Pipeline |
|---------|-----------|----------|
| **Configuration** | GUI-based | Code-based (Jenkinsfile) |
| **Version Control** | Manual | In Git repo |
| **Complexity** | Simple workflows | Complex workflows |
| **Parallel Stages** | Not native | Full support |
| **Learning Curve** | Easier | Steeper |
| **Reusability** | Limited | High |
| **Visualization** | Basic | Excellent (Blue Ocean) |

---

## 📊 Multi Configuration Projects

### What is a Multi-Configuration Project?

A **Multi-Configuration Project** (also called **Matrix Project**) runs the **same build with different configurations** automatically.

### Why Use Multi-Configuration?

- Test your application on **multiple platforms** (Windows, Linux, Mac)
- Test with **different environments** (Dev, Test, Prod)
- Build with **different versions** of tools (Java 8, 11, 17)
- All in **one job** with automatic combinations

### When to Use It?

- Cross-platform testing (Windows + Linux + Mac)
- Multiple database versions
- Different compiler versions
- Testing in multiple environments

### Example Scenario:

You have a Java application that needs to build on:
- Windows, Linux, Mac (3 OS)
- Java 8, 11, 17 (3 versions)

With Matrix Project: **1 job configuration × 9 automatic builds** = 9 builds total

Without Matrix Project: **9 separate jobs** to maintain manually

### How It Works:

1. Define **axes** (variables to vary)
2. Specify **values** for each axis
3. Jenkins creates **combinations** of all values
4. Each combination gets **its own build**
5. Can **exclude combinations** if needed

---

## 📁 Folders

### What are Folders?

**Folders** let you **organize jobs hierarchically** like a file system.

### Why Use Folders?

✅ **Better Organization** - Group related jobs together  
✅ **Cleaner UI** - Easier to find jobs  
✅ **Hierarchical** - Can create nested folders  
✅ **Security** - Control access per folder  
✅ **Taxonomy** - Organize by project, team, or app  

### When to Use Folders?

- Large teams with 50+ jobs
- Multiple projects running in Jenkins
- Want to organize by department or product
- Need folder-level permissions

### Folder Examples:

```
Jenkins
├── Backend/
│   ├── API-Service
│   ├── Database-Service
│   └── Auth-Service
├── Frontend/
│   ├── Web-App
│   └── Mobile-App
└── DevOps/
    ├── Infrastructure
    └── Deployment
```

### How to Create Folders:

1. Click **New Item**
2. Enter folder name
3. Select **Folder**
4. Click **OK**
5. Create jobs **inside** the folder

---

## 🌿 Multi-Branch Pipeline

### What is Multi-Branch Pipeline?

A **Multi-Branch Pipeline** **automatically creates separate jobs for each branch** in your Git repository.

### Why Use Multi-Branch?

✅ **Automatic Branch Detection** - New branch = New job  
✅ **Pull Request Support** - Build PRs automatically  
✅ **Single Configuration** - Configure once, works for all branches  
✅ **Clean Organization** - Jobs per branch are organized  
✅ **No Manual Maintenance** - Delete branch = Job disappears  

### When to Use It?

- Git repository with multiple branches
- Want each branch auto-built
- Need to test pull requests
- Developers working on feature branches

### How It Works:

1. You push a **new branch** to GitHub
2. Jenkins **detects** the new branch
3. Jenkins **automatically creates** a job for that branch
4. Jenkins **finds Jenkinsfile** in the branch
5. Jenkins **runs the pipeline** for that branch

### Requirements:

- Repository must have a **Jenkinsfile** in root
- Each branch should have a **Jenkinsfile** (optional, can inherit)
- Repository connected to **GitHub/GitLab/Bitbucket**

---

## 🏛️ Organization Folder

### What is Organization Folder?

An **Organization Folder** **scans an entire GitHub Organization or Bitbucket Team** and **automatically creates Multibranch Pipeline jobs** for each repository.

### Why Use Organization Folder?

✅ **Massive Automation** - Scan entire organization  
✅ **Zero Manual Setup** - New repo = Auto job created  
✅ **Team Scalability** - Perfect for large teams  
✅ **Consistency** - Same pipeline rules for all repos  
✅ **Self-Service** - Developers push Jenkinsfile, job auto-created  

### When to Use It?

- Organization with 10+ repositories
- Want all repos to auto-build
- Each repo has Jenkinsfile
- Large development teams

### Example Workflow:

1. Developer creates new repository in GitHub Org
2. Developer adds `Jenkinsfile` to repo
3. Jenkins **automatically** detects repo
4. Jenkins **automatically** creates Multibranch Pipeline
5. Jenkins **automatically** builds all branches

### How to Create:

1. Click **New Item**
2. Enter name (e.g., "GitHub Organization")
3. Select **Organization Folder**
4. Click **OK**
5. Configure GitHub credentials
6. Specify organization name
7. Jenkins will scan and auto-create jobs

---

## 🔧 Free Style Project Configuration

Now let's dive into **detailed configuration sections** of a Freestyle job.

### General Section

#### Description

**What**: Add a **description** of what this job does  
**Why**: Help yourself and team understand the job's purpose  
**Example**: "Builds and tests the Payment Service API"

#### Discard Old Builds

**What**: Automatically **delete old builds** to save disk space  
**Why**: Jenkins stores build history (logs, artifacts, etc.) which takes space  
**When to Use**: When you have many builds per day  

**Options**:
- **Max # of builds to keep**: Keep only last 10 builds
- **Max # of days to keep builds**: Keep only builds from last 30 days

#### This project is parameterized

**What**: Allow **passing parameters** when triggering a build  
**Why**: Run the same job with different inputs (branch, environment, etc.)  
**Example**: Ask user to choose which Git branch to build

---

### Source Code Management (SCM)

**What**: Tell Jenkins **where to fetch your code**

#### None

**What**: Don't fetch any code repository  
**Use Case**: Jobs that don't need source code (notifications, deployments)

#### Git

**What**: Fetch code from **Git repository** (GitHub, GitLab, etc.)  
**Configure**: 
- Repository URL (e.g., `https://github.com/user/repo.git`)
- Branch to build (e.g., `main`, `develop`, `feature-branch`)
- Credentials (username/password or SSH key)

#### Other VCS (Subversion, CVS, etc.)

Available through plugins

---

### Build Triggers

**What**: **When should Jenkins automatically trigger a build?**

#### Trigger builds remotely

**What**: Trigger a build by **calling a special URL** externally
**Why**: Integrate with external systems, webhooks, or scripts  
**How It Works**:
1. Check **"Trigger builds remotely"**
2. Set an **Authentication Token** (secret string)
3. Call URL: `http://jenkins-url/job/JobName/build?token=YOUR_TOKEN`
4. Build gets triggered!

**Example Use Cases**:
- Trigger from another system
- Trigger from a cron job
- Trigger from monitoring system
- CI/CD integration from external tools

#### Build after other projects are built

**What**: **Start this build only after another project** successfully completes 
**Why**: Create dependent jobs (build depends on another project)  
**How It Works**:
1. Specify project name (e.g., "ProjectA")
2. Choose trigger condition:
   - **"Trigger only if build is stable"** - Only if successful
   - **"Trigger even if build is unstable"** - Even with warnings
3. When ProjectA finishes → This job starts

**Example Pipeline**: ProjectA builds → ProjectB deploys staging → ProjectC tests

#### Build periodically

**What**: Run the build on a **schedule** (like a cron job)  
**Why**: Run nightly builds, daily reports, scheduled maintenance  
**Format**: Uses **cron syntax**

```
H 2 * * *      # Run daily at 2 AM
H 14 * * *     # Run daily at 2 PM
H 2 * * 0      # Run every Sunday at 2 AM
H 22 * * 1-5   # Run weekdays at 10 PM
```

**Use Cases**:
- Nightly builds
- Daily test runs
- Scheduled deployments
- Automated cleanup jobs

#### GitHub hook trigger for GITScm polling

**What**: Build triggered **automatically when code is pushed to GitHub**
**Why**: Instant feedback - build triggers immediately on code push  
**How It Works**:
1. Check **"GitHub hook trigger for GITScm polling"**
2. Add **webhook** in GitHub repo settings
3. When developer pushes code → GitHub notifies Jenkins → Build starts
4. Developer gets build result immediately

**Setup Steps**:
- In Jenkins: Check the trigger option
- In GitHub Repo: Settings → Webhooks → Add webhook
- Webhook URL: `http://jenkins-url/github-webhook/`
- GitHub will notify Jenkins on code push

**Advantages**:
- **Real-time feedback** - No waiting for polling
- **Reduced latency** - Builds start immediately
- **Webhook is efficient** - No constant polling of repo

#### Poll SCM

**What**: Jenkins **regularly checks** the repository for changes
**Why**: Older method (before webhooks), simpler setup, no external config  
**How It Works**:
1. Every X minutes, Jenkins checks: "Did code change?"
2. If yes → Start a build
3. If no → Wait and check again

**Format**: Same cron syntax as "Build periodically"

```
H/15 * * * *   # Check every 15 minutes
H/5 * * * *    # Check every 5 minutes
```

**Disadvantages**:
- **Higher latency** - Wait up to polling interval for build
- **Resource intensive** - Constant polling of repo
- **Less efficient** than webhooks

**When to Use**: 
- GitHub webhook not available
- Enterprise firewall blocks webhooks
- Legacy systems

### Difference: Webhook vs Poll SCM

| Feature | Webhook | Poll SCM |
|---------|---------|----------|
| **Response Time** | Instant (seconds) | Up to polling interval |
| **Efficiency** | Very efficient | Resource intensive |
| **Setup** | Need GitHub config | Just Jenkins config |
| **Network** | Push notification | Pull/check regularly |
| **Best For** | Modern setups | Legacy/limited firewall |

---

### Build Environment

**What**: Setup the **environment before the build starts**

#### Delete workspace before build starts

**What**: **Wipe clean the workspace** (remove old files) before building
**Why**: Ensure clean state, prevent conflicts with old files  
**When to Use**: Most jobs should have this enabled  
**What Gets Deleted**: All files from previous builds in workspace

#### Use secret text or files

**What**: Securely use **passwords, API keys, credentials** in builds
**Why**: Don't expose credentials in logs or configuration  
**How It Works**:
1. Store credentials in Jenkins (username/password, API key, SSH key)
2. Reference in build steps as environment variables
3. Jenkins **masks** the value in logs (shows `****`)
4. Build can use the credential securely

**Types of Credentials**:
- **Secret Text**: API keys, tokens, passwords
- **Secret File**: Certificates, SSH keys, config files
- **Username/Password**: For authentication
- **SSH Username with Private Key**: For secure SSH access

**Example**:
```bash
# In Jenkins, you set: MY_API_KEY = "sk-12345abcde"
# In build step:
echo "API Key is: $MY_API_KEY"  # Shows as: API Key is: ****
```

#### Add timestamps to console output

**What**: Add **timestamps** to each line of build logs
**Why**: Know exactly when each build step ran, helps with optimization  
**What It Shows**:
```
14:23:45 [Build] Starting build...
14:23:46 [Build] Fetching code from GitHub...
14:24:12 [Build] Compiling...
14:24:45 [Build] Running tests...
14:25:12 [Build] Done!
```

**Use Cases**:
- Find slow build steps
- Understand build timeline
- Troubleshoot timing issues
- Performance optimization

#### Inspect build logs for published build scans

**What**: Integrate with **Gradle Build Scans** for detailed analysis  
**Why**: Get deep insight into build performance and issues  
**Requirements**: Gradle build with Build Scan plugin

#### Terminate a build if it stuck

**What**: **Automatically stop** a build if it hangs/gets stuck
**Why**: Prevent runaway builds consuming resources forever  
**How It Works**:
1. Set a timeout (e.g., 30 minutes)
2. If build doesn't complete in 30 minutes → Jenkins kills it
3. Build marked as **Failed** or **Aborted**

**Timeout Strategies**:
- **Absolute**: Set fixed timeout (30 minutes)
- **Deadline**: Build must finish by certain time (2 PM)
- **Elastic**: Based on previous builds average
- **Likely Stuck**: Kill if no output for X minutes
- **No Activity**: Kill if no console output for X time

**Example**:
```
Timeout: 30 minutes
Strategy: No Activity
If build has no console output for 5 minutes → ABORT
```

**Benefits**:
- Free up executors
- Prevent hung processes
- Automatic cleanup
- Resource management

---

### Build Steps

**What**: **What actually runs during the build** (the main work)

#### Execute shell

**What**: Run **bash/shell commands**  
**Example**:
```bash
#!/bin/bash
echo "Starting build..."
npm install
npm run build
npm run test
echo "Build completed!"
```

**Use Cases**:
- Run custom scripts
- Execute any Linux command
- Python scripts, YAML processing, etc.

#### Execute Windows batch command

**What**: Run **Windows batch commands**  
**Example**:
```batch
@echo off
echo Starting build on Windows...
call npm install
call npm run build
```

#### Invoke Ant

**What**: Build with **Apache Ant** (Java build tool)
**Configure**:
- Ant version
- Targets to run (e.g., `clean compile test package`)

**Example Ant targets**:
```
clean        # Remove old build artifacts
compile      # Compile Java code
test         # Run tests
package      # Create JAR/WAR file
deploy       # Deploy application
```

#### Invoke Maven

**What**: Build with **Apache Maven** (Java build tool)  
**Configure**:
- Maven version
- Goals to run (e.g., `clean install`)

**Common Maven goals**:
```
clean           # Remove old artifacts
install         # Build and install
package         # Create JAR/WAR
deploy          # Deploy to repo
test            # Run tests
```

#### Build Step Options

Many other plugins add build steps:
- **Gradle Build**: Run Gradle builds
- **Docker Build**: Build Docker images
- **AWS CLI**: Run AWS commands
- **Kubernetes**: Deploy to K8s
- **CloudFormation**: Deploy infrastructure
- **Trigger parameterized build**: Call other jobs
- **Copy artifacts**: Copy files from other builds

---

### Post-Build Actions

**What**: **Actions to perform after the build completes** (success or failure)

#### Archive artifacts

**What**: **Save files** from build (JAR, WAR, logs, reports)
**Why**: Keep build outputs for download, deployment, or inspection  
**Configure**: Specify file pattern to archive
```
Example: **/*.jar          # Archive all JAR files
Example: build/reports/**  # Archive test reports
```

**Use Cases**:
- Save compiled JAR/WAR for deployment
- Keep test reports
- Archive build logs
- Store coverage reports

#### Send notifications

**What**: **Notify team** of build success/failure  

**Email Notification**:
- Configure email recipients
- Send on success, failure, or always
- Attach artifacts to email

**Slack Notification**:
- Post build status to Slack channel
- Include build logs/details
- Mention team members on failure

**Custom Webhooks**:
- Send JSON payload to external systems
- Trigger other automations

#### Publish test results

**What**: **Display test reports** in Jenkins UI  
**Configure**: Point to test result files
```
Example: **/target/surefire-reports/*.xml  # JUnit reports
Example: build/test-results/**              # Test reports
```

**Shows**: Pass/fail rates, test history, trends

#### Trigger other builds

**What**: **Start another job** after this one completes  
**Why**: Chain jobs together (build → test → deploy)  
**Configure**: Specify downstream job name and conditions

---

## 🎯 Summary

### Jenkins UI Components Overview

| Component | Purpose | Use When |
|-----------|---------|----------|
| **Jobs** | Individual build tasks | Need to automate work |
| **Freestyle** | GUI-based simple jobs | Quick and easy setup |
| **Pipeline** | Code-based complex workflows | Enterprise CI/CD |
| **Plugins** | Extend Jenkins functionality | Need more capabilities |
| **Folders** | Organize jobs hierarchically | 50+ jobs or large teams |
| **Multibranch** | Auto-build each Git branch | Multiple branches/PRs |
| **Organization Folder** | Auto-discover repos in org | Large organizations |
| **Matrix Project** | Build multiple configurations | Cross-platform testing |

### Build Configuration Workflow

```
1. Create Job
   ↓
2. Configure Source Code (GitHub, GitLab, etc)
   ↓
3. Set Build Triggers (When to build)
   ↓
4. Configure Build Environment (Setup)
   ↓
5. Add Build Steps (What to run)
   ↓
6. Set Post-Build Actions (Notify, archive)
   ↓
7. Save & Trigger Build
```

---
