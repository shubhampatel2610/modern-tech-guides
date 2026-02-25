# CI/CD Complete Guide for Beginners

**Date:** February 18, 2026  
**Topics Covered:** CI/CD basics, pipelines, examples, tools, best practices, and interview tips

---

## Table of Contents
1. [What is CI/CD?](#what-is-cicd)
2. [Why We Use CI/CD](#why-we-use-cicd)
3. [CI vs CD (Simple Difference)](#ci-vs-cd-simple-difference)
4. [Typical CI/CD Pipeline Flow](#typical-cicd-pipeline-flow)
5. [Example: Simple Node.js Pipeline](#example-simple-nodejs-pipeline)
6. [Example: Docker Build + Deploy](#example-docker-build--deploy)
7. [Common Stages Explained](#common-stages-explained)
8. [CI/CD for Branches (Dev → Prod)](#cicd-for-branches-dev--prod)
9. [How to Add CI/CD to an Existing Project](#how-to-add-cicd-to-an-existing-project)
10. [Where to See Builds, Logs, and Artifacts](#where-to-see-builds-logs-and-artifacts)
11. [How to Deploy a Specific Version](#how-to-deploy-a-specific-version)
12. [Best Practices](#best-practices)
13. [Common Problems & Fixes](#common-problems--fixes)
14. [Popular CI/CD Tools](#popular-cicd-tools)
15. [Interview Questions & Quick Answers](#interview-questions--quick-answers)

---

## What is CI/CD?
**CI (Continuous Integration):** Developers merge code frequently, and every merge triggers automated tests and build checks.  
**CD (Continuous Delivery/Deployment):** After CI passes, the app is automatically released to staging or production.

**Easy analogy:**
- **CI** is like checking every puzzle piece fits before adding it.  
- **CD** is like automatically delivering the finished puzzle to the customer.

---

## Why We Use CI/CD
- **Fewer bugs**: Tests run on every change.
- **Faster releases**: Automation removes manual steps.
- **Consistent builds**: Same steps every time.
- **Confidence**: You know what is in production.

---

## CI vs CD (Simple Difference)
- **CI:** Build + Test (always)
- **CD (Delivery):** Automatically prepare release (may still need manual approval)
- **CD (Deployment):** Automatically deploy to production

---

## Typical CI/CD Pipeline Flow

```
Developer -> Git push -> CI build/test -> Package/Artifact -> Deploy to Staging -> Approve -> Deploy to Prod
```

**Step-by-step in simple words:**
1. Developer pushes code to Git.
2. CI runs tests and builds the app.
3. Pipeline creates an artifact (zip/docker image).
4. Deploy to staging for review/testing.
5. If approved, deploy to production.

---

## Example: Simple Node.js Pipeline

### Project structure (simple)
```
my-app/
├── src/
├── package.json
└── tests/
```

### Basic pipeline steps (commented)
```yaml
# Example pipeline steps (generic YAML)
steps:
  - name: Install
    run: npm ci

  - name: Lint
    run: npm run lint

  - name: Test
    run: npm test

  - name: Build
    run: npm run build
```

**What this does:**
- Installs dependencies
- Checks code style
- Runs tests
- Builds final output

---

## Example: Docker Build + Deploy

### Build a Docker image
```bash
# Build the app image
# (exact command depends on CI system)
docker build -t my-app:${GIT_SHA} .
```

### Push to registry
```bash
# Push to registry so servers can pull it
docker push registry.example.com/my-app:${GIT_SHA}
```

### Deploy (simple)
```bash
# Deploy to Kubernetes (example)
kubectl set image deployment/my-app my-app=registry.example.com/my-app:${GIT_SHA}
```

---

## Common Stages Explained

1. **Checkout** – Get code from Git
2. **Install** – Download dependencies
3. **Lint** – Check code style
4. **Test** – Run unit/integration tests
5. **Build** – Compile/package code
6. **Security Scan** – Check vulnerabilities
7. **Artifact** – Save build output
8. **Deploy** – Release to environment

---

## CI/CD for Branches (Dev → Prod)

**Typical flow:**
- **feature/** → PR → **develop** → staging
- **main** → production

**Why:**
- Keeps production stable
- Lets QA test in staging

---

## How to Add CI/CD to an Existing Project

Think of it as **writing a recipe** for your project so the build server can repeat it.

### Step 1: Pick a CI/CD tool
Use the tool that matches your Git hosting:
- GitHub → **GitHub Actions**
- GitLab → **GitLab CI**
- Azure Repos → **Azure Pipelines**

### Step 2: Add a pipeline file
Below is a **simple GitHub Actions** example for a Node.js app.

**File:** `.github/workflows/ci.yml`
```yaml
name: CI

on:
  push:
    branches: ["main", "develop"]
  pull_request:

jobs:
  build-test:
    runs-on: ubuntu-latest

    steps:
      # 1) Checkout code
      - name: Checkout
        uses: actions/checkout@v4

      # 2) Install Node
      - name: Setup Node
        uses: actions/setup-node@v4
        with:
          node-version: 20

      # 3) Install dependencies
      - name: Install
        run: npm ci

      # 4) Run tests
      - name: Test
        run: npm test

      # 5) Build
      - name: Build
        run: npm run build
```

### Step 3: Add a deployment stage (simple example)
```yaml
# Example: deploy when pushing to main
  deploy:
    needs: build-test
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
      - name: Deploy
        run: echo "Deploying to production..."
```

### Step 4: Store secrets safely
Put tokens, passwords, and API keys in **CI/CD secrets** (never in code).

---

## Where to See Builds, Logs, and Artifacts

### GitHub Actions
- Go to your repo → **Actions** tab
- Click a workflow run to see:
  - **Logs** (each step)
  - **Artifacts** (if uploaded)

### GitLab CI
- Repo → **CI/CD** → **Pipelines**
- Click pipeline → **Jobs** → logs

### Azure DevOps
- Project → **Pipelines** → **Runs**

**Tip:** Add an artifact step so you can download the build output.
```yaml
- name: Upload artifact
  uses: actions/upload-artifact@v4
  with:
    name: build-output
    path: dist/
```

---

## How to Deploy a Specific Version

### Option 1: Deploy by Git tag (simple and common)
1. Tag a release in Git:
   ```bash
   git tag v1.2.3
   git push origin v1.2.3
   ```
2. Pipeline triggers on tag and deploys that exact version.

```yaml
on:
  push:
    tags:
      - 'v*'
```

### Option 2: Deploy by Docker image tag
Build images with the commit SHA and deploy that exact version:
```bash
# Build image with commit hash
docker build -t my-app:${GIT_SHA} .

# Deploy using that tag
kubectl set image deployment/my-app my-app=registry/my-app:${GIT_SHA}
```

### Option 3: Manual approval for production
Many teams deploy automatically to **staging**, then require approval for **production**.

---

## Best Practices
- Keep pipelines **fast** (developers hate slow pipelines)
- Use **caching** for dependencies
- Fail early (lint/test first)
- Separate **build** and **deploy**
- Use **secrets managers** (never hardcode keys)
- Use **staging** before production

---

## Common Problems & Fixes

**1) Pipeline is slow**
- Use caching (npm/yarn cache)
- Run tests in parallel

**2) “Works on my machine”**
- Build inside Docker for consistency

**3) Flaky tests**
- Fix unstable tests or isolate them

**4) Deployment fails**
- Check logs, roll back to last good artifact

---

## Popular CI/CD Tools
- **GitHub Actions** (easy, integrated with GitHub)
- **GitLab CI** (great for GitLab repos)
- **Jenkins** (flexible, self-hosted)
- **Azure DevOps Pipelines** (enterprise)
- **CircleCI** (fast cloud CI)
- **Bitbucket Pipelines**

---

## Interview Questions & Quick Answers

**1) What is CI/CD?**  
CI is automated build/test; CD is automated release/deploy.

**2) Why is CI important?**  
It catches issues early and keeps main branch stable.

**3) Difference between delivery and deployment?**  
Delivery prepares for release; deployment actually releases to production.

**4) What is an artifact?**  
A packaged build output like a zip file or Docker image.

**5) What is a pipeline?**  
A sequence of automated steps from code to deployment.

**6) What is rollback?**  
Reverting to a previous stable version if deployment fails.

**7) How do you secure CI/CD?**  
Use secrets managers, least privilege, and signed artifacts.

**8) What is blue-green deployment?**  
Two environments (blue/green); switch traffic to new version after validation.

**9) What is canary deployment?**  
Release to a small % of users first, then expand.

**10) What causes flaky pipelines?**  
Unstable tests, environment issues, race conditions.

---

## Final Tip
Start small: run tests on every push, then add build + deploy when stable. Automation grows step by step.
