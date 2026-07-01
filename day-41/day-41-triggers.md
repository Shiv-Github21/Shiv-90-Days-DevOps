# Understanding a Pull Request Workflow (Beginner Friendly)

Let's understand it as if you're using GitHub with friends.

---

# Imagine This Scenario

You have a project with two branches:

```text
main  ← Production (important code)
```

Now you want to add a new feature.

Instead of changing `main`, you create another branch.

```text
feature/login
```

You write your code here.

When you're done, you create a **Pull Request (PR)** asking GitHub:

> "Can I merge my `feature/login` branch into `main`?"

Before allowing that, GitHub automatically runs your workflow.

That's exactly what this workflow does.

---

# Line by Line

## Workflow Name

```yaml
name: PR Quality Check
```

This is simply the name you'll see in the **Actions** tab.

---

## Trigger

```yaml
on:
  pull_request:
```

This means:

- Run this workflow whenever someone creates or updates a Pull Request.
- It does **not** run when you push code normally.
- It runs **only** for Pull Requests.

---

## Types

```yaml
types:
  - opened
  - synchronize
```

### `opened`

Runs when someone creates a Pull Request.

Example:

```text
feature/login
      │
      ▼
Create Pull Request
      │
      ▼
Workflow starts
```

### `synchronize`

Suppose you already created the Pull Request.

Then later you add another commit.

```bash
git add .
git commit -m "Fixed login bug"
git push
```

GitHub updates the Pull Request.

This event is called **synchronize**, so the workflow runs again.

---

## Branch

```yaml
branches:
  - main
```

This means:

Run **only** if the Pull Request is trying to merge into `main`.

### ✅ Runs

```text
feature/login
      │
      ▼
main
```

### ❌ Doesn't Run

```text
feature/login
      │
      ▼
develop
```

Because the target branch isn't `main`.

---

## Job

```yaml
jobs:
  pr-checking:
```

A **job** is simply a group of steps.

Think of it like:

```text
Job
 ├── Step 1
 ├── Step 2
 └── Step 3
```

---

## Runner

```yaml
runs-on: ubuntu-latest
```

GitHub creates a fresh Ubuntu virtual machine.

Imagine GitHub saying:

> "I'll give you a temporary Linux computer."

Everything happens on that computer.

When the workflow finishes...

🗑️ GitHub deletes it.

---

## Checkout

```yaml
uses: actions/checkout@v4
```

Suppose GitHub gives you a fresh computer.

It has:

- No project
- No files
- Nothing

The **checkout** action means:

> Download my GitHub repository onto this computer.

Now the runner has your project files and can use them.

---

## Print Branch Name

```yaml
run: |
  echo "PR check running for branch: ${{ github.event.pull_request.head.ref }}"
```

This prints the branch that created the Pull Request.

Example:

You created:

```text
feature/login
```

Output:

```text
PR check running for branch: feature/login
```

---

# What is `head.ref`?

Imagine this Pull Request:

```text
feature/login
       │
       ▼
      main
```

**Source branch:**

```text
feature/login
```

**Destination branch:**

```text
main
```

So,

```yaml
github.event.pull_request.head.ref
```

means:

> "Which branch is this Pull Request coming from?"

Output:

```text
feature/login
```

---

# What is `base.ref`?

```yaml
github.event.pull_request.base.ref
```

means:

> "Which branch is this Pull Request trying to merge into?"

Output:

```text
main
```

---

# Complete Picture

```text
You create a new branch
        │
        ▼
feature/login
        │
        ▼
Write code
        │
        ▼
git push
        │
        ▼
Open Pull Request
        │
        ▼
GitHub detects the PR
        │
        ▼
Runs PR Quality Check workflow
        │
        ▼
Creates Ubuntu Runner
        │
        ▼
Downloads your repository
        │
        ▼
Prints:

PR check running for branch: feature/login
```

---

# Why Do Companies Use This?

Before anyone can merge code into `main`, GitHub can automatically:

- ✅ Run tests
- ✅ Build the project
- ✅ Check code quality
- ✅ Scan for security issues
- ✅ Ensure nothing is broken

If everything passes:

- ✔️ The Pull Request is safe to merge.

If something fails:

- ❌ GitHub blocks the merge until the issue is fixed.

---

# Summary

A Pull Request workflow helps teams automatically verify code before it is merged into the `main` branch. It improves code quality, catches bugs early, and ensures that only tested and validated code reaches production.
