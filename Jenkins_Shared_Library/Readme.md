
# 📚 Jenkins Shared Library 



---

## What is Jenkins Shared Library?

**Jenkins Shared Library** is a collection of **reusable code** (Groovy scripts) that you can use in multiple Jenkins pipelines.

Think of it like this:

```
📚 Library = Book of Recipes
Jenkinsfile = Your Pipeline (uses recipes from the book)
Shared Library = Common Recipes (reused in many pipelines)
```

**Simple Definition:** Instead of writing the same pipeline code again and again in each project, you write it once in a shared library and reuse it everywhere.

**Real Example:**
```
Without Shared Library:
Pipeline 1 Jenkinsfile → 100 lines of code to deploy to AWS
Pipeline 2 Jenkinsfile → Same 100 lines of code (copy-paste)
Pipeline 3 Jenkinsfile → Same 100 lines of code (copy-paste)
Problem: If you find a bug, you fix it in 3 places!

With Shared Library:
Pipeline 1, 2, 3 Jenkinsfile → Call shared library function: deployToAWS()
Problem: If you find a bug, you fix it in 1 place (the library)
```

---

## Why Do We Need Shared Libraries?

### Problems Without Shared Libraries:

- ❌ **Copy-Paste Hell** - Write same code in 10 different Jenkinsfiles
- ❌ **Maintenance Nightmare** - Fix a bug in 10 places instead of 1
- ❌ **Inconsistency** - Different teams have different versions of the same code
- ❌ **Waste of Time** - Developers write the same code over and over
- ❌ **Error Prone** - Each copy might have different bugs or versions
- ❌ **Scalability Issues** - Adding new projects means copying old code again

### Real Scenario:

```
Company has 20 microservices. Each needs to:
1. Build Java code
2. Run tests
3. Build Docker image
4. Push to Docker registry
5. Deploy to Kubernetes

Without Shared Library:
- Each team writes same 200 lines of Jenkinsfile code
- Takes 20 × 200 = 4000 lines of duplicated code
- Each team wastes 2 hours writing pipeline code
- Total wasted time: 40 hours

With Shared Library:
- Write 200 lines once in shared library
- Each team calls: deployMicroservice()
- Takes 20 × 5 minutes to set up = 1.7 hours
- Saved time: 38+ hours
```

---

## When to Use Shared Libraries?

| Situation | Use Shared Library? |
|-----------|-------------------|
| **Multiple projects with same build process** | YES - Perfect for this |
| **Same deployment steps for multiple teams** | YES - Saves time |
| **Common security scans (SonarQube, SAST)** | YES - Standardize checks |
| **Common notification steps (Slack, email)** | YES - One place to update |
| **Same artifact repository steps (Nexus, Artifactory)** | YES - Reuse everywhere |
| **Different infrastructure provisioning** | NO - Too different |
| **Small project with unique workflow** | NO - Overkill |
| **Enterprise with multiple teams** | ALWAYS - Essential |
| **CI/CD that needs standardization** | ALWAYS - Best practice |

---

## Benefits of Shared Libraries

### 🔄 Code Reusability (DRY Principle)
- Write code once, use it in 20+ pipelines
- Save development time
- Focus on business logic, not repetitive code

**Example:** Build Maven project
```
Shared Library: buildMaven()
All Maven projects call this function
Update once, all projects get the fix
```

---

### 🎯 Consistency Across Teams
- All teams use same deployment process
- Same quality checks everywhere
- Same security standards
- Prevents "works on my machine" problems

**Example:** Deployment to AWS
```
Team A deploys: deployToAWS(prod)
Team B deploys: deployToAWS(prod)
Both use same steps, same security, same process
No surprises
```

---

### 🐛 Easy Maintenance and Bug Fixes
- Fix a bug in one place (the library)
- All pipelines using it immediately get the fix
- No need to update 20 different Jenkinsfiles

**Example:** Fix bug in Docker image build
```
Before (without library): Fix in 20 Jenkinsfiles
After (with library): Fix in 1 shared library file
```

---

### 📚 Knowledge Sharing
- Senior DevOps engineers write best practices in library
- Junior developers use proven code
- Team learns from each other
- Reduces learning curve

**Example:** Security scanning
```
Shared Library has: runSecurityScan()
Includes: SonarQube, SAST, dependency check
Junior dev doesn't need to know all tools
Just calls: runSecurityScan()
Learns best practices from the code
```

---

### ⚡ Faster Project Setup
- New project? Just call shared library functions
- No need to write pipelines from scratch
- Onboard new teams faster

**Example:** New microservice team
```
Without library: 1 week to write and test Jenkinsfile
With library: 2 hours to set up pipeline
```

---

### 🤝 Team Collaboration
- Centralized pipeline logic
- Different teams contribute improvements
- Shared ownership of CI/CD

**Example:**
```
Platform Team: Creates and maintains shared library
Team A: Uses library + contributes improvements
Team B: Uses library + suggests new functions
Team C: Uses library + reports bugs
```

---

## How Shared Library Works

### The Flow

```
1. CREATE SHARED LIBRARY
   └─ Write Groovy code
   └─ Store in Git repository
   └─ Example: deployToKubernetes()

2. CONFIGURE JENKINS
   └─ Tell Jenkins where shared library is (Git URL)
   └─ Jenkins remembers this configuration

3. USE IN JENKINSFILE
   └─ Import: @Library("my-shared-lib")
   └─ Call function: deployToKubernetes()

4. JENKINS EXECUTES
   └─ Jenkins downloads shared library from Git
   └─ Executes the function
   └─ Pipeline continues
```

### Detailed Flow

**Step 1: Git Repository has Shared Library**
```
GitHub Repository:
├── vars/
│   ├── deployAWS.groovy      ← Function to deploy to AWS
│   ├── buildDocker.groovy    ← Function to build Docker image
│   └── runTests.groovy       ← Function to run tests
├── src/
│   └── com/company/Logger.groovy  ← Helper classes
└── resources/
    └── config.json           ← Configuration files
```

**Step 2: Configure in Jenkins**
```
Jenkins → Manage Jenkins → Configure System
→ Global Pipeline Libraries
→ Name: my-shared-lib
→ Repository URL: https://github.com/company/jenkins-shared-lib
```

**Step 3: Use in Jenkinsfile**
```groovy
@Library("my-shared-lib") _

pipeline {
    stages {
        stage("Build") {
            steps {
                buildDocker()      // Call from shared library
            }
        }
        stage("Deploy") {
            steps {
                deployAWS()        // Call from shared library
            }
        }
    }
}
```

**Step 4: Jenkins Executes**
```
Jenkins reads Jenkinsfile
Sees: @Library("my-shared-lib")
Downloads shared library from GitHub
Executes buildDocker() function
Executes deployAWS() function
```

---

## Shared Library Structure

Jenkins Shared Libraries have a standard folder structure:

```
jenkins-shared-library/
│
├── vars/                          ← Global functions (most common)
│   ├── deployAWS.groovy          ← Function you can call
│   ├── deployAWS.txt             ← Documentation for function
│   ├── runTests.groovy           ← Another function
│   └── buildDocker.groovy        ← Another function
│
├── src/                           ← Groovy classes (advanced)
│   └── com/company/
│       ├── Logger.groovy         ← Helper class
│       └── Utils.groovy          ← Utility class
│
└── resources/                     ← Static files (config, scripts)
    └── scripts/
        └── deploy.sh             ← Shell script files
```

### Understanding Each Folder:

#### **vars/ - Global Functions (What You'll Use)**
- Contains **reusable functions** you call from Jenkinsfile
- Each `.groovy` file becomes a function you can call
- **Easiest to understand and use**
- Best for: deployment, testing, notifications

**Example: `vars/deployAWS.groovy`**
```groovy
def call(String environment) {
    echo "Deploying to AWS ${environment}"
    sh "aws s3 sync . s3://${environment}-bucket"
}
```

In Jenkinsfile:
```groovy
@Library("my-shared-lib") _

deployAWS("production")  // Calls the function above
```

---

#### **src/ - Groovy Classes (Advanced)**
- Contains **complex Groovy classes** for advanced logic
- Organized like Java packages
- Used when `vars/` is not enough
- Best for: Complex logic, database operations, API calls

**Example: `src/com/company/Logger.groovy`**
```groovy
package com.company

class Logger {
    def logger
    
    Logger(logger) {
        this.logger = logger
    }
    
    void logInfo(String message) {
        logger.echo "ℹ️ INFO: ${message}"
    }
}
```

In Jenkinsfile (advanced):
```groovy
@Library("my-shared-lib") _
import com.company.Logger

Logger logger = new Logger(this)
logger.logInfo("Starting deployment")
```

---

#### **resources/ - Static Files (Optional)**
- Contains **configuration files, scripts, JSON data**
- Used by functions in `vars/` and `src/`
- Best for: Config files, external scripts

**Example: `resources/config.json`**
```json
{
  "environments": {
    "dev": "dev-bucket",
    "prod": "prod-bucket"
  }
}
```

---

## Step-by-Step: How to Create and Use

### Step 1: Create GitHub Repository

```bash
# Create on GitHub
1. Go to github.com
2. Click "New Repository"
3. Name: "jenkins-shared-library"
4. Initialize with README
5. Create repository
```

---

### Step 2: Clone and Create Structure

```bash
git clone https://github.com/YOUR-USER/jenkins-shared-library.git
cd jenkins-shared-library

# Create folders
mkdir vars src resources

# Create your first function
cat > vars/deployAWS.groovy << 'EOF'
def call(String environment) {
    echo "🚀 Deploying to AWS environment: ${environment}"
    sh "aws s3 sync . s3://${environment}-bucket"
    echo "✅ Deployment complete!"
}
EOF

# Push to GitHub
git add .
git commit -m "Add deployAWS function"
git push origin main
```

---

### Step 3: Configure in Jenkins

```
1. Open Jenkins
2. Click "Manage Jenkins"
3. Click "Configure System"
4. Search for "Global Pipeline Libraries"
5. Click "Add"
   - Name: my-shared-lib
   - Default Version: main
   - Repository URL: https://github.com/YOUR-USER/jenkins-shared-library.git
6. Click "Save"
```

---

### Step 4: Use in Your Jenkinsfile

```groovy
@Library("my-shared-lib") _

pipeline {
    agent any
    
    stages {
        stage("Deploy to Dev") {
            steps {
                script {
                    deployAWS("dev")
                }
            }
        }
        
        stage("Deploy to Prod") {
            steps {
                script {
                    deployAWS("prod")
                }
            }
        }
    }
}
```

---

### Step 5: Run Pipeline

```
1. Create new Jenkins Pipeline job
2. Point to Git repo with Jenkinsfile above
3. Click "Build"
4. See magic happen! 🎉
   ✅ Jenkins loads shared library
   ✅ Executes deployAWS("dev")
   ✅ Executes deployAWS("prod")
```

---

## Real-World Scenarios

### Scenario 1: Build Multiple Microservices

**Problem:**
```
Company has 15 microservices written in Java
Each has same build pipeline:
1. Checkout code from Git
2. Build with Maven
3. Run unit tests
4. Build Docker image
5. Push to Docker registry
6. Deploy to Kubernetes

Each team writes same 150 lines Jenkinsfile
Total: 15 × 150 = 2250 lines duplicated code!
```

**Solution with Shared Library:**

**Create `vars/buildAndDeploy.groovy`:**
```groovy
def call(Map config) {
    echo "🏗️ Building ${config.appName}"
    
    stage("Checkout") {
        git url: config.gitRepo, branch: 'main'
    }
    
    stage("Build") {
        sh "mvn clean package -DskipTests"
    }
    
    stage("Test") {
        sh "mvn test"
    }
    
    stage("Docker Build") {
        sh "docker build -t ${config.appName}:${BUILD_NUMBER} ."
    }
    
    stage("Push to Registry") {
        sh "docker push ${config.appName}:${BUILD_NUMBER}"
    }
    
    stage("Deploy to K8s") {
        sh "kubectl set image deployment/${config.appName} app=${config.appName}:${BUILD_NUMBER}"
    }
}
```

**Each team's Jenkinsfile (now just 10 lines!):**
```groovy
@Library("my-shared-lib") _

buildAndDeploy([
    appName: "user-service",
    gitRepo: "https://github.com/company/user-service"
])
```

**Result:**
- 15 teams, each writes 10 lines instead of 150
- Saved: 2100 lines of code
- If bug found: Fix in 1 place
- All teams get fix immediately

---

### Scenario 2: Standardized Security Scanning

**Problem:**
```
Company needs to run security checks on all pipelines:
- SonarQube (code quality)
- SAST scanning (find security bugs)
- Dependency check (find vulnerable libraries)

Each team implements differently
Some teams forget steps
No standardization = Security gaps
```

**Solution:**

**Create `vars/runSecurityScan.groovy`:**
```groovy
def call(String projectName) {
    echo "🔒 Running security scans for ${projectName}"
    
    stage("SonarQube Scan") {
        sh "sonar-scanner -Dsonar.projectKey=${projectName}"
    }
    
    stage("SAST Scan") {
        sh "semgrep --config=p/owasp-top-ten --json --output=sast-results.json ."
    }
    
    stage("Dependency Check") {
        sh "dependency-check --project ${projectName} --scan . --format JSON --out dependency-check-report.json"
    }
    
    stage("Report") {
        echo "✅ Security scans complete"
    }
}
```

**All teams use it:**
```groovy
@Library("my-shared-lib") _

pipeline {
    stages {
        stage("Security") {
            steps {
                runSecurityScan("my-app")
            }
        }
    }
}
```

**Result:**
- All teams run same security checks
- Consistent standards across company
- New security tool added? Update library once
- All teams benefit immediately

---

### Scenario 3: Common Slack Notifications

**Problem:**
```
Every pipeline needs to notify team on Slack
Each developer writes notification code differently
Some pipelines notify only on failure
Some on success and failure
Some hardcode Slack URLs (security risk!)
```

**Solution:**

**Create `vars/notifySlack.groovy`:**
```groovy
def call(String status, String channel = "#general") {
    String color = status == "SUCCESS" ? "good" : "danger"
    String emoji = status == "SUCCESS" ? "✅" : "❌"
    
    sh '''
        curl -X POST ${SLACK_WEBHOOK_URL} \
        -H 'Content-type: application/json' \
        -d '{
            "attachments": [{
                "color": "${color}",
                "title": "${emoji} Build ${status}",
                "text": "Project: ${JOB_NAME}\\nBuild: ${BUILD_NUMBER}"
            }]
        }'
    '''
}
```

**All teams use it:**
```groovy
@Library("my-shared-lib") _

pipeline {
    post {
        success {
            notifySlack("SUCCESS", "#deployment-logs")
        }
        failure {
            notifySlack("FAILED", "#alerts")
        }
    }
}
```

**Result:**
- Consistent notifications everywhere
- Easy to change notification format
- URL stored securely in Jenkins credentials
- New notification channel? Update in 1 place

---

### Scenario 4: Deployment to Multiple Environments

**Problem:**
```
Deploy same application to: Dev, Staging, Prod
Same steps, different configuration
Each environment has different servers, credentials
Code is repeated for each environment
```

**Solution:**

**Create `vars/deployToEnvironment.groovy`:**
```groovy
def call(String env) {
    Map config = [
        "dev": [
            "server": "dev-server.internal",
            "port": "8080",
            "replicas": "2"
        ],
        "staging": [
            "server": "staging-server.internal",
            "port": "8080",
            "replicas": "3"
        ],
        "prod": [
            "server": "prod-server.internal",
            "port": "8443",
            "replicas": "10"
        ]
    ]
    
    def envConfig = config[env]
    
    echo "🚀 Deploying to ${env}"
    echo "Server: ${envConfig.server}"
    echo "Replicas: ${envConfig.replicas}"
    
    sh "kubectl scale deployment myapp --replicas=${envConfig.replicas}"
    sh "kubectl set image deployment/myapp app=${DOCKER_IMAGE}:${BUILD_NUMBER}"
}
```

**Use in Jenkinsfile:**
```groovy
@Library("my-shared-lib") _

pipeline {
    stages {
        stage("Deploy Dev") {
            steps {
                deployToEnvironment("dev")
            }
        }
        stage("Deploy Staging") {
            steps {
                deployToEnvironment("staging")
            }
        }
        stage("Deploy Prod") {
            steps {
                deployToEnvironment("prod")
            }
        }
    }
}
```

**Result:**
- Same function, different configuration
- Easy to add new environments
- Configuration centralized in library
- All teams use same deployment logic

---

## DevOps Engineer Perspective

### What DevOps Engineers Know

As a DevOps engineer, you should understand:

#### **1. Build Once, Use Everywhere**
- Your job is to create standards
- Write deployment logic in shared library
- All teams reuse it
- You maintain it, everyone benefits

#### **2. Security and Consistency**
- Shared library enforces security best practices
- All deployments follow same process
- Audit trails are consistent
- Compliance is easier

#### **3. Scalability**
- New project needs 2 hours, not 2 weeks
- Onboard new teams faster
- Scale the company without scaling headcount
- Keep quality consistent

#### **4. Version Control**
- Shared library is Git-versioned code
- Track changes in library
- Rollback if something breaks
- Tag releases for stability

#### **5. Contributing to Library**
- Teams report bugs in library
- You fix it once, everyone benefits
- Teams suggest improvements
- Library evolves with company needs

#### **6. Documentation Matters**
- Write clear documentation (.txt files)
- Less time answering "How do I use this?"
- New developers self-service
- Reduces support burden

---

### Real DevOps Job Title

In job descriptions, you'll see:

```
"Maintain CI/CD infrastructure"
= Create and maintain Jenkins shared libraries

"Standardize deployment process"
= Write shared library functions

"Onboard new teams quickly"
= Create templates in shared library

"Reduce deployment time"
= Create reusable library functions

"Enable self-service CI/CD"
= Make library easy to use
```

---

## Best Practices

### ✅ DO

1. **Start with Simple Functions**
   - Don't make it too complex
   - Start with `vars/` folder
   - Learn before moving to `src/` folder

2. **Use Meaningful Names**
   - `deployToKubernetes()` ✅ (clear)
   - `deploy()` ❌ (too vague)
   - `buildDocker()` ✅ (clear)
   - `build()` ❌ (too vague)

3. **Document Everything**
   - Create `.txt` files for documentation
   - Explain parameters
   - Show examples
   - Help teams use it correctly

4. **Version Your Library**
   - Use semantic versioning (1.0.0, 1.1.0, 2.0.0)
   - Tag releases in Git
   - Let teams choose version to use
   - Don't force breaking changes

5. **Test Your Shared Library**
   - Write tests for library functions
   - Test before committing
   - Prevent bugs in production pipelines

6. **Keep Functions Focused**
   - One function = one job
   - `deployAWS()` - just deployment
   - `runTests()` - just testing
   - Don't mix multiple concerns

### ❌ DON'T

1. **Don't Overload Functions**
   - One function shouldn't do everything
   - Keep functions single-purpose
   - Makes testing easier

2. **Don't Hardcode Values**
   - Externalize configuration
   - Use parameters instead
   - Makes functions reusable

3. **Don't Ignore Error Handling**
   - Add error checking
   - Provide clear error messages
   - Help teams debug issues

4. **Don't Skip Documentation**
   - Nobody will use it
   - Teams waste time figuring it out
   - Support burden increases

5. **Don't Make Backward Incompatible Changes Without Warning**
   - Breaks other teams' pipelines
   - Use versioning properly
   - Deprecate old functions first

---

## Quick Reference

### Common Shared Library Functions

| Function | Purpose | Example |
|----------|---------|---------|
| `buildDocker()` | Build Docker image | Called by all microservices |
| `deployToK8s()` | Deploy to Kubernetes | Deploy dev, staging, prod |
| `runTests()` | Run unit/integration tests | Standard test suite |
| `runSecurityScan()` | Run security tools | SonarQube, SAST, etc. |
| `notifySlack()` | Send Slack notifications | Alert team on success/failure |
| `publishArtifact()` | Push to artifact repo | Nexus, Artifactory, etc. |
| `deployToAWS()` | Deploy to AWS | Lambda, EC2, ECS, etc. |
| `generateReport()` | Generate reports | Coverage, dependency, etc. |

---

### Function Naming Convention

```
Verb + Technology/Purpose

✅ Good:
buildDocker()
deployToKubernetes()
runSecurityScan()
publishToNexus()
notifySlack()

❌ Bad:
build()
deploy()
scan()
publish()
notify()
```

---


## Real-World Questions You'll Get Asked

### Interview Questions:

1. **"What's the advantage of shared library over copy-paste Jenkinsfile?"**
   - Answer: Fix in 1 place instead of 50 places. Consistency. Faster onboarding.

2. **"How do you version a shared library?"**
   - Answer: Git tags or branches. Teams can use specific version or main branch.

3. **"What goes in vars/ vs src/?"**
   - Answer: vars = simple functions (use directly). src = complex classes (advanced).

4. **"How do you ensure shared library doesn't break pipelines?"**
   - Answer: Test it. Version it. Warn teams before breaking changes.

5. **"Can multiple teams use same shared library?"**
   - Answer: Yes! That's the point. All teams import same library, add parameters for customization.

---
