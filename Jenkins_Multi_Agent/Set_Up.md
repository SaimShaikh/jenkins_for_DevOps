
# Jenkins Multi-Agent Setup with SSH

## 📌 Requirements

### You Need
- **Two AWS EC2 Instances**
  - EC2 #1 → Jenkins Master
  - EC2 #2 → Jenkins Agent
- **OS:** Ubuntu 20.04+
- **Instance Type:** t2.medium+
- **Security Groups:**
  - Allow SSH (22)
  - Allow Jenkins (8080)
  - Allow internal communication within VPC
- **Prerequisites:**
  - Java 11+ installed on both
  - SSH access working
  - Private IP connectivity between instances

---

# 🔧 Step 1: Install Jenkins (ONLY on Master EC2)

### Connect to Master EC2:
```bash
ssh -i your-key.pem ubuntu@master-ec2-ip
```

### Install Java:
```bash
sudo apt update
sudo apt install openjdk-11-jdk -y
```

### Install Jenkins:
```bash
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null

echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null

sudo apt update
sudo apt install jenkins -y
sudo systemctl start jenkins
sudo systemctl enable jenkins
```

### Access Jenkins:
```
http://master-ec2-ip:8080
```

---

# 🔧 Step 2: Prepare Agent EC2 (NO Jenkins needed)

### Connect to Agent EC2:

### Install Java:
```bash
sudo apt update
sudo apt install openjdk-11-jdk -y
```

---

# 🔐 Step 3: SSH Key Setup (Master → Agent)

## Generate SSH Keys on Master
```bash
cd ~/.ssh
ssh-keygen 
```

Press **Enter** 3 times for defaults.

## Copy Public Key
```bash
cat ~/.ssh/id_ed25519.pub
```
Copy the full key.

## Paste Key into Agent Node (ec2)
```bash
cd ./ssh
ls
vim authorized_keys 
```

Paste the public key, then:

---

# 🎯 Step 4: Configure Jenkins Master

Open Jenkins UI:
```
http://master-ec2-ip:8080
```

## Add SSH Credentials:
**Manage Jenkins → Manage Credentials → Global → Add Credentials**

| Field | Value |
|-------|--------|
| Kind | SSH Username with private key |
| Scope | Global |
| ID | jenkins-agent-key |
| Username | Linux Username  |
| Private key | Paste id_ed25519 (private key) |
| Passphrase | leave empty |

---

# 🎯 Step 5: Create Jenkins Agent Node

**Manage Jenkins → Nodes → New Node**

### Fill:
| Field | Value |
|-------|--------|
| Node Name | <ANY NAME> |
| Type | Permanent Agent |
| # of Executors | 2 |
| Remote Directory | /home/ubuntu |
| Labels | <ANY NAME> |
| Usage | Use as much as possible |
| Launch Method | Launch via SSH |
| Host | agent Public  IP |
| Credentials | jenkins-agent-key |
| Host Key Strategy | Non-verifying |

Click **Save**.

---

# 🔥 Step 6: Test Agent

Create Freestyle job → Restrict to agent → Add shell script:

```bash
echo "Running on $(hostname)"
whoami
pwd
```

Run build.

You should see:

```
Building remotely on agent-node-1
```

✔️ Success.

---

# 🧯 Troubleshooting

### Agent Offline?
```bash
ssh jenkins@agent-private-ip
```
If fails → fix key/permissions.

### Permission issues?
```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

### Workspace missing?
```bash
sudo su - jenkins
mkdir -p /home/jenkins/jenkins-agent
```

---

# ✅ DONE — Your Jenkins Multi-Agent Setup Is Fully Working! 🚀

