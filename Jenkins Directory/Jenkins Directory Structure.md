

## What is JENKINS_HOME?

**JENKINS_HOME** is the main folder where Jenkins stores all its important data:
- 🔧 Configuration files
- 💼 Job definitions
- 📊 Build history
- 🔐 Credentials and secrets
- 📦 Plugins
- 📝 Logs and artifacts

Think of it as the **"brain" of Jenkins** - everything important lives here!

### Default Locations:

| OS | Path |
|----|----|
| **Linux (Package)** | `/var/lib/jenkins` |
| **Linux (Manual)** | `/opt/jenkins` or `/home/jenkins/.jenkins` |
| **macOS** | `~/.jenkins` or `/Library/Jenkins` |
| **Windows** | `C:\Users\[YourUsername]\.jenkins` |
| **Docker** | `/var/jenkins_home` |

---

## Finding Your JENKINS_HOME

### Method 1: Via Command Line
```bash
echo $JENKINS_HOME
```

### Method 2: Via Jenkins UI
1. Go to **Manage Jenkins** → **System Information**
2. Look for "JENKINS_HOME" in the list

### Method 3: Find it manually
```bash
# Linux
ps aux | grep jenkins | grep -v grep

# macOS
launchctl list | grep jenkins

# Windows (PowerShell)
Get-Process java | Where-Object {$_.ProcessName -like "*jenkins*"}
```

---

## Complete Directory Structure

```
JENKINS_HOME/
│
├── 📄 config.xml                          # Global Jenkins configuration
├── 📄 jenkins.model.JenkinsLocationConfiguration.xml
├── 📄 hudson.model.UpdateCenter.xml
├── 📄 credentials.xml
│
├── 📁 jobs/                               # All job definitions and builds
│   ├── my-pipeline/
│   │   ├── config.xml                     # Job configuration
│   │   ├── workspace/                     # Working directory
│   │   └── builds/
│   │       ├── 1/
│   │       │   ├── build.xml
│   │       │   ├── log                    # Console output
│   │       │   └── changelog.xml
│   │       ├── 2/
│   │       └── lastStableBuild → builds/1/
│   │
│   └── another-job/
│       └── ...
│
├── 📁 plugins/                            # Installed Jenkins plugins
│   ├── git.jpi
│   ├── pipeline-model-definition.jpi
│   ├── docker.jpi
│   └── kubernetes.jpi
│
├── 📁 secrets/                            # Encrypted credentials & keys
│   ├── master.key
│   ├── hudson.util.Secret
│   └── credentials.xml
│
├── 📁 users/                              # User accounts & permissions
│   ├── admin/
│   │   ├── config.xml
│   │   └── builds/
│   └── developer/
│       └── config.xml
│
├── 📁 nodes/                              # Agent node configurations
│   ├── master/
│   ├── docker-agent/
│   └── kubernetes-agent/
│
├── 📁 workspace/                          # Shared workspace for jobs
│   ├── my-pipeline/
│   ├── my-pipeline@2/                     # For parallel runs
│   └── another-job/
│
├── 📁 fingerprints/                       # Artifact tracking
│
├── 📁 userContent/                        # Custom HTML/static files
│   ├── dashboard.html
│   ├── style.css
│   └── images/
│
├── 📁 logs/                               # Jenkins system logs
│   └── jenkins.log
│
└── 📁 updates/                            # Plugin update metadata

```

---

## Directory Details & Use Cases

### 1️⃣ **config.xml** - Global Jenkins Configuration
**Location:** `$JENKINS_HOME/config.xml`

**What it contains:**
- Jenkins system message
- Security realm configuration
- Email notification settings
- Number of executors
- Quiet period settings
- System authentication

**Use Cases:**
- Change security settings
- Update email configuration
- Modify quiet period (delay before builds)
- Backup before major changes

**Example:**
```xml
<hudson>
  <version>2.387.1</version>
  <numExecutors>4</numExecutors>
  <description>My Jenkins Server</description>
  <quietPeriod>5</quietPeriod>
  <securityRealm class="hudson.security.LocalSecurityRealm"/>
</hudson>
```

---

### 2️⃣ **jobs/** - Job Definitions & Build History
**Location:** `$JENKINS_HOME/jobs/`

**What it contains:**
- All Jenkins jobs (Freestyle, Pipeline, etc.)
- Each job's configuration
- Complete build history

**Subfolder Structure:**
```
jobs/
├── my-pipeline/
│   ├── config.xml                    # Job pipeline script
│   ├── builds/
│   │   ├── 1/
│   │   │   ├── build.xml             # Build metadata
│   │   │   ├── log                   # Console output
│   │   │   └── archive/              # Artifacts
│   │   ├── 2/
│   │   └── lastSuccessfulBuild →     # Symlink
│   └── workspace/                    # Job working directory
```

**Use Cases:**
- **Backup specific job:** `tar czf my-job-backup.tar.gz jobs/my-pipeline/`
- **Copy job to another Jenkins:** Copy `my-pipeline/` folder
- **Analyze build logs:** Check `builds/123/log`
- **Restore old build:** Copy `builds/oldid/` back

**Example - Check build log:**
```bash
cat $JENKINS_HOME/jobs/my-pipeline/builds/5/log
```

---

### 3️⃣ **plugins/** - Installed Plugins
**Location:** `$JENKINS_HOME/plugins/`

**What it contains:**
- All installed Jenkins plugins (.jpi files)
- Plugin update site data

**Use Cases:**
- **List installed plugins:**
  ```bash
  ls -la $JENKINS_HOME/plugins/ | grep .jpi
  ```
- **Remove plugin:**
  ```bash
  rm $JENKINS_HOME/plugins/unwanted-plugin.jpi
  rm -rf $JENKINS_HOME/plugins/unwanted-plugin/
  ```
- **Backup plugins:** `cp -r $JENKINS_HOME/plugins/ plugins-backup/`

**Common Plugins:**
| Plugin | Purpose |
|--------|---------|
| `git.jpi` | Git SCM integration |
| `docker.jpi` | Docker container builds |
| `kubernetes.jpi` | Kubernetes cluster support |
| `pipeline-model-definition.jpi` | Declarative pipelines |
| `slack.jpi` | Slack notifications |

---

### 4️⃣ **secrets/** - Credentials & Encryption Keys
**Location:** `$JENKINS_HOME/secrets/`

**What it contains:**
- Master encryption key
- Encrypted credentials
- API tokens

**⚠️ IMPORTANT:** Never share this folder!

**Use Cases:**
- **Backup for migration:** Always backup `secrets/` folder
- **Rotate credentials:** Jenkins uses this for encryption/decryption
- **Troubleshoot auth issues:** Check permissions here

**Example - Backup secrets securely:**
```bash
tar czf secrets-backup.tar.gz $JENKINS_HOME/secrets/
chmod 600 secrets-backup.tar.gz
```

---

### 5️⃣ **users/** - User Accounts & Permissions
**Location:** `$JENKINS_HOME/users/`

**What it contains:**
- Individual user configurations
- User build history
- API tokens

**Structure:**
```
users/
├── admin/
│   ├── config.xml                # Admin user settings
│   └── builds/
├── developer/
│   ├── config.xml                # Developer user settings
│   └── builds/
└── ci-user/
    └── config.xml
```

**Use Cases:**
- **Add user manually:** Create folder `users/newuser/` with `config.xml`
- **Reset user:** Delete user folder and recreate
- **Check user permissions:** View `config.xml` in user folder

---

### 6️⃣ **nodes/** - Agent Configuration
**Location:** `$JENKINS_HOME/nodes/`

**What it contains:**
- Master/agent node definitions
- Connection settings for remote agents
- Node resource configurations

**Structure:**
```
nodes/
├── master/                           # Built-in master
│   └── config.xml
├── docker-agent-01/
│   └── config.xml
├── kubernetes-agent/
│   └── config.xml
└── windows-builder/
    └── config.xml
```

**Use Cases:**
- **Scale builds:** Add new agent nodes
- **Specialized tasks:** Separate Windows/Linux agents
- **Docker agents:** Container-based distributed builds
- **Kubernetes agents:** Cloud-native scaling

**Example - Add a new agent:**
```bash
mkdir $JENKINS_HOME/nodes/my-agent/
# Create config.xml with agent settings
```

---

### 7️⃣ **workspace/** - Build Working Directories
**Location:** `$JENKINS_HOME/workspace/`

**What it contains:**
- Repository checkouts
- Intermediate build files
- Temporary build data

**Structure:**
```
workspace/
├── my-pipeline/                      # Main job workspace
├── my-pipeline@2/                    # Parallel execution workspace
├── another-job/
└── feature-branch-job/
```

**⚠️ WARNING:** Do NOT manually edit workspace files during builds!

**Use Cases:**
- **Debug build issues:** Check out files with `git log`, `cat pom.xml`
- **Clean workspace:** Jenkins does this automatically, but you can delete old folders
- **Check disk usage:** `du -sh workspace/`

**Example - Clean old workspaces:**
```bash
find $JENKINS_HOME/workspace/ -maxdepth 1 -type d -mtime +30 -exec rm -rf {} \;
```

---

### 8️⃣ **fingerprints/** - Artifact Tracking
**Location:** `$JENKINS_HOME/fingerprints/`

**What it contains:**
- Hashes of build artifacts
- Tracks artifact usage across jobs

**Use Cases:**
- **Dependency auditing:** "Which jobs used this JAR?"
- **Traceability:** Track artifact from build to deployment
- **Impact analysis:** See what breaks if artifact changes

**Example - View fingerprints:**
- Jenkins UI → Job → Build → "View Fingerprints"

---

### 9️⃣ **userContent/** - Custom Static Files
**Location:** `$JENKINS_HOME/userContent/`

**What it contains:**
- Custom HTML pages
- CSS stylesheets
- Images and static assets
- Custom dashboards

**Use Cases:**
- **Custom UI:** Create `dashboard.html` and access via `http://jenkins.com/userContent/dashboard.html`
- **Documentation:** Host internal wikis
- **Status pages:** Custom build status displays

**Example - Add custom dashboard:**
```bash
# Create custom HTML
cat > $JENKINS_HOME/userContent/status.html << 'EOF'
<!DOCTYPE html>
<html>
<head><title>Build Status</title></head>
<body>
  <h1>My Project Status</h1>
  <p>Access via: http://jenkins.com/userContent/status.html</p>
</body>
</html>
EOF
```

---

### 🔟 **logs/** - System Logs
**Location:** `$JENKINS_HOME/logs/`

**What it contains:**
- Jenkins system logs
- Debug information
- Error messages

**Use Cases:**
- **Troubleshooting:** Check `jenkins.log` for errors
- **Performance analysis:** Review startup/shutdown times
- **Security audit:** Check for failed login attempts

**Example - Monitor logs in real-time:**
```bash
tail -f $JENKINS_HOME/logs/jenkins.log
```

---

## Practical Examples

### Example 1: Backup Jenkins Instance
```bash
#!/bin/bash

BACKUP_DIR="/backups/jenkins"
BACKUP_DATE=$(date +%Y%m%d_%H%M%S)

# Stop Jenkins
sudo systemctl stop jenkins

# Create backup
tar czf $BACKUP_DIR/jenkins_backup_$BACKUP_DATE.tar.gz $JENKINS_HOME/

# Start Jenkins
sudo systemctl start jenkins

echo "✅ Backup created: jenkins_backup_$BACKUP_DATE.tar.gz"
```

### Example 2: List All Jobs with Status
```bash
#!/bin/bash

echo "📋 Jenkins Jobs Status:"
for job in $JENKINS_HOME/jobs/*/; do
  JOB_NAME=$(basename "$job")
  CONFIG_FILE="$job/config.xml"
  
  if [ -f "$CONFIG_FILE" ]; then
    echo "✓ Job: $JOB_NAME"
    cat "$CONFIG_FILE" | grep -o "<description>.*</description>"
  fi
done
```

### Example 3: Monitor Disk Usage
```bash
#!/bin/bash

echo "💾 Jenkins Directory Sizes:"
echo "Jobs:       $(du -sh $JENKINS_HOME/jobs/ 2>/dev/null)"
echo "Workspace:  $(du -sh $JENKINS_HOME/workspace/ 2>/dev/null)"
echo "Plugins:    $(du -sh $JENKINS_HOME/plugins/ 2>/dev/null)"
echo "Total:      $(du -sh $JENKINS_HOME/ 2>/dev/null)"
```

### Example 4: Clean Old Build Artifacts
```bash
#!/bin/bash

# Delete builds older than 30 days
find $JENKINS_HOME/jobs/*/builds/ -maxdepth 1 -type d -mtime +30 -exec rm -rf {} \;

echo "✅ Old builds cleaned"
```

---

## Best Practices

### ✅ DO's

| Practice | Reason |
|----------|--------|
| **Backup JENKINS_HOME regularly** | Full recovery in case of failure |
| **Version control job configs** | Track changes, rollback if needed |
| **Separate jobs and workspace** | Easy cleanup without losing configs |
| **Use agents for builds** | Distribute load, isolate environments |
| **Encrypt credentials** | Security best practice |
| **Monitor disk usage** | Prevent storage issues |
| **Document custom plugins** | Help team understand setup |

### ❌ DON'Ts

| Mistake | Problem |
|--------|---------|
| **Manually edit XML files during runs** | Corrupts data, build failures |
| **Share secrets/ folder** | Exposes credentials |
| **Delete workspace during builds** | Breaks running jobs |
| **Store large artifacts in jobs/** | Fills up disk space |
| **Run Jenkins as root** | Security vulnerability |
| **Skip regular backups** | Data loss risk |

---

## Quick Reference Commands

```bash
# Find JENKINS_HOME
echo $JENKINS_HOME

# Disk usage overview
du -sh $JENKINS_HOME/*

# List all jobs
ls $JENKINS_HOME/jobs/

# Check last 10 lines of jenkins log
tail -n 10 $JENKINS_HOME/logs/jenkins.log

# List installed plugins
ls $JENKINS_HOME/plugins/ | grep .jpi

# View recent build log
cat $JENKINS_HOME/jobs/[JOBNAME]/builds/lastSuccessfulBuild/log

# Backup current state
tar czf ~/jenkins-backup-$(date +%s).tar.gz $JENKINS_HOME/

# Find large directories
du -sh $JENKINS_HOME/* | sort -rh | head -5

# Clean workspace
rm -rf $JENKINS_HOME/workspace/*
```

---

