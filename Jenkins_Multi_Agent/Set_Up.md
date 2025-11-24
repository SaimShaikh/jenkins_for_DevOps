
# Jenkins Multi-Agent Setup with SSH 

---

## 📋 Requirements

### What You Need

✅ **2 AWS EC2 Instances**:
- **EC2 Instance 1**: For Jenkins Master Node
- **EC2 Instance 2**: For Jenkins Agent Node

✅ **EC2 Instance Specifications**:
- **OS**: Ubuntu 20.04 or higher (recommended)
- **Instance Type**: t2.medium or higher
- **Security Group**: Allow SSH (port 22) and Jenkins (port 8080) traffic

✅ **Access**:
- SSH access to both EC2 instances
- Security Group rules configured to allow communication between instances

✅ **Prerequisites**:
- Java 11 or higher installed on both instances
- Network connectivity between master and agent EC2 instances

---

## 🔧 Step 1: Install Jenkins on Both EC2 Instances

### On Master EC2 Instance

**1. Connect to Master EC2 via SSH**:
```bash
ssh -i your-key.pem ubuntu@master-ec2-ip
```

**2. Update System**:
```bash
sudo apt update
sudo apt upgrade -y
```

**3. Install Java**:
```bash
sudo apt install openjdk-11-jdk -y
java -version
```

**4. Install Jenkins**:
```bash
# Add Jenkins Repository
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null

echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null

# Install Jenkins
sudo apt-get update
sudo apt-get install jenkins -y

# Start Jenkins
sudo systemctl start jenkins
sudo systemctl enable jenkins

# Check Jenkins Status
sudo systemctl status jenkins
```

**5. Access Jenkins**:
```
Open browser and go to: http://master-ec2-ip:8080
```

---

### On Agent EC2 Instance

**1. Connect to Agent EC2 via SSH**:
```bash
ssh -i your-key.pem ubuntu@agent-ec2-ip
```

**2. Update System**:
```bash
sudo apt update
sudo apt upgrade -y
```

**3. Install Java**:
```bash
sudo apt install openjdk-11-jdk -y
java -version
```

**4. Create Jenkins User for Agent**:
```bash
# Create jenkins user
sudo useradd -m -s /bin/bash jenkins

# Set password for jenkins user
sudo passwd jenkins
```

**5. Create Agent Workspace Directory**:
```bash
# Switch to jenkins user
sudo su - jenkins

# Create workspace directory
mkdir -p /home/jenkins/jenkins-agent

# Exit jenkins user
exit
```

**Note**: Jenkins does NOT need to be installed on the agent machine. Only Java needs to be installed!

---

## 🔐 Step 2: SSH Key Generation and Configuration

### On Master EC2 Instance - Generate SSH Keys

**Step 1: Navigate to SSH Directory**:
```bash
cd ~/.ssh
```

**Step 2: Generate SSH Key Pair**:
```bash
ssh-keygen -t ed25519 -C "jenkins-agent-key"
```

**Step 3: When Prompted, Press Enter 2 Times**:
```
Enter file in which to save the key (/home/ubuntu/.ssh/id_ed25519): [Press Enter]
Enter passphrase (empty for no passphrase): [Press Enter]
Enter same passphrase again: [Press Enter]
```

**Why Press Enter?**
- Use default filename (`id_ed25519`)
- No passphrase (for automation to work without prompts)

**Step 4: Verify Keys Created**:
```bash
ls -la ~/.ssh/
```

**Expected Output**:
```
id_ed25519        (Private Key - KEEP SECRET!)
id_ed25519.pub    (Public Key - can be shared)
```

---

### On Master EC2 Instance - Copy Public Key

**Step 1: Display Public Key**:
```bash
cat ~/.ssh/id_ed25519.pub
```

**Step 2: Copy the Entire Output**:
The output will look like:
```
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAINy6V2M... jenkins-agent-key
```

**Copy everything from `ssh-ed25519` to the end.**

---

### On Agent EC2 Instance - Add Public Key

**Step 1: Connect to Agent EC2**:
```bash
ssh -i your-key.pem ubuntu@agent-ec2-ip
```

**Step 2: Switch to Jenkins User**:
```bash
sudo su - jenkins
```

**Step 3: Navigate to SSH Directory**:
```bash
cd ~/.ssh
```

**If `.ssh` directory doesn't exist**:
```bash
mkdir -p ~/.ssh
chmod 700 ~/.ssh
```

**Step 4: Create/Edit authorized_keys File**:
```bash
# Using nano editor
nano ~/.ssh/authorized_keys

# OR using vim
vim ~/.ssh/authorized_keys
```

**Step 5: Paste Master's Public Key**:
- Right-click to paste (in PuTTY/terminal)
- The public key from master should be one single line
- Paste it and save

**Step 6: Set Correct Permissions**:
```bash
# Fix permissions
chmod 600 ~/.ssh/authorized_keys
chmod 700 ~/.ssh

# Verify
ls -la ~/.ssh/
```

**Expected Permissions**:
```
drwx------ (700) for .ssh directory
-rw------- (600) for authorized_keys file
```

**Step 7: Exit Jenkins User**:
```bash
exit
```

---

### Verify SSH Connection Works

**On Master EC2 - Test SSH Connection**:
```bash
# Test connection to agent
ssh -i ~/.ssh/id_ed25519 jenkins@agent-ec2-private-ip

# If successful, you should be logged in as jenkins@agent
# Type 'exit' to return to master
exit
```

**If Successful Output**:
```
jenkins@agent-node:~$
```

**If Failed**: Check [Troubleshooting](#troubleshooting) section

---

## 🎯 Step 3: Configure Jenkins Master to Connect to Agent

### Part 1: Add SSH Credentials to Jenkins

**Step 1: Open Jenkins Dashboard**:
```
Go to: http://master-ec2-ip:8080
```

**Step 2: Navigate to Manage Jenkins**:
```
Click "Manage Jenkins" in left sidebar
```

**Step 3: Click "Manage Credentials"**:
```
Find and click "Manage Credentials"
```

**Step 4: Click "Global" Under Credentials**:
```
Under "Credentials" section, click "Global"
```

**Step 5: Click "Add Credentials" (Left Sidebar)**:
```
On the left, click "Add Credentials"
```

**Step 6: Get Private Key from Master**:

On your **Master EC2 machine**, open a terminal and get the private key:
```bash
cd ~/.ssh
cat id_ed25519
```

Copy the **entire** private key output (from `-----BEGIN` to `-----END`)

---

### Part 2: Fill in Credentials Form

**On Jenkins - Add Credentials Form**:

Fill in these fields:

| Field | Value |
|-------|-------|
| **Kind** | SSH Username with private key |
| **Scope** | Global |
| **ID** | `jenkins-agent-key` (any name you want) |
| **Description** | `SSH Key for Agent Connection` |
| **Username** | `jenkins` (the user on agent node) |
| **Private Key** | Paste the entire private key from master |
| **Passphrase** | Leave empty (we didn't set one) |

**Private Key Entry**:
- Select **"Enter directly"** option
- Click in the text area
- Paste the entire private key from master
- Make sure everything is included (from `-----BEGIN` to `-----END`)

**Click "Create"**

---

### Part 3: Create New Node in Jenkins

**Step 1: Go to Dashboard**:
```
Click "Jenkins" logo to go to dashboard
```

**Step 2: Click "Manage Jenkins"**:
```
Left sidebar → Manage Jenkins
```

**Step 3: Click "Nodes" or "Manage Nodes and Clouds"**:
```
Find and click this option
```

**Step 4: Click "New Node"**:
```
Left sidebar → New Node
```

**Step 5: Enter Node Name**:
```
Node name: agent-node-1 (or any name)
Select: "Permanent Agent"
Click: "Create"
```

---

### Part 4: Configure Node Settings

**Fill in the Configuration Form**:

| Field | Value |
|-------|-------|
| **# of executors** | 2 (or based on your need) |
| **Remote root directory** | `/home/jenkins/jenkins-agent` |
| **Labels** | `agent1` (any label you want) |
| **Usage** | "Use this node as much as possible" |
| **Launch method** | "Launch agents via SSH" |
| **Host** | `agent-ec2-private-ip` (or agent security group DNS) |
| **Credentials** | Select "jenkins-agent-key" (created above) |
| **Host Key Verification Strategy** | "Non verifying Verification Strategy" |
| **Availability** | "Keep this agent online as much as possible" |

---

### Detailed Field Explanations

#### # of executors
**What**: How many builds can run simultaneously on this agent
```
Example: 2 executors = 2 builds at same time
Usually = Number of CPU cores on the EC2 instance
```

#### Remote root directory
**What**: Workspace folder on agent where Jenkins stores builds
```
Must exist: /home/jenkins/jenkins-agent
Must be writable by jenkins user
```

#### Labels
**What**: Tags to identify this agent
```
Example: agent1, linux, build, docker
Use in pipeline: agent { label 'agent1' }
```

#### Launch method
**What**: How master connects to agent
```
"Launch agents via SSH" = Master connects to agent (recommended)
SSH is secure and master controls everything
```

#### Host
**What**: IP address or hostname of agent EC2
```
Example: 10.0.0.50 (private IP)
Or: agent-node.example.com (DNS)
Must be reachable from master EC2
```

#### Credentials
**What**: Authentication to SSH into agent
```
Select: jenkins-agent-key (the one you created)
This uses the SSH key pair for authentication
```

#### Host Key Verification Strategy
**What**: Verify the agent's SSH fingerprint
```
"Non verifying Verification Strategy" = Don't verify (OK for testing)
Production: Use "Manually trusted key"
```

---

## ✅ Step 4: Verify Connection

### Check Agent Status in Jenkins

**Step 1: Go to Nodes Page**:
```
Jenkins Dashboard → Manage Jenkins → Nodes
```

**Step 2: Look for Your Agent**:
```
You should see your agent listed (e.g., "agent-node-1")
```

**Step 3: Check Status**:
```
🟢 Green Circle = Online ✅
🟤 Brown Circle = Offline ❌
```

**Step 4: View Agent Details**:
```
Click on your agent name
```

**Step 5: Check Logs**:
```
Click "Logs" in left sidebar
Scroll down to see connection logs
```

---

### Test Build on Agent

**Step 1: Create Test Job**:
```
Jenkins Dashboard → New Item
Job name: test-agent-job
Select: Freestyle project
Click: OK
```

**Step 2: Configure Job**:
```
Under "General":
✓ Check "Restrict where this project can run"
Labels: agent1 (your agent label)

Add Build Step: Execute shell
```

**Step 3: Add Build Step Commands**:
```bash
#!/bin/bash
echo "=== Testing Agent Connection ==="
echo "Running on: $NODE_NAME"
echo "Hostname: $(hostname)"
echo "User: $(whoami)"
echo "Working Directory: $(pwd)"
echo "=== Test Complete ==="
```

**Step 4: Save and Build**:
```
Click: Save
Click: Build Now
```

**Step 5: Check Console Output**:
```
Click build number in Build History
Click: Console Output
```

**Expected Output**:
```
Started by user
Building remotely on agent1
=== Testing Agent Connection ===
Running on: agent-node-1
Hostname: ip-10-0-0-50
User: jenkins
Working Directory: /home/jenkins/jenkins-agent/workspace/test-agent-job
=== Test Complete ===
```

**Success Indicators**:
- ✅ "Building remotely on agent1" appears
- ✅ Shows agent node name
- ✅ Build completes without errors
- ✅ Log shows correct working directory

---

## 🔧 Troubleshooting

### Problem 1: Agent Shows Offline

**Symptoms**:
```
Agent status: 🟤 Brown Circle (Offline)
```

**Solution 1 - Check SSH Connection**:
```bash
# On Master EC2
ssh -i ~/.ssh/id_ed25519 jenkins@agent-ec2-private-ip

# If fails, check:
# 1. Agent EC2 security group allows SSH (port 22)
# 2. Private IP is correct
# 3. authorized_keys has correct permissions (600)
```

**Solution 2 - Check Jenkins Logs**:
```
In Jenkins: Click agent → Click "Logs"
Look for error messages
```

**Solution 3 - Verify SSH Key**:
```bash
# On Master
cat ~/.ssh/id_ed25519.pub

# On Agent - Check authorized_keys contains above
sudo su - jenkins
cat ~/.ssh/authorized_keys
```

---

### Problem 2: "Authentication Failed"

**Symptoms**:
```
Error: Failed to authenticate
```

**Causes & Solutions**:

**1. Wrong Private Key**:
- Verify you pasted the **private key** (id_ed25519), not public key
- Private key starts with: `-----BEGIN OPENSSH PRIVATE KEY-----`

**2. Permissions Wrong on Agent**:
```bash
# On Agent
sudo su - jenkins
chmod 600 ~/.ssh/authorized_keys
chmod 700 ~/.ssh
```

**3. Public Key Not in authorized_keys**:
```bash
# Verify on Agent
cat ~/.ssh/authorized_keys
# Should contain the master's public key (id_ed25519.pub)
```

---

### Problem 3: "Remote root directory does not exist"

**Symptoms**:
```
Error: Remote root directory /home/jenkins/jenkins-agent does not exist
```

**Solution**:
```bash
# On Agent EC2
sudo su - jenkins
mkdir -p /home/jenkins/jenkins-agent
ls -la /home/jenkins/
```

---

### Problem 4: Agent Disconnects Frequently

**Symptoms**:
```
Agent goes offline randomly
Build fails: "No executors available"
```

**Solutions**:

**1. Check Network Connectivity**:
```bash
# On Master
ping agent-ec2-private-ip
```

**2. Increase Connection Timeout**:
```
In Jenkins: Manage Jenkins → System Configuration
Look for Agent settings
Increase timeout value
```

**3. Check Agent Logs**:
```
Click agent → Logs
Look for connection timeout errors
```

---

### Problem 5: "Permission Denied" in Build

**Symptoms**:
```
Error: Permission denied (publickey)
```

**Solutions**:

**1. Verify Jenkins User Permissions**:
```bash
# On Agent
sudo su - jenkins
cd /home/jenkins/jenkins-agent
ls -la
# Files should have jenkins ownership
```

**2. Check Build Directory Permissions**:
```bash
# On Agent
ls -la /home/jenkins/jenkins-agent/workspace/
```

---

## 🎯 Quick Summary

### What We Did

```
Step 1: Generate SSH key pair on Master
        ↓
Step 2: Add Master's public key to Agent's authorized_keys
        ↓
Step 3: Add SSH credentials to Jenkins
        ↓
Step 4: Create new node in Jenkins with SSH configuration
        ↓
Step 5: Verify connection is working
        ↓
✅ Agent is now connected to Master!
```

### Configuration Checklist

**On Master EC2**:
- [ ] Java 11+ installed
- [ ] Jenkins installed and running
- [ ] SSH key pair generated (id_ed25519, id_ed25519.pub)
- [ ] SSH credentials added to Jenkins
- [ ] Node created in Jenkins with correct settings

**On Agent EC2**:
- [ ] Java 11+ installed
- [ ] Jenkins user created
- [ ] Master's public key in ~/.ssh/authorized_keys
- [ ] Correct permissions (600 on authorized_keys, 700 on .ssh)
- [ ] /home/jenkins/jenkins-agent directory exists

**Jenkins Configuration**:
- [ ] Credentials created (jenkins-agent-key)
- [ ] Node created (agent-node-1)
- [ ] SSH settings configured
- [ ] Agent shows Online (green circle)
- [ ] Test build successful

---

**Your Jenkins Master-Agent SSH setup is complete!** 🚀

---
**Congratulations! You now have a distributed Jenkins setup!** 🎊
