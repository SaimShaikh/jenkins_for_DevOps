# 👥 Jenkins Role-Based User Management Explained 

---

## Real-World Scenario First

### The Problem: Before RBAC

**Company Situation:**
- 30 people using Jenkins
- 5 different teams (Developers, QA, DevOps, Support, Management)
- Only 2 options: Full access or no access

**What Happens:**
```
- ❌ Junior Dev gets full Jenkins access → Accidentally deletes production pipeline
- ❌ QA team member gets full access → Configures job, breaks build for all teams
- ❌ Offshore contractor gets full access → Sees sensitive credentials
- ❌ Support team gets no access → Can't even view build logs
- ❌ Manager has full admin access → Accidentally changes critical settings
```

### Solution: With RBAC

**Same Company, Now with Roles:**
```
- ✅ Junior Dev Role: Can BUILD and VIEW dev/test jobs only. Cannot DELETE.
- ✅ QA Role: Can BUILD and CONFIGURE QA jobs. Cannot access PROD jobs.
- ✅ Offshore Contractor: Can only VIEW and BUILD approved jobs. Cannot configure.
- ✅ Support Role: Can VIEW build logs and TRIGGER reruns. Cannot configure.
- ✅ Manager Role: Can VIEW all jobs but NOT configure. Cannot delete.
- ✅ Admin Role: Full access to everything (only 1-2 people need this).

Result:
- Everyone has exactly what they need
- Nobody has more access than required
- Security improved
- Fewer accidents
- Better compliance
```

---

## What is Role-Based Access Control?

**RBAC (Role-Based Access Control)** is a way to control **who can do what** in Jenkins.

Think of it like a company:

```
🏢 Company Structure:
├─ CEO → Can do everything
├─ Manager → Can manage team, not fire people
├─ Developer → Can write code, not access payroll
└─ Intern → Can read documents, limited actions

Jenkins with RBAC:
├─ Admin → Full access
├─ Senior Developer → Can configure jobs
├─ Junior Developer → Can trigger builds
├─ QA → Can only access QA jobs
└─ Support → Can view logs only
```

**Simple Definition:** RBAC is a security system where you create **roles** (like Developer, QA, Admin), give each role **permissions** (like Build, Configure, Delete), and assign **users** to roles. Each user then has exactly the permissions they need.

---

## Why Do We Need RBAC?

### Problems Without RBAC:

- ❌ **Security Risk** - Anyone with access can delete pipelines or steal credentials
- ❌ **No Accountability** - Can't track who changed what
- ❌ **Mistakes Happen** - Junior dev accidentally breaks production
- ❌ **Compliance Issues** - Can't prove access control for audits
- ❌ **Team Silos** - Can't safely give contractors/offshore teams access
- ❌ **Scalability Problem** - 100 people? Everyone has admin or nobody?

### Real Impacts:

```
Without RBAC:
- 1 mistake = whole Jenkins down
- Credentials exposed = all projects compromised
- Compliance fails = company gets fined
- Support can't help because they have no access

With RBAC:
- 1 mistake only affects that role's jobs
- Credentials protected by role limits
- Compliance passes = audit trail clear
- Support has just right access to help
```

---

## When to Use RBAC?

| Situation | Use RBAC? |
|-----------|----------|
| **Small team (2-3 people)** | NO - Overkill |
| **Everyone is admin** | NO - Same as overkill |
| **Multiple teams using same Jenkins** | YES - Essential |
| **Contractors/offshore teams** | YES - Mandatory |
| **Production pipelines** | YES - Critical |
| **Compliance requirements (GDPR, HIPAA)** | YES - Required |
| **Growing company** | YES - Plan early |
| **Sensitive projects** | YES - Required |
| **Every enterprise** | YES - Always |

---

## Benefits of RBAC

### 🔒 Security
- Each person only accesses what they need
- Limits damage from mistakes
- Prevents unauthorized access
- Protects credentials

**Example:** Contractor can build job but can't see production credentials

---

### 📋 Compliance
- Clear audit trail of who did what
- Meets regulatory requirements
- Easily prove access control
- Generate compliance reports

**Example:** Auditor asks "Who configured this pipeline?" → RBAC logs show exactly who and when

---

### 👥 Team Flexibility
- Different teams, different access
- Offshore teams safe to add
- Contractors limited to specific jobs
- Departments isolated from each other

**Example:** DevOps team can't delete QA jobs. QA can't access prod pipelines.

---

### ⚡ Reduced Mistakes
- Junior devs can't delete critical jobs
- Accidental changes limited to their role
- Easy rollback when mistakes happen

**Example:** Junior dev clicks "Delete Pipeline" → Permission denied (saved!)

---

### 📊 Scalability
- Add new teams easily
- Define roles once, reuse forever
- Onboard contractors safely
- Enterprise-ready

**Example:** 100 new developers? Just add them to "Developer" role. Done.

---

## How RBAC Works

### The RBAC Flow

```
1. CREATE USERS
   └─ Add people to Jenkins
   └─ Example: john, sarah, raj, priya

2. CREATE ROLES
   └─ Define what each role can do
   └─ Example: Developer, QA, Admin, Support

3. ASSIGN PERMISSIONS TO ROLES
   └─ Developer can: Build, View
   └─ QA can: Build, View, Configure
   └─ Admin can: Everything
   └─ Support can: View only

4. ASSIGN USERS TO ROLES
   └─ john → Developer role
   └─ sarah → QA role
   └─ raj → Admin role
   └─ priya → Support role

5. RESULT
   └─ john can Build and View only
   └─ sarah can Build, View, Configure QA jobs
   └─ raj can do everything
   └─ priya can View only
```

### Understanding the Concepts

**User:** A person (john@company.com)

**Role:** A job title with permissions (Developer, QA, Admin)

**Permission:** What you can do (Build, View, Configure, Delete)

**Assignment:** Linking user to role (john = Developer role)

---

## Types of Roles

### 1. Global Roles
**Scope:** Applies to whole Jenkins instance

**Examples:**
- **Admin** - Full access to everything
- **Developer** - Can build and view all jobs
- **Viewer** - Can only view, no building
- **Read-Only** - Can view Jenkins dashboard, nothing else

**Example Permissions:**
```
Admin Global Role:
✅ Can manage Jenkins settings
✅ Can create/delete jobs
✅ Can manage users
✅ Can configure security

Developer Global Role:
✅ Can view all jobs
✅ Can build all jobs
❌ Cannot create/delete jobs
❌ Cannot manage users
```

---

### 2. Item Roles (Job-Specific Roles)
**Scope:** Applies to specific jobs or job patterns

**Examples:**
- **QA Team** - Can access jobs matching `QA-*` pattern
- **Prod Admins** - Can access jobs matching `*-prod` pattern
- **Dev Support** - Can access jobs in `Development` folder

**Example Permissions:**
```
QA Item Role (applies to jobs like: QA-Auth, QA-Payment, QA-Order):
✅ Can view these jobs
✅ Can build these jobs
✅ Can configure these jobs
❌ Cannot delete these jobs
❌ Cannot access non-QA jobs
```

**How Patterns Work:**

| Pattern | Matches | Doesn't Match |
|---------|---------|---------------|
| `dev-*` | dev-api, dev-web, dev-db | prod-api, test-web |
| `*-prod` | api-prod, web-prod, db-prod | api-dev, prod-api |
| `QA.*` | QA.Auth, QA.Payment, QA.Dashboard | Auth.QA, payment-QA |
| `.*-test` | api-test, web-test, db-test | test-api, testing-web |

---

## Step-by-Step: How to Set Up RBAC

### ⚠️ IMPORTANT: Master Node Only

**All RBAC steps are done on Jenkins MASTER (CONTROLLER) node, NOT on agent nodes.**

```
Master (Controller):
└─ Web UI (where you configure RBAC)
   └─ Users are managed here
   └─ Roles are created here
   └─ Permissions are set here

Agent Nodes:
└─ Execute jobs/builds
└─ Cannot access this configuration
└─ Don't need RBAC setup
```

---

### Step 1: Install Role-Based Authorization Strategy Plugin

**Location:** Jenkins Master (Controller) Web UI

```
1. Go to Jenkins Dashboard (on Master)
2. Click "Manage Jenkins"
3. Click "Manage Plugins"
4. Search for "Role-based Authorization Strategy"
5. Click "Install"
6. Restart Jenkins (Master will restart, agents unaffected)
```

---

### Step 2: Enable RBAC in Security

**Location:** Jenkins Master (Controller) Web UI

```
1. Go to Jenkins Dashboard (on Master)
2. Click "Manage Jenkins"
3. Click "Security" (or "Configure Global Security")
4. Under "Authorization", select "Role-Based Strategy"
5. Click "Save"
6. New option appears: "Manage and Assign Roles"
```

---

### Step 3: Create Users

**Location:** Jenkins Master (Controller) Web UI

```
1. Go to Jenkins Dashboard (on Master)
2. Click "Manage Jenkins"
3. Click "Manage Users"
4. Click "Create User"
5. Fill in:
   - Username: john
   - Password: (strong password)
   - Full name: John Developer
   - Email: john@company.com
6. Click "Create User"
7. Repeat for other users
```

**Create Users:**
- john (Developer)
- sarah (QA Lead)
- raj (DevOps/Admin)
- priya (Support)

---

### Step 4: Create Roles and Assign Permissions (Global Roles)

**Location:** Jenkins Master (Controller) Web UI

```
1. Go to Jenkins Dashboard (on Master)
2. Click "Manage Jenkins"
3. Click "Manage and Assign Roles"
4. Click "Manage Roles"
5. Under "Global Roles", type role name and click "Add"
```

**Create These Global Roles:**

#### Role 1: Admin
```
Location: Master Web UI → Global Roles section
- Check ALL permissions (or at minimum: Overall/Administer)
```

#### Role 2: Developer
```
Location: Master Web UI → Global Roles section
- Overall/Read ✅
- Job/View ✅
- Job/Build ✅
- Job/Cancel ✅
- Job/Configure ✅
- Job/Discover ✅
```

#### Role 3: QA
```
Location: Master Web UI → Global Roles section
- Overall/Read ✅
- Job/View ✅
- Job/Build ✅
- Job/Configure ✅
(No Delete permission)
```

#### Role 4: Support
```
Location: Master Web UI → Global Roles section
- Overall/Read ✅
- Job/View ✅
- Job/Build ✅
- Job/Cancel ✅
(No Configure or Delete)
```

#### Role 5: Viewer
```
Location: Master Web UI → Global Roles section
- Overall/Read ✅
- Job/View ✅
- Job/Discover ✅
(No Build, Configure, Delete)
```

---

### Step 5: Create Item Roles (Job-Specific) - Optional

**Location:** Jenkins Master (Controller) Web UI

**Use Case:** Limit QA team to only QA jobs

```
1. Go to Jenkins Dashboard (on Master)
2. Click "Manage Jenkins"
3. Click "Manage and Assign Roles"
4. Click "Manage Roles"
5. Scroll down to "Item Roles" section
6. Create item role:
   - Role name: qa-team
   - Pattern: QA.*
   - Permissions: Job/View ✅ Job/Build ✅
7. Click "Add"

Note: Pattern "QA.*" matches: QA.Auth, QA.Payment, QA.Dashboard
```

---

### Step 6: Assign Roles to Users

**Location:** Jenkins Master (Controller) Web UI

```
1. Go to Jenkins Dashboard (on Master)
2. Click "Manage Jenkins"
3. Click "Manage and Assign Roles"
4. Click "Assign Roles"
5. Under "Global Roles", assign users:
   - john → Developer
   - sarah → QA
   - raj → Admin
   - priya → Support

6. Under "Item Roles" (if created):
   - sarah → qa-team (restricts to QA.* jobs only)

7. Click "Save"
```

---

### Step 7: Test Everything

**Location:** Jenkins Master (Controller) Web UI

```
Test as john (Developer):
1. Log out (or use private browsing)
2. Log in as john at: https://jenkins-master:8080
3. Should see Jenkins dashboard ✅
4. Should see all jobs ✅
5. Should be able to build ✅
6. Should be able to configure ✅
7. Should NOT be able to delete ✅

Test as priya (Support):
1. Log out
2. Log in as priya at: https://jenkins-master:8080
3. Should see Jenkins dashboard ✅
4. Should see all jobs ✅
5. Should NOT be able to build ❌ (greyed out)
6. Should NOT be able to configure ❌ (greyed out)
7. Should NOT see delete option ❌
```

---

## Master vs Agent Node

### Quick Reference Table

| Component | Master (Controller) | Agent Nodes |
|-----------|------------------|------------|
| **RBAC Configuration** | ✅ Configured here | ❌ Not involved |
| **User Creation** | ✅ Configured here | ❌ Not involved |
| **Role Management** | ✅ Configured here | ❌ Not involved |
| **Permission Assignment** | ✅ Configured here | ❌ Not involved |
| **Plugin Installation** | ✅ Installed here | ❌ Not needed |
| **Build Execution** | ❌ Usually not | ✅ Runs builds |
| **Workspace** | ❌ Optional | ✅ Has workspace |
| **Tools (Maven, Docker)** | ❌ Usually not | ✅ Has tools |

### Simple Rule

```
🎮 Master = Control Center
   └─ Where you configure RBAC
   └─ Where users log in
   └─ Where permissions are managed

🔧 Agents = Workers
   └─ Execute jobs
   └─ Run builds
   └─ Don't care about RBAC
```

### What If Agent Node is Down?

```
If Agent Node is Down:
- ❌ Builds waiting for that agent cannot run
- ✅ RBAC still works fine
- ✅ Users can still log in
- ✅ Users can still configure jobs

If Master Node is Down:
- ❌ Nobody can log in (RBAC doesn't work)
- ❌ Nobody can access Jenkins at all
- ❌ No builds can be triggered
```

---

## Common Permissions Explained

### Permission Decision Matrix

Use this table to decide who gets what:

| Permission | Junior Dev | Senior Dev | QA | DevOps | Support | Manager | Admin |
|-----------|-----------|-----------|-----|--------|---------|---------|-------|
| **Overall/Read** | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| **Job/View** | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| **Job/Build** | ✅ | ✅ | ✅ | ✅ | ✅ | ❌ | ✅ |
| **Job/Cancel** | ✅ | ✅ | ✅ | ✅ | ✅ | ❌ | ✅ |
| **Job/Configure** | ❌ | ✅ | ✅ | ✅ | ❌ | ❌ | ✅ |
| **Job/Delete** | ❌ | ❌ | ❌ | ✅ | ❌ | ❌ | ✅ |
| **Job/Create** | ❌ | ✅ | ❌ | ✅ | ❌ | ❌ | ✅ |
| **Overall/Manage** | ❌ | ❌ | ❌ | ✅ | ❌ | ❌ | ✅ |
| **Overall/Administer** | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ |

---

### Overall Permissions (Apply to Whole Jenkins)
```
✅ Read - View Jenkins dashboard and available jobs
✅ Manage - Access Jenkins configuration and settings
✅ Run Hooks - Execute web hooks
✅ Cancel - Cancel running builds
✅ Create - Create new jobs
✅ Delete - Delete jobs
✅ Administer - Full admin access
```

### Job Permissions (Apply to Specific Jobs)
```
✅ View - See the job and its configuration
✅ Build - Click "Build Now" button
✅ Cancel - Stop a running build
✅ Configure - Edit the job settings
✅ Delete - Remove the job
✅ Move - Move job to folder
✅ Discover - Find job through API
✅ Read - View job
✅ Extend - Run job with parameters
```

### Common Permission Combos:

**Viewer Role:**
- Job/View ✅
- Job/Discover ✅
- Everything else ❌

**Developer Role:**
- Job/View ✅
- Job/Build ✅
- Job/Cancel ✅
- Job/Discover ✅
- Job/Configure ❌
- Job/Delete ❌

**QA Lead Role:**
- Job/View ✅
- Job/Build ✅
- Job/Configure ✅
- Job/Delete ❌
- Job/Cancel ✅

**Admin Role:**
- All permissions ✅

---

## Best Practices

### ✅ DO

1. **Start with Least Privilege**
   - Give minimum permissions needed
   - Add more only if requested
   - Not "full access" and remove later

2. **Create Clear Roles**
   - Developer, QA, DevOps, Support, Admin
   - Names should be obvious
   - Match your company structure

3. **Use Item Roles for Isolation**
   - Dev team only sees `dev-*` jobs
   - QA team only sees `qa-*` jobs
   - Prevents accidental interference

4. **Regular Audits**
   - Review roles every quarter
   - Remove unused roles
   - Update roles when teams change
   - Remove people who left company

5. **Use Groups (if available)**
   - Group = multiple users with same role
   - Easier than assigning individually
   - Change group = affects all members

6. **Document Your Roles**
   - Keep list of what each role can do
   - Share with team
   - Update when roles change

7. **Remember: Master Node Only**
   - All RBAC configuration on Master
   - Agents don't need setup
   - Backup Master regularly

### ❌ DON'T

1. **Don't Give Everyone Admin Access**
   - Too risky
   - No accountability
   - Compliance failure
   - One mistake = everything broken

2. **Don't Mix Concerns**
   - Developer shouldn't be Admin
   - QA shouldn't manage infrastructure jobs
   - Support shouldn't configure pipelines

3. **Don't Give Contractors Full Access**
   - Limit to specific jobs only
   - Use Item Roles with patterns
   - Time-limit their access if possible

4. **Don't Create Too Many Roles**
   - 5-10 roles is typical
   - More than 20 = too complex
   - Becomes maintenance nightmare

5. **Don't Forget Offshore/Contractors**
   - Give them minimal permissions
   - Use Item Roles to restrict
   - Hide sensitive information
   - Regular access reviews

6. **Don't Configure RBAC on Agent Nodes**
   - All RBAC is on Master only
   - Agent nodes just execute builds
   - No permissions to manage on agents

---

## Real Scenario Examples

### Example 1: Typical Startup (20 People)

```
Create Roles (on Master):
- Admin (2 people: CTO, DevOps Lead)
- Developer (8 people: Engineering team)
- QA (3 people: Testing team)
- Support (2 people: DevOps support)
- Viewer (5 people: Manager, interns, contractors)

Developer Role Permissions:
✅ View, Build, Configure dev/test jobs
❌ Cannot delete
❌ Cannot access prod jobs

Result: Everyone has clear boundaries
```

---

### Example 2: Enterprise (200 People)

```
Create Roles (on Master):
- Admin (3 people: Platform team)
- TeamLead (10 people: Tech leads)
- Developer (80 people)
- QA (40 people)
- DevOps (20 people: Infrastructure)
- Support (10 people)
- Contractor (20 people: External)
- Viewer (17 people: Management, analytics)

Use Item Roles (on Master):
- Developers: dev.* jobs only
- QA: qa.* jobs only
- DevOps: infrastructure.* and prod.* jobs
- Contractor: contractor.* jobs only

Result: Scalable, secure, compliant
```

---

### Example 3: Restricted Prod Access

```
Scenario: Junior dev shouldn't touch production

Solution (Configure on Master):
Global Role: Developer
- Overall/Read ✅
- Job/View ✅
- Job/Build ✅
- Job/Configure ✅

Item Role: ProdOnly (pattern: *-prod)
- Permissions: Nothing

Result: 
- Developer can build/configure dev and test jobs
- Production jobs appear greyed out
- Cannot accidentally break production
```

---

## Quick Reference

### Quick Permission Guide

```
Viewer (Read-only):
→ Can see what's happening
→ Cannot do anything
→ Good for: Managers, stakeholders

Developer:
→ Can view and build jobs
→ Can configure jobs
→ Cannot delete jobs
→ Good for: Engineers

QA Lead:
→ Can do everything Developer can
→ Can configure QA jobs
→ Cannot delete jobs
→ Good for: QA leads

DevOps/Admin:
→ Can do everything
→ Can delete jobs
→ Can manage users and security
→ Good for: DevOps engineers, platform team
```

---

### Troubleshooting

**Problem:** User can't see jobs
```
Solution: 
1. Check Overall/Read permission
2. Make sure user assigned to role with Overall/Read
3. Verify on Master web UI
```

**Problem:** User can't build
```
Solution: 
1. Check Job/Build permission
2. Make sure role has Job/Build
3. Make sure user assigned to role
4. Verify on Master web UI
```

**Problem:** User sees all jobs but should only see QA jobs
```
Solution: 
1. Use Item Roles with pattern
2. Create item role with pattern: qa-*
3. Assign user to that item role
4. Verify on Master web UI
```

---


