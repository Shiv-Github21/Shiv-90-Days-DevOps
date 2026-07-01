# GitHub Actions: `working-directory` & `fetch-depth: 0` (Fresher Notes)

## Project Structure

Imagine your project looks like this:

```text
DevBoard/
│
├── frontend/
│   ├── package.json
│   ├── src/
│   └── Dockerfile
│
├── backend/
│   ├── package.json
│   ├── app.js
│   └── Dockerfile
│
└── .github/
    └── workflows/
        └── ci.yml
```

---

# 1. What is `working-directory`?

## Problem

By default, GitHub Actions executes commands from the **root directory** of your project.

Suppose your workflow contains:

```yaml
- name: Install dependencies
  run: npm install
```

GitHub will look for:

```text
DevBoard/package.json
```

But your `package.json` is actually inside:

```text
DevBoard/frontend/package.json
```

So the workflow fails with an error similar to:

```text
npm ERR! package.json not found
```

---

## Solution 1 (Without `working-directory`)

You have to change the directory before every command.

```yaml
- name: Install dependencies
  run: cd frontend && npm install

- name: Run Tests
  run: cd frontend && npm test

- name: Build Application
  run: cd frontend && npm run build
```

Notice how you're repeating:

```bash
cd frontend
```

again and again.

---

## Solution 2 (Using `working-directory`)

Instead of writing `cd frontend` every time, write it once.

```yaml
defaults:
  run:
    working-directory: ./frontend
```

Now your workflow becomes much cleaner.

```yaml
jobs:
  frontend:
    runs-on: ubuntu-latest

    defaults:
      run:
        working-directory: ./frontend

    steps:
      - uses: actions/checkout@v4

      - name: Install Dependencies
        run: npm install

      - name: Run Tests
        run: npm test

      - name: Build Application
        run: npm run build
```

GitHub internally behaves like this:

```bash
cd frontend
npm install

cd frontend
npm test

cd frontend
npm run build
```

You don't have to write `cd frontend` every time.

---

## Real-Life Analogy

Imagine you're working in an office.

Your manager says:

> "Today, all your work is in **Room 101**."

Instead of walking to Room 101 before every task, you simply stay there for the entire day.

That's exactly what `working-directory` does.

---

# 2. What is `fetch-depth: 0`?

When GitHub checks out your repository, it **does not download the complete Git history by default**.

By default:

```yaml
- uses: actions/checkout@v4
```

is equivalent to:

```yaml
- uses: actions/checkout@v4
  with:
    fetch-depth: 1
```

This downloads only the latest commit.

---

## Example Repository

Suppose your repository contains:

```text
Commit 1
Commit 2
Commit 3
Commit 4
Commit 5 (Latest)
```

---

### Default Checkout (`fetch-depth: 1`)

GitHub downloads only:

```text
Commit 5
```

This is faster because it downloads less data.

---

### Full Checkout (`fetch-depth: 0`)

```yaml
- uses: actions/checkout@v4
  with:
    fetch-depth: 0
```

Now GitHub downloads:

```text
Commit 1
Commit 2
Commit 3
Commit 4
Commit 5
```

The complete Git history is available.

---

# Why use `fetch-depth: 0`?

Some tools need access to previous commits.

Examples include:

- SonarQube
- semantic-release
- Automatic versioning
- Changelog generation
- `git log`
- `git describe`
- `git blame`
- Comparing commits
- Release automation

If only the latest commit is available, these tools may fail or produce incorrect results.

---

## Real-Life Analogy

Imagine someone gives you a book.

### `fetch-depth: 1`

You receive only the **last page**.

You know how the story ends but not how it got there.

### `fetch-depth: 0`

You receive the **entire book**.

Now you understand the complete story.

---

# Complete Example

```yaml
name: Frontend CI

on:
  workflow_dispatch:

jobs:
  frontend:
    runs-on: ubuntu-latest

    defaults:
      run:
        working-directory: ./frontend

    steps:
      - name: Checkout Code
        uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 22

      - name: Install Dependencies
        run: npm ci

      - name: Run Tests
        run: npm test

      - name: Build Application
        run: npm run build
```

---

# Interview Questions

## Q1. What is `working-directory`?

**Answer:**

`working-directory` tells GitHub Actions which folder should be used for all `run` commands. It avoids writing `cd <folder>` before every command and makes workflows cleaner and easier to maintain.

---

## Q2. What is `fetch-depth: 0`?

**Answer:**

By default, `actions/checkout` downloads only the latest commit (`fetch-depth: 1`). Setting `fetch-depth: 0` downloads the complete Git history. This is useful for tools like SonarQube, semantic-release, changelog generation, and Git commands that require previous commits.

---

# Quick Revision

| Feature | Meaning |
|---------|---------|
| `working-directory` | Sets the default folder for all `run` commands. |
| Default directory | Repository root folder. |
| `fetch-depth: 1` | Downloads only the latest commit (default). |
| `fetch-depth: 0` | Downloads the complete Git history. |
| Benefit of `working-directory` | Avoids repeating `cd <folder>` in every step. |
| Benefit of `fetch-depth: 0` | Enables tools that need the full Git history. |

---

# Easy Trick to Remember

✅ **working-directory = Where should my commands run?**

✅ **fetch-depth: 0 = Download the complete Git history.**

Think of it like this:

- **working-directory** → "Which room am I working in?"
- **fetch-depth: 0** → "Give me the entire book, not just the last page."
