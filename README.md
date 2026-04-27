<div align="center">

# 🔐 DevSecOps: Zero to Production-Grade Security

### DevOps + Security Mindset = DevSecOps

> **"Security is not a phase. It is a culture embedded into every commit, every pipeline, every container, and every cluster."**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)
[![Security: Shift Left](https://img.shields.io/badge/Security-Shift%20Left-blue.svg)](#shift-left)

</div>

---

## 📖 What is This Guide?

This is a **complete, beginner-to-advanced DevSecOps reference** written so that even someone who has never touched a terminal can understand *why* security matters at every step of the software delivery lifecycle.

Whether you are a developer, a QA engineer, a cloud engineer, or a complete beginner by the end of this guide you will understand, because i shared my overall experince in DevSecOps:

- What DevSecOps is and why it exists
- How to secure Git, Infrastructure as Code, Containers, and Kubernetes
- How to build a CI/CD pipeline that catches vulnerabilities automatically
- What SAST, DAST, and SCA mean and how to use them

---

## 📚 Table of Contents

1. [The Big Picture What is DevSecOps?](#1-the-big-picture--what-is-devsecops)
2. [Shift Left vs Shield Right](#2-shift-left-vs-shield-right)
3. [Threat Modelling](#3-threat-modelling)
4. [DevSecOps for Git](#4-devsecops-for-git)
5. [IaC Security Terraform](#5-iac-security--terraform)
6. [Container Security Docker](#6-container-security--docker)
7. [Kubernetes Security](#7-kubernetes-security)
8. [CI/CD Pipeline Security](#8-cicd-pipeline-security)
9. [Application Security SAST, DAST, SCA](#9-application-security--sast-dast-sca)
10. [The Ultimate Secure Pipeline](#10-the-ultimate-secure-pipeline)

---

## 1. The Big Picture What is DevSecOps?

### The Old World (DevOps without Security)

Imagine a factory that builds cars. The factory is fast cars roll off the line every hour. But nobody checks the brakes until the car is already sold and on the road. When a brake fails, it is a disaster.

That is exactly what happens when security is an afterthought in software.

```
Developer writes code → Code is tested → Code is deployed → Security team scans → Vulnerabilities found → Fix → Redeploy
                                                                                         ↑
                                                                              (Too late. Already in production.)
```

### The DevSecOps World

DevSecOps embeds security checks at **every single stage** from the moment a developer types the first line of code.

```
Developer writes code
       ↓
  Pre-commit hook blocks secrets          ← Security at commit time
       ↓
  Pull Request triggers CI pipeline
       ↓
  SAST scans source code                  ← Security at build time
  SCA checks dependencies
  Container image scanned with Trivy
       ↓
  Staging deployment
       ↓
  DAST attacks the running app            ← Security at runtime
       ↓
  Production deployment
       ↓
  Runtime monitoring + alerts             ← Security in production
```

### The Scope of DevSecOps

| Area | What a DevSecOps Engineer Secures |
|------|----------------------------------|
| **Git / VCS** | No secrets in code, branch protection, RBAC |
| **IaC (Terraform)** | No hardcoded credentials, no misconfigured infra |
| **Scripting (Python/Bash)** | No vulnerable packages, no hardcoded passwords |
| **Containers (Docker)** | Non-root users, minimal images, no CVEs |
| **Kubernetes** | Private clusters, RBAC, network policies, secrets management |
| **CI/CD** | Automated SAST, DAST, SCA, image scanning in every pipeline |

### Why AI Makes DevSecOps More Important, Not Less

Today, AI writes roughly **80% of the code** in many organizations. Developers use GitHub Copilot, ChatGPT, and similar tools. This is great for speed but AI:

- Copies patterns from the internet, including **vulnerable code from Stack Overflow**
- Uses **outdated packages** with known CVEs
- Does not understand your organization's specific security requirements

A DevSecOps engineer is the safety net that catches what AI misses.

---

## 2. Shift Left vs Shield Right

This is the **most fundamental concept** in DevSecOps. You must understand this before everything else.

### The Timeline of Software Delivery

```
LEFT                                                                    RIGHT
 |                                                                        |
 ▼                                                                        ▼
Dev writes code → Build → Test → Stage → Deploy → Production → Monitor
```

- **Left** = where application development starts (developer's laptop)
- **Right** = where the application runs in production (users are using it)

### Shield Right (The Old Approach)

Shield Right means you add security **only on the right side** after the application is already deployed.

Examples of Shield Right tools:
- API Gateway with rate limiting
- WAF (Web Application Firewall) in front of the app
- Runtime security monitoring on the EKS cluster

**The problem:** By the time you find a vulnerability on the right, it has already been in your codebase for weeks or months. Fixing it is expensive, slow, and risky.

> 💡 **Rule of thumb:** A bug found at development costs $1 to fix. The same bug found in production costs $100 to fix.

### Shift Left (The DevSecOps Approach)

Shift Left means you move security checks as far **left** as possible ideally to the developer's machine before code is even committed.

```
🔒 Pre-commit hooks          → catches secrets before they leave the laptop
🔒 SAST in CI pipeline       → catches code vulnerabilities at build time
🔒 SCA in CI pipeline        → catches vulnerable dependencies at build time
🔒 Container scanning        → catches CVEs before the image is deployed
🔒 IaC scanning (Checkov)    → catches misconfigurations before infra is created
🔒 DAST in staging           → catches runtime vulnerabilities before production
```

### The Combined Approach (Best Practice)

You do not choose one or the other. You do **both**:

| Stage | Shift Left | Shield Right |
|-------|-----------|--------------|
| Code | Pre-commit hooks, SAST | — |
| Build | SCA, container scanning | — |
| Deploy | IaC scanning, Kyverno policies | API Gateway, WAF |
| Runtime | — | Network policies, monitoring, alerts |

---

## 3. Threat Modelling

Before writing a single line of code, a DevSecOps engineer asks: **"What could go wrong?"**

That is threat modelling a structured way to identify, analyze, and mitigate security risks **before they become real problems**.

### The 3-Step Process

```
Step 1: IDENTIFY        Step 2: ANALYZE         Step 3: MITIGATE
─────────────────       ─────────────────       ─────────────────
What assets do          How likely is           What controls can
we have?                this attack?            we put in place?
What can go wrong?      How severe?             How do we test them?
```

### The STRIDE Framework

STRIDE is the industry-standard framework for categorizing threats:

| Letter | Threat | Plain English | Example |
|--------|--------|---------------|---------|
| **S** | Spoofing | Pretending to be someone else | Attacker logs in as admin |
| **T** | Tampering | Modifying data without permission | Attacker changes order amount to $0 |
| **R** | Repudiation | Denying you did something | Developer denies deleting the database |
| **I** | Information Disclosure | Leaking sensitive data | API returns passwords in response |
| **D** | Denial of Service | Making the system unavailable | Flooding the server with requests |
| **E** | Elevation of Privilege | Gaining more access than allowed | Normal user accesses admin panel |

### Tool: OWASP Threat Dragon

OWASP Threat Dragon is a free, open-source tool that lets you **draw a diagram** of your application and automatically identify threats.

```
How it works:
1. Draw your system (web app → API → database → external services)
2. Mark trust boundaries (where data crosses security zones)
3. Threat Dragon automatically suggests STRIDE threats for each component
4. You document mitigations for each threat
```

### Real Example: 3-Tier Web Application

```
[Browser] HTTPS──► [Load Balancer] ──► [App Server] ──► [Database]
                            |                   |
                      Trust Boundary      Trust Boundary

Threats identified:
- Browser → LB:    Spoofing (fake requests), DoS (flood)
- LB → App:        Tampering (modified headers)
- App → DB:        SQL Injection, Information Disclosure
- App Server:      Elevation of Privilege (running as root)
```

---

## 4. DevSecOps for Git

Git is where everything starts. A developer's first action is pushing code to a repository. If secrets leak here, **everything downstream is compromised**.

### Why Git Security Matters

> Real incident: A developer accidentally commits an AWS access key to a public GitHub repo. Within **4 minutes**, automated bots find it and spin up 100 EC2 instances for crypto mining. The bill: $50,000.

This is not hypothetical. It happens every week.

---

### 4.1 `.gitignore` The First Line of Defence

`.gitignore` tells Git: **"Do not track these files."**

Without it, a developer can accidentally commit:
- `.env` files (database passwords, API keys)
- `terraform.tfstate` (contains infrastructure secrets)
- `.pem` files (SSH private keys)
- `node_modules/` (thousands of files that bloat the repo)

**Create a `.gitignore` at the root of every project:**

```gitignore
# Environment files NEVER commit these
.env
.env.local
.env.production
.env.*

# Terraform state contains real infrastructure secrets
*.tfstate
*.tfstate.backup
.terraform/

# SSH and certificates
*.pem
*.key
*.p12
*.pfx

# Cloud credentials
.aws/credentials
.gcp/
credentials.json

# IDE and OS files
.DS_Store
.vscode/
*.swp

# Dependency directories
node_modules/
__pycache__/
.venv/
vendor/
```

> ⚠️ **Important:** `.gitignore` only prevents *untracked* files from being committed. If a file was already committed once, `.gitignore` will not remove it from history. Use `git rm --cached <file>` to untrack it.

---

### 4.2 Pre-Commit Hooks Catching Secrets Before They Leave Your Laptop

`.gitignore` protects against accidental file commits. But what if a developer **hardcodes** a password directly inside `app.py`?

```python
# BAD hardcoded secret inside source code
DB_PASSWORD = "SuperSecret123!"
```

`.gitignore` cannot catch this. That is where **pre-commit hooks** come in.

#### How Pre-Commit Hooks Work

```
Developer runs: git commit -m "add feature"
                        ↓
              Pre-commit hook fires BEFORE the commit is saved
                        ↓
              Hook scans all staged files for patterns
                        ↓
         Found secret? → BLOCK commit, show error
         No secret?    → Allow commit to proceed
```

#### Option A: Native Git Hook (Custom Script)

The `.git/hooks/pre-commit` file runs automatically before every commit.

```bash
# .git/hooks/pre-commit
#!/bin/bash

echo "🔍 Scanning for secrets..."

# Check for common secret patterns
PATTERNS=(
  "password\s*=\s*['\"][^'\"]{6,}"
  "secret\s*=\s*['\"][^'\"]{6,}"
  "api_key\s*=\s*['\"][^'\"]{6,}"
  "AKIA[0-9A-Z]{16}"          # AWS Access Key
  "-----BEGIN.*PRIVATE KEY-----"
)

for pattern in "${PATTERNS[@]}"; do
  if git diff --cached | grep -iE "$pattern"; then
    echo "❌ SECRET DETECTED! Commit blocked."
    echo "   Remove the secret and use environment variables instead."
    exit 1
  fi
done

echo "✅ No secrets found. Proceeding with commit."
exit 0
```

```bash
# Make it executable
chmod +x .git/hooks/pre-commit
```

**Limitation:** You have to know every possible secret pattern. You cannot cover everything with a custom script.

#### Option B: The `pre-commit` Framework with Gitleaks (Recommended)

The `pre-commit` framework is an open-source tool that manages hooks for you. Gitleaks is a tool that knows **thousands of secret patterns** AWS keys, GitHub tokens, Stripe keys, Slack webhooks, and more.

**Step 1: Install the pre-commit framework**

```bash
pip install pre-commit
```

**Step 2: Create `.pre-commit-config.yaml` at the root of your repo**

```yaml
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/gitleaks/gitleaks
    rev: v8.24.2
    hooks:
      - id: gitleaks
        name: Detect hardcoded secrets
        description: Detect secrets using Gitleaks
        entry: gitleaks protect --staged --redact --config .gitleaks.toml
        language: golang
        pass_filenames: false

  # Bonus: also check for large files accidentally staged
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.6.0
    hooks:
      - id: check-added-large-files
        args: ['--maxkb=500']
      - id: detect-private-key
      - id: check-merge-conflict
```

**Step 3: Install the hooks into your repo**

```bash
pre-commit install
```

**Step 4: Keep hooks up to date automatically**

```bash
pre-commit autoupdate
```

Now every `git commit` automatically runs Gitleaks. If a secret is found, the commit is blocked with a clear error message.

---

### 4.3 Gitleaks Scanning the Entire Repository History

Pre-commit hooks protect future commits. But what about **secrets that were committed 2 years ago**?

Attackers who gain access to a repo always check the full commit history first.

```bash
# Scan the entire repo history for secrets
gitleaks detect --source . --verbose

# What this does:
# - Walks through EVERY commit from the beginning of the repo
# - Checks each file change against 150+ secret patterns
# - Reports: which file, which commit, which line, what type of secret
```

**Sample output:**

```
Finding:     password = "SuperSecret123!"
Secret:      SuperSecret123!
RuleID:      generic-password
Entropy:     3.58
File:        config/database.py
Line:        14
Commit:      a3f9c2d
Author:      john.doe@company.com
Date:        2023-06-15
```

If secrets are found in history, you must:
1. Rotate the secret immediately (assume it is compromised)
2. Use `git filter-branch` or BFG Repo Cleaner to remove it from history
3. Force push the cleaned history

---

### 4.4 Gitleaks in GitHub Actions Automated Scanning on Every PR

You cannot force every developer to install pre-commit. But you **can** enforce scanning in CI.

```yaml
# .github/workflows/gitleaks.yaml
name: Secret Scanning

on:
  push:
    branches: ["**"]
  pull_request:
    branches: ["**"]
  workflow_dispatch:  # allows manual trigger from GitHub UI

jobs:
  gitleaks-scan:
    name: Scan for Secrets
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
        with:
          fetch-depth: 0  # IMPORTANT: fetch full history, not just latest commit

      - name: Run Gitleaks
        uses: gitleaks/gitleaks-action@v2
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          # Only needed for GitHub Organizations (not personal accounts):
          # GITLEAKS_LICENSE: ${{ secrets.GITLEAKS_LICENSE }}
```

> 💡 `fetch-depth: 0` is critical. Without it, GitHub Actions only fetches the latest commit and misses historical secrets.

---

### 4.5 Branch Protection Rules Enforcing Code Review

By default, anyone with write access can push directly to `main`. This is dangerous.

**Best practice:** No one not even the repo owner should push directly to `main`. All changes must go through a Pull Request.

**How to set it up in GitHub:**

```
GitHub Repo → Settings → Branches → Add branch ruleset

Name: protect-main
Enforcement: Active
Target branches: main, releases-*

Rules to enable:
✅ Require a pull request before merging
   └─ Required approvals: 2
   └─ Require review from Code Owners
   └─ Dismiss stale reviews when new commits are pushed

✅ Require status checks to pass before merging
   └─ Add: gitleaks-scan (your CI job name)
   └─ Add: any other required CI checks

✅ Require branches to be up to date before merging
✅ Block force pushes
✅ Restrict deletions
```

This means a PR can only be merged when:
1. All CI checks pass (including Gitleaks)
2. At least 2 reviewers have approved
3. A Code Owner has reviewed (if applicable)

---

### 4.6 CODEOWNERS Mandatory Expert Review

In large projects, you want specific people to review specific parts of the codebase.

```
# .github/CODEOWNERS

# Global rule security team reviews everything
*                           @security-team

# Terraform changes must be reviewed by cloud team
/terraform/                 @cloud-team

# Payment service only senior engineers
/services/payments/         @senior-engineers @security-team

# GitHub Actions workflows DevSecOps team must review
/.github/workflows/         @devsecops-team

# Kubernetes manifests
/k8s/                       @platform-team
```

When a PR touches any of these paths, the specified team is **automatically added as a required reviewer**.

---

### 4.7 RBAC Who Can Do What in Your Repo

Not everyone needs the same level of access.

| Role | Access Level | Who Gets It |
|------|-------------|-------------|
| **Admin** | Full control settings, delete repo, manage members | DevOps leads, repo owners |
| **Maintain** | Manage issues, PRs, settings (no destructive actions) | Senior engineers |
| **Write** | Push branches, create PRs, merge (with approval) | All developers |
| **Triage** | Manage issues and PRs, cannot push code | QA, project managers |
| **Read** | View and clone only | External contractors, auditors |

```
GitHub Repo → Settings → Collaborators and teams → Manage access
```

---

### 4.8 Dependabot Automated Dependency Updates

Your application uses third-party packages. Those packages have vulnerabilities. Dependabot watches your dependencies and **automatically creates PRs** to update vulnerable packages.

```yaml
# .github/dependabot.yml
version: 2
updates:
  # Node.js / npm
  - package-ecosystem: "npm"
    directory: "/"
    schedule:
      interval: "weekly"
    open-pull-requests-limit: 10
    labels:
      - "dependencies"
      - "security"

  # Python / pip
  - package-ecosystem: "pip"
    directory: "/"
    schedule:
      interval: "weekly"

  # Docker base images
  - package-ecosystem: "docker"
    directory: "/"
    schedule:
      interval: "weekly"

  # GitHub Actions
  - package-ecosystem: "github-actions"
    directory: "/"
    schedule:
      interval: "weekly"
```

Dependabot will:
1. Scan your `package.json`, `requirements.txt`, `Dockerfile`, etc.
2. Check against the GitHub Advisory Database for known CVEs
3. Automatically open a PR with the fix
4. Show you the severity of the vulnerability

---

## 5. IaC Security Terraform

Infrastructure as Code (IaC) means your cloud infrastructure servers, databases, networks is defined in code files (usually Terraform `.tf` files). This is powerful, but it introduces new security risks.

### Common IaC Security Mistakes

| Mistake | Real-World Impact |
|---------|------------------|
| Hardcoded AWS credentials in `.tf` files | Attacker gets full AWS access |
| S3 bucket created as `public = true` | All company data exposed to internet |
| Security group with `0.0.0.0/0` on port 22 | Anyone can SSH into your servers |
| `terraform.tfstate` committed to Git | Contains all resource IDs, IPs, and sometimes secrets |
| No encryption on RDS database | Data breach if storage is compromised |

---

### 5.1 What NOT to Do

```hcl
# ❌ NEVER DO THIS hardcoded credentials
provider "aws" {
  region     = "us-east-1"
  access_key = "AKIAIOSFODNN7EXAMPLE"      # ← hardcoded AWS key
  secret_key = "wJalrXUtnFEMI/K7MDENG"    # ← hardcoded secret
}

# ❌ NEVER DO THIS public S3 bucket
resource "aws_s3_bucket_acl" "data" {
  acl = "public-read"    # ← entire bucket exposed to internet
}

# ❌ NEVER DO THIS open SSH to the world
resource "aws_security_group_rule" "ssh" {
  cidr_blocks = ["0.0.0.0/0"]    # ← anyone can SSH in
  from_port   = 22
  to_port     = 22
  protocol    = "tcp"
}
```

---

### 5.2 Checkov Automated IaC Misconfiguration Scanner

Checkov is an open-source tool that scans your Terraform (and other IaC) files against **hundreds of security best practices**.

Think of it as a linter, but for security.

```bash
# Install
pip install checkov

# Scan your terraform directory
checkov -d ./terraform

# Scan a single file
checkov -f main.tf

# Output as JSON for CI integration
checkov -d ./terraform --output json
```

**Sample output:**

```
Check: CKV_AWS_18: "Ensure the S3 bucket has access logging enabled"
FAILED for resource: aws_s3_bucket.data
File: /terraform/s3.tf:12-20

Check: CKV_AWS_21: "Ensure the S3 bucket has versioning enabled"
FAILED for resource: aws_s3_bucket.data
File: /terraform/s3.tf:12-20

Passed checks: 18, Failed checks: 4, Skipped checks: 0
```

**Add Checkov to your CI pipeline:**

```yaml
# .github/workflows/iac-security.yaml
name: IaC Security Scan

on:
  pull_request:
    paths:
      - 'terraform/**'

jobs:
  checkov:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Run Checkov
        uses: bridgecrewio/checkov-action@master
        with:
          directory: terraform/
          framework: terraform
          output_format: sarif
          output_file_path: checkov-results.sarif
          soft_fail: false   # fail the pipeline if issues found

      - name: Upload results to GitHub Security tab
        uses: github/codeql-action/upload-sarif@v3
        with:
          sarif_file: checkov-results.sarif
```

---

### 5.3 HashiCorp Vault Secrets Management for CI/CD

#### The Problem with Storing Credentials in GitHub Secrets

GitHub Secrets seem safe, but:
- They are **long-lived** the same key works for months or years
- If someone gets access to the repo settings, they can see them
- There is no audit trail of who used the secret and when
- Rotating them requires manual updates in every repo

#### The Solution: Short-Lived Credentials via Vault + OIDC

```
The Flow:

Developer → Push code → GitHub Actions starts
                              ↓
                    GitHub sends JWT token to Vault
                    (using OIDC OpenID Connect protocol)
                              ↓
                    Vault verifies: "Is this really GitHub Actions
                    running on repo X, branch Y?"
                              ↓
                    Vault generates short-lived AWS credentials
                    (valid for 15 minutes only)
                              ↓
                    GitHub Actions uses credentials to deploy
                              ↓
                    Credentials automatically expire
                    (even if stolen, they are useless after 15 min)
```

**GitHub Actions workflow using Vault:**

```yaml
# .github/workflows/deploy.yaml
name: Deploy Infrastructure

on:
  push:
    branches: [main]

permissions:
  id-token: write   # Required for OIDC
  contents: read

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Get short-lived credentials from Vault
        uses: hashicorp/vault-action@v3
        with:
          url: https://vault.your-company.com
          method: jwt
          role: github-actions-deploy
          secrets: |
            aws/creds/deploy-role access_key | AWS_ACCESS_KEY_ID ;
            aws/creds/deploy-role secret_key | AWS_SECRET_ACCESS_KEY

      - name: Deploy with Terraform
        run: |
          terraform init
          terraform apply -auto-approve
        # AWS credentials are now available as environment variables
        # They will expire in 15 minutes automatically
```

**Best practices for `.gitignore` with Terraform:**

```gitignore
# Terraform NEVER commit these
*.tfstate
*.tfstate.backup
.terraform/
.terraform.lock.hcl
terraform.tfvars        # often contains real values
*.auto.tfvars
crash.log
override.tf
override.tf.json
```

---

## 6. Container Security Docker

A container is like a lightweight virtual machine that packages your application and all its dependencies together. Docker is the most popular tool for building and running containers.

### Why Container Security Matters

When you run a Docker container, it shares the **same Linux kernel** as the host machine. If the container is compromised, an attacker can potentially escape to the host.

---

### 6.1 Never Run Containers as Root

By default, processes inside a Docker container run as the `root` user. This is like running your entire application as a system administrator.

**Why this is dangerous:**

```
Container running as root
         ↓
Attacker exploits a vulnerability in your app
         ↓
Attacker has root inside the container
         ↓
Docker daemon runs as root on the host
         ↓
Attacker escapes container → gets root on the HOST machine
         ↓
Complete infrastructure compromise
```

**❌ Insecure Dockerfile (runs as root):**

```dockerfile
FROM node:25
WORKDIR /app
COPY . .
RUN npm install
EXPOSE 3000
CMD ["npm", "start"]
# No USER instruction = runs as root by default
```

**✅ Secure Dockerfile (non-root user):**

```dockerfile
FROM node:25
WORKDIR /app

# Create a dedicated non-root user and group
RUN groupadd -r appgroup && useradd -r -g appgroup appuser

COPY . .
RUN npm install

# Switch to non-root user BEFORE the CMD
USER appuser

EXPOSE 3000
CMD ["npm", "start"]
```

---

### 6.2 Multi-Stage Builds Smaller Images, Smaller Attack Surface

Every package inside a container is a potential vulnerability. The more packages, the more attack surface.

**The problem with single-stage builds:**

```
FROM node:25          ← Full Node.js image: 1.1 GB
WORKDIR /app
COPY . .
RUN npm install       ← Installs build tools + dev dependencies
EXPOSE 3000
CMD ["npm", "start"]

Final image size: ~400 MB
Contains: build tools, compilers, dev dependencies, npm cache
All of these are unnecessary at runtime and are potential vulnerabilities.
```

**Multi-stage builds split the process into two phases:**

```
Stage 1 (Build):    Install everything needed to BUILD the app
Stage 2 (Run):      Copy only the final artifact nothing else
```

**✅ Multi-stage Dockerfile:**

```dockerfile
# ─────────────────────────────────────────
# Stage 1: Build Stage
# ─────────────────────────────────────────
FROM node:25 AS builder
WORKDIR /build

# Copy dependency manifest first (Docker layer caching optimization)
# If package.json hasn't changed, Docker reuses the cached npm install layer
COPY package.json package-lock.json ./
RUN npm ci --only=production   # ci is faster and more reliable than install

# Copy source code
COPY . .

# ─────────────────────────────────────────
# Stage 2: Production Stage
# ─────────────────────────────────────────
FROM node:25-slim
WORKDIR /app

# Copy ONLY what we need from the build stage
COPY --from=builder /build/node_modules ./node_modules
COPY --from=builder /build/app.js ./app.js

EXPOSE 3000
CMD ["node", "app.js"]

# Result: ~80 MB instead of ~400 MB
# Build tools, compilers, and dev dependencies are NOT in the final image
```

---

### 6.3 Distroless Images The Most Minimal Base Image

Even `node:25-slim` contains a shell (`bash`/`sh`), package managers (`apt`), and system utilities. An attacker who gets into the container can use these tools.

**Distroless images** contain only:
- The application runtime (e.g., Node.js)
- The application itself
- Nothing else no shell, no package manager, no utilities

```dockerfile
# ─────────────────────────────────────────
# Stage 1: Build (needs full tools)
# ─────────────────────────────────────────
FROM node:25 AS builder
WORKDIR /build

# Create non-root user here (distroless has no shell to do this)
RUN groupadd -r appgroup && useradd -r -g appgroup appuser

COPY package.json package-lock.json ./
RUN npm ci --only=production
COPY . .

# ─────────────────────────────────────────
# Stage 2: Distroless Production Image
# ─────────────────────────────────────────
FROM gcr.io/distroless/nodejs20-debian12

WORKDIR /app

# Copy user definitions from builder (distroless has no useradd)
COPY --from=builder /etc/passwd /etc/passwd
COPY --from=builder /etc/group /etc/group

# Copy only app files
COPY --from=builder /build/node_modules ./node_modules
COPY --from=builder /build/app.js ./app.js

# Run as non-root
USER appuser

EXPOSE 3000
CMD ["app.js"]

# Final image size: ~52 MB
# No shell = attacker cannot run commands even if they get in
```

**Image size comparison:**

| Base Image | Size | Has Shell | Has Package Manager |
|-----------|------|-----------|-------------------|
| `node:25` | ~1.1 GB | ✅ Yes | ✅ Yes |
| `node:25-slim` | ~80 MB | ✅ Yes | ✅ Yes |
| `gcr.io/distroless/nodejs20` | ~52 MB | ❌ No | ❌ No |

---

### 6.4 `.dockerignore` Do Not Copy Secrets Into Your Image

The `COPY . .` instruction copies **everything** in your project directory into the image. This includes:

- `.env` files with passwords
- `.git/` directory (commit history)
- `node_modules/` (already installed in build stage)
- `Dockerfile` itself
- Test files, documentation

```dockerignore
# .dockerignore

# Secrets NEVER include these
.env
.env.*
*.pem
*.key
secrets/

# Git history
.git
.gitignore

# Dependencies (will be installed fresh in the image)
node_modules/
__pycache__/
.venv/

# Build artifacts
dist/
build/
*.log

# Development files
*.test.js
*.spec.js
tests/
docs/

# Docker files themselves
Dockerfile*
docker-compose*
.dockerignore
```

---

### 6.5 Hardened `docker run` Runtime Security

Even with a secure image, you can add extra protection at runtime:

```bash
docker run \
  --read-only \                        # Container filesystem is read-only
  --tmpfs /tmp \                       # Allow writes only to /tmp (in memory)
  --cap-drop ALL \                     # Drop ALL Linux capabilities
  --cap-add NET_BIND_SERVICE \         # Add back only what you need
  --security-opt no-new-privileges \   # Process cannot gain more privileges
  --pids-limit 100 \                   # Max 100 processes (prevents fork bombs)
  --memory 256m \                      # Max 256 MB RAM
  --cpus 0.5 \                         # Max 50% of one CPU core
  --user 1001:1001 \                   # Run as specific UID:GID
  -p 3000:3000 \
  your-image:tag
```

**What each flag does:**

| Flag | Protection |
|------|-----------|
| `--read-only` | Attacker cannot write malware to the filesystem |
| `--tmpfs /tmp` | Allows legitimate temp writes without persisting |
| `--cap-drop ALL` | Removes all Linux kernel capabilities (root powers) |
| `--security-opt no-new-privileges` | Prevents privilege escalation via setuid binaries |
| `--pids-limit` | Prevents fork bomb attacks that crash the host |
| `--memory` / `--cpus` | Prevents resource exhaustion attacks |

---

### 6.6 Trivy Scanning Images for CVEs

Before pushing an image to production, scan it for known vulnerabilities:

```bash
# Install Trivy
brew install aquasecurity/trivy/trivy   # macOS
# or
apt install trivy                        # Ubuntu

# Scan a local image
trivy image your-app:latest

# Scan and fail if HIGH or CRITICAL vulnerabilities found
trivy image --exit-code 1 --severity HIGH,CRITICAL your-app:latest

# Scan in CI pipeline
trivy image --format sarif --output trivy-results.sarif your-app:latest
```

**Add to GitHub Actions:**

```yaml
- name: Scan image with Trivy
  uses: aquasecurity/trivy-action@master
  with:
    image-ref: your-app:latest
    format: sarif
    output: trivy-results.sarif
    severity: HIGH,CRITICAL
    exit-code: 1   # Fail pipeline if vulnerabilities found

- name: Upload Trivy results to GitHub Security tab
  uses: github/codeql-action/upload-sarif@v3
  with:
    sarif_file: trivy-results.sarif
```

---

## 7. Kubernetes Security

Kubernetes (K8s) is the platform that runs and manages your containers at scale. Most engineers jump straight to creating an EKS cluster and deploying pods. A DevSecOps engineer thinks about the **entire security architecture** first.

### The Right Way to Set Up EKS (vs. the Common Way)

```
❌ Common approach:
   Create EKS cluster → Deploy pods → Add security later

✅ DevSecOps approach:
   VPC (Virtual Private Cloud)
    └─ Private Subnets (nodes are NOT accessible from internet)
        └─ EKS Cluster
            └─ Node Groups (EC2 instances in private subnets)
                └─ Pods (your applications)
                    └─ Secrets managed by Vault / AWS Secrets Manager
```

The key difference: **nodes live in private subnets**. There is no direct internet access to your cluster nodes. All traffic goes through a load balancer or VPN.

---

### 7.1 Namespaces Isolation Between Teams

By default, all pods in a Kubernetes cluster can see and interact with each other. This is a problem when multiple teams share one cluster.

**The problem without namespaces:**

```
Cluster (no namespaces)
├── payments-pod-1
├── payments-pod-2
├── shipment-pod-1
├── scores-pod-1
└── scores-configmap

Problem: A developer on the payments team can accidentally
         delete scores-configmap. There is no ownership boundary.
```

**The solution namespaces with resource quotas:**

```yaml
# namespace-setup.yaml

# Create isolated namespace for payments team
apiVersion: v1
kind: Namespace
metadata:
  name: team-payments
  labels:
    team: payments
    environment: production
---
# Limit how much CPU and memory the payments team can use
apiVersion: v1
kind: ResourceQuota
metadata:
  name: payments-quota
  namespace: team-payments
spec:
  hard:
    # Minimum resources pods must request
    requests.cpu: "4"
    requests.memory: 8Gi
    # Maximum resources pods can use
    limits.cpu: "10"
    limits.memory: 16Gi
    # Maximum number of pods
    pods: "20"
    # Maximum number of services
    services: "10"
```

```bash
kubectl apply -f namespace-setup.yaml

# Now deploy to a specific namespace
kubectl apply -f deployment.yaml -n team-payments

# A developer in team-payments cannot accidentally affect team-shipment
```

---

### 7.2 RBAC in Kubernetes Fine-Grained Permissions

Kubernetes RBAC controls **what actions** a pod or user can perform on **which resources**.

The three components:

```
ServiceAccount  →  Role  →  RoleBinding
(WHO)              (WHAT)    (connects WHO to WHAT)
```

**ServiceAccount** = an identity for a pod (like a user account, but for applications)

**Role** = a set of permissions (what verbs on what resources)

**RoleBinding** = attaches a Role to a ServiceAccount

**Real example: payments pod can only READ pods, not modify them**

```bash
# Step 1: Create a ServiceAccount for the payments app
kubectl create serviceaccount payments-app -n team-payments
```

```yaml
# Step 2: Create a Role with minimal permissions
# role-pod-reader.yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: pod-reader
  namespace: team-payments
rules:
  - apiGroups: [""]
    resources: ["pods"]
    verbs: ["get", "list", "watch"]   # READ ONLY no create, delete, update
  - apiGroups: [""]
    resources: ["configmaps"]
    verbs: ["get"]                    # Can read configmaps but not modify
```

```yaml
# Step 3: Bind the Role to the ServiceAccount
# rolebinding-payments.yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: payments-pod-reader
  namespace: team-payments
subjects:
  - kind: ServiceAccount
    name: payments-app
    namespace: team-payments
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

```bash
kubectl apply -f role-pod-reader.yaml
kubectl apply -f rolebinding-payments.yaml

# Verify permissions
kubectl auth can-i list pods \
  --as=system:serviceaccount:team-payments:payments-app \
  -n team-payments
# Output: yes

kubectl auth can-i delete pods \
  --as=system:serviceaccount:team-payments:payments-app \
  -n team-payments
# Output: no
```

**ClusterRole vs Role:**

| Type | Scope | Use Case |
|------|-------|----------|
| `Role` | Single namespace | App needs to read pods in its own namespace |
| `ClusterRole` | All namespaces | Monitoring tool needs to read pods everywhere |

---

### 7.3 Network Policies Controlling Pod-to-Pod Traffic

By default, **every pod can talk to every other pod** in the cluster. This means if an attacker compromises your frontend pod, they can directly connect to your database pod.

Network Policies let you define firewall rules at the pod level.

```yaml
# network-policy-payments-db.yaml
# Only allow the payments-api pod to connect to the payments-db pod
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: payments-db-access
  namespace: team-payments
spec:
  # This policy applies to pods with label: app=payments-db
  podSelector:
    matchLabels:
      app: payments-db

  policyTypes:
    - Ingress    # Control incoming traffic
    - Egress     # Control outgoing traffic

  ingress:
    # Only allow traffic FROM pods with label: app=payments-api
    - from:
        - podSelector:
            matchLabels:
              app: payments-api
      ports:
        - protocol: TCP
          port: 5432   # PostgreSQL port

  egress:
    # Database pod should not initiate outbound connections
    - {}   # Empty = deny all egress
```

```bash
kubectl apply -f network-policy-payments-db.yaml

# Now:
# payments-api → payments-db: ALLOWED (port 5432)
# frontend → payments-db:     BLOCKED
# shipment-api → payments-db: BLOCKED
```

> 💡 **Note:** Network Policies require a CNI plugin that supports them. For local clusters use **Calico**. For EKS use the **AWS VPC CNI** with network policy support enabled.

---

### 7.4 Kyverno Policy Enforcement with Admission Controllers

**The problem:** A junior engineer deploys a pod using `image: mysql:latest`. Today it works. Tomorrow, `latest` gets a critical CVE. Your production database is now vulnerable.

**Admission Controllers** intercept every request to the Kubernetes API before it is saved. Kyverno lets you write policies that:
- **Validate**: Block resources that violate your rules
- **Mutate**: Automatically add labels, annotations, or security settings
- **Generate**: Automatically create related resources

```bash
# Install Kyverno
kubectl apply --server-side \
  -f https://github.com/kyverno/kyverno/releases/latest/download/install.yaml
```

**Policy 1: Block `latest` image tags**

```yaml
# policy-no-latest-tag.yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: disallow-latest-tag
  annotations:
    policies.kyverno.io/description: >
      Require all container images to have a specific version tag.
      The 'latest' tag is mutable and makes deployments unpredictable.
spec:
  validationFailureAction: Enforce   # Enforce = block, Audit = just warn
  rules:
    - name: require-image-tag
      match:
        resources:
          kinds:
            - Pod
      validate:
        message: "Image tag 'latest' is not allowed. Use a specific version like 'mysql:8.0.35'."
        pattern:
          spec:
            containers:
              - image: "!*:latest"
```

**Policy 2: Require non-root containers**

```yaml
# policy-no-root-containers.yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: require-non-root
spec:
  validationFailureAction: Enforce
  rules:
    - name: check-runAsNonRoot
      match:
        resources:
          kinds:
            - Pod
      validate:
        message: "Containers must not run as root. Set runAsNonRoot: true."
        pattern:
          spec:
            containers:
              - securityContext:
                  runAsNonRoot: true
```

**Policy 3: Auto-mutate add security context if missing**

```yaml
# policy-add-security-context.yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: add-default-security-context
spec:
  rules:
    - name: add-security-context
      match:
        resources:
          kinds:
            - Pod
      mutate:
        patchStrategicMerge:
          spec:
            containers:
              - (name): "*"
                securityContext:
                  +(allowPrivilegeEscalation): false
                  +(readOnlyRootFilesystem): true
```

---

### 7.5 Secrets Management in Kubernetes

Kubernetes has a built-in `Secret` resource, but it has a critical flaw: **secrets are only base64 encoded, not encrypted**.

```bash
# Anyone with kubectl access can decode a secret in seconds
kubectl get secret db-credentials -o jsonpath='{.data.password}' | base64 -d
# Output: SuperSecret123!
```

**The right approach: External Secrets Operator (ESO) + HashiCorp Vault**

Instead of storing actual secrets in Kubernetes, you store a **reference** to where the secret lives in Vault. ESO fetches the real secret at runtime.

```
What you store in Git (safe to commit):
┌─────────────────────────────────────┐
│  ExternalSecret manifest            │
│  "Get the DB password from          │
│   vault/secret/payments/db"         │
└─────────────────────────────────────┘

What ESO does at runtime:
ExternalSecret → ESO → Vault → fetches real secret → creates K8s Secret
```

**Step 1: Install ESO**

```bash
helm repo add external-secrets https://charts.external-secrets.io
helm install external-secrets external-secrets/external-secrets -n external-secrets --create-namespace
```

**Step 2: Configure Vault as a SecretStore**

```yaml
# vault-secret-store.yaml
apiVersion: external-secrets.io/v1beta1
kind: SecretStore
metadata:
  name: vault-backend
  namespace: team-payments
spec:
  provider:
    vault:
      server: "https://vault.your-company.com"
      path: "secret"
      version: "v2"
      auth:
        kubernetes:
          mountPath: "kubernetes"
          role: "payments-app"
```

**Step 3: Create an ExternalSecret (safe to commit to Git)**

```yaml
# external-secret-db.yaml
apiVersion: external-secrets.io/v1beta1
kind: ExternalSecret
metadata:
  name: payments-db-credentials
  namespace: team-payments
spec:
  refreshInterval: 1h   # Re-fetch from Vault every hour
  secretStoreRef:
    name: vault-backend
    kind: SecretStore
  target:
    name: db-credentials   # Name of the K8s Secret to create
    creationPolicy: Owner
  data:
    - secretKey: password
      remoteRef:
        key: secret/payments/database
        property: password
    - secretKey: username
      remoteRef:
        key: secret/payments/database
        property: username
```

```bash
# This file is safe to commit to Git it contains no actual secrets
kubectl apply -f external-secret-db.yaml

# ESO automatically creates the real K8s Secret by fetching from Vault
kubectl get secret db-credentials -n team-payments
```

---

## 8. CI/CD Pipeline Security

A CI/CD pipeline is the automated assembly line that takes code from a developer's commit to a running application in production. A DevSecOps engineer turns this pipeline into a **security gate** code cannot reach production unless it passes every security check.

### The Secure Pipeline Architecture

```
Developer pushes code
         ↓
┌─────────────────────────────────────────────────────────────┐
│                    CI/CD PIPELINE                           │
│                                                             │
│  Stage 1: Secret Scanning (Gitleaks)                        │
│           ↓ PASS                                            │
│  Stage 2: SAST Static Code Analysis (SonarQube/Semgrep)     │
│           ↓ PASS                                            │
│  Stage 3: SCA Dependency Scanning (Snyk/OWASP Dependency)   │
│           ↓ PASS                                            │
│  Stage 4: Build Docker Image                                │
│           ↓                                                 │
│  Stage 5: Container Scanning (Trivy)                        │
│           ↓ PASS                                            │
│  Stage 6: IaC Scanning (Checkov)                            │
│           ↓ PASS                                            │
│  Stage 7: Deploy to Staging                                 │
│           ↓                                                 │
│  Stage 8: DAST Dynamic Testing (OWASP ZAP)                  │
│           ↓ PASS                                            │
│  Stage 9: Deploy to Production                              │
└─────────────────────────────────────────────────────────────┘
```

Any stage that fails **stops the pipeline**. Code does not move forward.

### Complete Secure Pipeline Example

```yaml
# .github/workflows/secure-pipeline.yaml
name: Secure CI/CD Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

permissions:
  contents: read
  security-events: write   # Required for uploading SARIF results
  id-token: write          # Required for OIDC with Vault

jobs:
  # ─────────────────────────────────────────
  # Stage 1: Secret Scanning
  # ─────────────────────────────────────────
  secret-scan:
    name: Secret Scanning
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
      - uses: gitleaks/gitleaks-action@v2
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

  # ─────────────────────────────────────────
  # Stage 2: SAST Static Code Analysis
  # ─────────────────────────────────────────
  sast:
    name: Static Code Analysis
    runs-on: ubuntu-latest
    needs: secret-scan
    steps:
      - uses: actions/checkout@v4

      - name: Run Semgrep SAST
        uses: semgrep/semgrep-action@v1
        with:
          config: >-
            p/security-audit
            p/owasp-top-ten
            p/python
          generateSarif: "1"

      - name: Upload SAST results
        uses: github/codeql-action/upload-sarif@v3
        with:
          sarif_file: semgrep.sarif

  # ─────────────────────────────────────────
  # Stage 3: SCA Dependency Scanning
  # ─────────────────────────────────────────
  sca:
    name: Dependency Vulnerability Scan
    runs-on: ubuntu-latest
    needs: secret-scan
    steps:
      - uses: actions/checkout@v4

      - name: Run Snyk SCA
        uses: snyk/actions/python@master
        env:
          SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
        with:
          args: --severity-threshold=high --sarif-file-output=snyk.sarif

      - name: Upload SCA results
        uses: github/codeql-action/upload-sarif@v3
        with:
          sarif_file: snyk.sarif

  # ─────────────────────────────────────────
  # Stage 4 & 5: Build + Container Scan
  # ─────────────────────────────────────────
  build-and-scan:
    name: Build and Scan Container
    runs-on: ubuntu-latest
    needs: [sast, sca]
    steps:
      - uses: actions/checkout@v4

      - name: Build Docker image
        run: docker build -t app:${{ github.sha }} .

      - name: Scan image with Trivy
        uses: aquasecurity/trivy-action@master
        with:
          image-ref: app:${{ github.sha }}
          format: sarif
          output: trivy-results.sarif
          severity: HIGH,CRITICAL
          exit-code: 1

      - name: Upload Trivy results
        uses: github/codeql-action/upload-sarif@v3
        with:
          sarif_file: trivy-results.sarif

  # ─────────────────────────────────────────
  # Stage 6: IaC Scanning
  # ─────────────────────────────────────────
  iac-scan:
    name: IaC Security Scan
    runs-on: ubuntu-latest
    needs: secret-scan
    steps:
      - uses: actions/checkout@v4

      - name: Run Checkov
        uses: bridgecrewio/checkov-action@master
        with:
          directory: terraform/
          soft_fail: false

  # ─────────────────────────────────────────
  # Stage 7: Deploy to Staging
  # ─────────────────────────────────────────
  deploy-staging:
    name: Deploy to Staging
    runs-on: ubuntu-latest
    needs: [build-and-scan, iac-scan]
    environment: staging
    steps:
      - uses: actions/checkout@v4

      - name: Get credentials from Vault
        uses: hashicorp/vault-action@v3
        with:
          url: ${{ secrets.VAULT_URL }}
          method: jwt
          role: github-deploy-staging
          secrets: |
            aws/creds/staging access_key | AWS_ACCESS_KEY_ID ;
            aws/creds/staging secret_key | AWS_SECRET_ACCESS_KEY

      - name: Deploy to staging
        run: kubectl apply -f k8s/ -n staging

  # ─────────────────────────────────────────
  # Stage 8: DAST Dynamic Testing
  # ─────────────────────────────────────────
  dast:
    name: Dynamic Application Security Testing
    runs-on: ubuntu-latest
    needs: deploy-staging
    steps:
      - name: Run OWASP ZAP Baseline Scan
        uses: zaproxy/action-baseline@v0.12.0
        with:
          target: https://staging.your-app.com
          rules_file_name: zap-rules.tsv
          cmd_options: '-a'   # include alpha passive scan rules

  # ─────────────────────────────────────────
  # Stage 9: Deploy to Production
  # ─────────────────────────────────────────
  deploy-production:
    name: Deploy to Production
    runs-on: ubuntu-latest
    needs: dast
    environment: production   # Requires manual approval in GitHub
    steps:
      - uses: actions/checkout@v4

      - name: Get production credentials from Vault
        uses: hashicorp/vault-action@v3
        with:
          url: ${{ secrets.VAULT_URL }}
          method: jwt
          role: github-deploy-production
          secrets: |
            aws/creds/production access_key | AWS_ACCESS_KEY_ID ;
            aws/creds/production secret_key | AWS_SECRET_ACCESS_KEY

      - name: Deploy to production
        run: kubectl apply -f k8s/ -n production
```

---

## 9. Application Security SAST, DAST, SCA

These three testing methods cover different angles of application security. Together, they form a complete picture.

```
SAST  →  Reads your source code without running it  (like proofreading)
SCA   →  Checks your dependencies for known CVEs    (like checking ingredients)
DAST  →  Attacks your running application           (like a real hacker)
```

---

### 9.1 SAST Static Application Security Testing

SAST analyzes your **source code** without executing it. It looks for patterns that indicate security vulnerabilities.

**What SAST finds:**
- SQL Injection vulnerabilities
- Cross-Site Scripting (XSS)
- Hardcoded credentials
- Insecure cryptography (MD5, SHA1)
- Path traversal vulnerabilities
- Insecure deserialization

**What SAST cannot find:**
- Vulnerabilities that only appear at runtime
- Business logic flaws
- Authentication issues that depend on configuration

#### Tool: Semgrep

Semgrep is a fast, open-source SAST tool that supports 30+ languages.

```bash
# Install
pip install semgrep

# Scan with OWASP Top 10 rules
semgrep --config=p/owasp-top-ten ./src

# Scan Python code
semgrep --config=p/python-security ./src

# Scan with multiple rule sets
semgrep --config=p/security-audit --config=p/secrets ./src
```

**Example: Semgrep catches SQL Injection**

```python
# ❌ Vulnerable code Semgrep will flag this
def get_user(username):
    query = f"SELECT * FROM users WHERE username = '{username}'"
    cursor.execute(query)   # SQL Injection vulnerability

# ✅ Safe code parameterized query
def get_user(username):
    query = "SELECT * FROM users WHERE username = %s"
    cursor.execute(query, (username,))
```

#### Tool: SonarQube

SonarQube is an enterprise-grade SAST platform with a web dashboard.

```yaml
# sonar-project.properties
sonar.projectKey=my-app
sonar.projectName=My Application
sonar.sources=src
sonar.language=py
sonar.python.coverage.reportPaths=coverage.xml
```

```yaml
# In GitHub Actions
- name: SonarQube Scan
  uses: SonarSource/sonarqube-scan-action@master
  env:
    SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
    SONAR_HOST_URL: ${{ secrets.SONAR_HOST_URL }}
```

---

### 9.2 SCA Software Composition Analysis

Modern applications are 80% third-party code (libraries, frameworks, packages). SCA checks every dependency you use against databases of known vulnerabilities (CVEs).

**The problem SCA solves:**

```
Your app uses: requests==2.25.0
               ↓
CVE-2023-32681 discovered in requests < 2.31.0
               ↓
Without SCA: You never know. Your app is vulnerable.
With SCA:    Pipeline fails. You get a PR to update to 2.31.0.
```

#### Tool: Snyk

```bash
# Install
npm install -g snyk

# Authenticate
snyk auth

# Test Python project
snyk test --file=requirements.txt

# Test Node.js project
snyk test

# Monitor continuously (sends alerts when new CVEs are found)
snyk monitor
```

**Sample Snyk output:**

```
✗ High severity vulnerability found in django
  Description: SQL Injection
  Info: https://snyk.io/vuln/SNYK-PYTHON-DJANGO-2314115
  Introduced through: django@3.2.0
  Fix: Upgrade django to 3.2.14 or higher

✗ Medium severity vulnerability found in pillow
  Description: Buffer Overflow
  Introduced through: pillow@8.3.1
  Fix: Upgrade pillow to 9.0.0 or higher

2 vulnerabilities found (1 high, 1 medium)
```

#### Tool: OWASP Dependency-Check (Free Alternative)

```bash
# Run dependency check
dependency-check --project "My App" --scan ./src --format HTML

# In GitHub Actions
- name: OWASP Dependency Check
  uses: dependency-check/Dependency-Check_Action@main
  with:
    project: 'my-app'
    path: '.'
    format: 'SARIF'
    out: 'reports'
    args: --failOnCVSS 7   # Fail if CVSS score >= 7 (High)
```

---

### 9.3 DAST Dynamic Application Security Testing

DAST is the most realistic security test. It **actually attacks your running application** the same way a real hacker would sending malicious inputs, probing endpoints, testing authentication.

**What DAST finds that SAST cannot:**
- Authentication and session management flaws
- Server misconfigurations
- Vulnerabilities that only appear with specific inputs
- Business logic flaws
- API security issues

**The DAST workflow:**

```
1. Deploy app to staging environment
2. DAST tool crawls the application (finds all pages/endpoints)
3. DAST tool sends attack payloads to each endpoint:
   - SQL Injection attempts
   - XSS payloads
   - Path traversal attempts
   - Authentication bypass attempts
4. DAST tool reports which attacks succeeded
5. Pipeline fails if critical vulnerabilities found
```

#### Tool: OWASP ZAP (Zed Attack Proxy)

OWASP ZAP is the most widely used open-source DAST tool.

**Baseline scan (passive no active attacks, safe for any environment):**

```yaml
# .github/workflows/dast.yaml
- name: ZAP Baseline Scan
  uses: zaproxy/action-baseline@v0.12.0
  with:
    target: 'https://staging.your-app.com'
    # Baseline scan: crawls and passively checks for issues
    # Does NOT send attack payloads safe for staging
```

**Full scan (active sends real attack payloads, staging only):**

```yaml
- name: ZAP Full Scan
  uses: zaproxy/action-full-scan@v0.10.0
  with:
    target: 'https://staging.your-app.com'
    rules_file_name: '.zap/rules.tsv'
    cmd_options: '-a -j'
    # Full scan: actively tests for SQL injection, XSS, etc.
    # ONLY run against staging NEVER against production
```

**Custom ZAP rules file (`.zap/rules.tsv`):**

```tsv
# Rule ID    Action    Description
10016        IGNORE    Web Browser XSS Protection Not Enabled (handled by CDN)
10020        IGNORE    X-Frame-Options Header (handled by CDN)
40012        WARN      Cross Site Scripting (Reflected)
40014        FAIL      Cross Site Scripting (Persistent)
40018        FAIL      SQL Injection
90022        FAIL      Application Error Disclosure
```

---

### 9.4 SAST vs DAST vs SCA Quick Reference

| | SAST | SCA | DAST |
|--|------|-----|------|
| **What it tests** | Your source code | Your dependencies | Your running app |
| **When it runs** | At build time | At build time | After deployment |
| **Needs running app?** | No | No | Yes |
| **Finds** | Code vulnerabilities | Known CVEs in packages | Runtime vulnerabilities |
| **Speed** | Fast (seconds) | Fast (seconds) | Slow (minutes) |
| **False positives** | High | Low | Medium |
| **Best tools** | Semgrep, SonarQube | Snyk, OWASP Dep-Check | OWASP ZAP |

---

## 10. The Ultimate Secure Pipeline

Putting it all together - here is the complete DevSecOps security checklist organized by layer.

### Security Checklist by Layer

#### Git Security
- [ ] .gitignore configured for secrets, state files, credentials
- [ ] pre-commit framework installed with Gitleaks
- [ ] .pre-commit-config.yaml committed to repo
- [ ] Gitleaks GitHub Actions workflow added
- [ ] Branch protection rules enabled on main
- [ ] Minimum 2 required reviewers on PRs
- [ ] CODEOWNERS file configured
- [ ] RBAC configured (Admin / Write / Read)
- [ ] Dependabot enabled for all package ecosystems

#### IaC Security
- [ ] No hardcoded credentials in .tf files
- [ ] terraform.tfstate in .gitignore
- [ ] Checkov running in CI on every Terraform PR
- [ ] HashiCorp Vault configured for short-lived credentials
- [ ] GitHub Actions using OIDC (not long-lived keys)

#### Container Security
- [ ] Dockerfile uses non-root user
- [ ] Multi-stage build implemented
- [ ] Distroless or slim base image used
- [ ] .dockerignore configured
- [ ] Trivy scanning in CI pipeline
- [ ] docker run uses --read-only, --cap-drop ALL, resource limits

#### Kubernetes Security
- [ ] EKS nodes in private subnets
- [ ] Namespaces created per team
- [ ] ResourceQuotas set per namespace
- [ ] RBAC: ServiceAccounts with minimal permissions
- [ ] Network Policies restricting pod-to-pod traffic
- [ ] Kyverno policies: no latest tags, no root containers
- [ ] External Secrets Operator + Vault for secrets
- [ ] No raw Kubernetes Secrets committed to Git

#### Application Security
- [ ] SAST (Semgrep/SonarQube) in CI pipeline
- [ ] SCA (Snyk/OWASP Dep-Check) in CI pipeline
- [ ] DAST (OWASP ZAP) against staging after every deploy
- [ ] All HIGH/CRITICAL findings block the pipeline

---

### Tools Reference

| Category | Tool | Purpose | Cost |
|----------|------|---------|------|
| Secret Scanning | Gitleaks | Pre-commit + CI scanning | Free |
| Secret Scanning | GitHub Secret Scanning | Automatic repo scanning | Free |
| IaC Scanning | Checkov | Terraform misconfiguration | Free |
| Secrets Management | HashiCorp Vault | Dynamic short-lived credentials | Free (OSS) |
| Container Scanning | Trivy | CVE scanning for images | Free |
| Container Linting | Hadolint | Dockerfile best practices | Free |
| SAST | Semgrep | Source code vulnerability scanning | Free (OSS) |
| SAST | SonarQube | Enterprise code quality + security | Free (Community) |
| SCA | Snyk | Dependency vulnerability scanning | Free (limited) |
| SCA | OWASP Dep-Check | Dependency CVE scanning | Free |
| DAST | OWASP ZAP | Dynamic application testing | Free |
| K8s Policy | Kyverno | Admission controller policies | Free |
| K8s Secrets | External Secrets Operator | Vault-to-K8s secret sync | Free |
| Threat Modelling | OWASP Threat Dragon | Visual threat modelling | Free |

---

Built with a security-first mindset.
Every commit is a security decision. Make it count That's it. A Big Bye from Mathanprasath K!.
