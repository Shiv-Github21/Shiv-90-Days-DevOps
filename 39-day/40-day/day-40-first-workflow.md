# GitHub Actions Workflow Explanation (Beginner Friendly)

This workflow demonstrates the basics of GitHub Actions using **two jobs**. It can be triggered manually from the GitHub Actions page using `workflow_dispatch`.

---

## Workflow

```yaml
name: hello-workflow

on:
  workflow_dispatch:

jobs:
  hello-workflow:
    runs-on: ubuntu-latest
    steps:
      - name: checkout-code
        uses: actions/checkout@v4

      - name: intro
        run: echo "Hello from GitHub Actions"

  update-workflow:
    runs-on: ubuntu-latest
    steps:
      - name: checkout/cloning
        uses: actions/checkout@v4

      - name: curr-date-time
        run: echo "Formatted date is $(date +'%Y-%m-%d %H:%M:%S')"

      - name: Print the branch name
        run: echo "The triggering branch is ${{ github.ref_name }}"

      # Uncomment the following step to intentionally break the pipeline
      # - name: creating a error to break pipeline
      #   run: exit 1

      - name: Print and Check OS
        run: |
          echo "The runner OS is $RUNNER_OS"

          if [ "$RUNNER_OS" == "Linux" ]; then
            echo "This is a Linux runner"
          elif [ "$RUNNER_OS" == "Windows" ]; then
            echo "This is a Windows runner"
          elif [ "$RUNNER_OS" == "macOS" ]; then
            echo "This is a macOS runner"
          fi
```

---

# Explanation of the Workflow

## Workflow Name

```yaml
name: hello-workflow
```

This is the name displayed in the **GitHub Actions** tab.

---

## Trigger

```yaml
on:
  workflow_dispatch:
```

This workflow is triggered **manually**. To run it:

1. Go to your GitHub repository.
2. Open the **Actions** tab.
3. Select **hello-workflow**.
4. Click **Run workflow**.

---

# Job 1: `hello-workflow`

```yaml
hello-workflow:
  runs-on: ubuntu-latest
```

This job runs on GitHub's latest Ubuntu virtual machine.

### Step 1: Checkout Repository

```yaml
- name: checkout-code
  uses: actions/checkout@v4
```

Downloads your repository code onto the GitHub runner so it can be used in later steps.

### Step 2: Print a Message

```yaml
- name: intro
  run: echo "Hello from GitHub Actions"
```

Prints the message:

```
Hello from GitHub Actions
```

to the workflow logs.

---

# Job 2: `update-workflow`

This job also runs on an Ubuntu runner and demonstrates useful built-in variables.

### Step 1: Checkout Repository

```yaml
- name: checkout/cloning
  uses: actions/checkout@v4
```

Clones your repository onto the runner.

---

### Step 2: Print Current Date and Time

```yaml
- name: curr-date-time
  run: echo "Formatted date is $(date +'%Y-%m-%d %H:%M:%S')"
```

Displays the current date and time in the format:

```
YYYY-MM-DD HH:MM:SS
```

---

### Step 3: Print Branch Name

```yaml
- name: Print the branch name
  run: echo "The triggering branch is ${{ github.ref_name }}"
```

Prints the name of the branch that triggered the workflow.

Example:

```
The triggering branch is main
```

---

### Step 4: Break the Pipeline (Optional)

```yaml
# - name: creating a error to break pipeline
#   run: exit 1
```

If you uncomment these lines, the workflow will intentionally fail.

This is useful for learning how to:
- Identify failed workflows.
- Read error logs.
- Debug GitHub Actions pipelines.

---

### Step 5: Check Runner Operating System

```yaml
- name: Print and Check OS
```

This step prints the operating system of the GitHub runner.

Possible outputs include:

- Linux
- Windows
- macOS

Since the workflow uses:

```yaml
runs-on: ubuntu-latest
```

the output will be:

```
The runner OS is Linux
This is a Linux runner
```

---

# Workflow Summary

```
Run Workflow (Manual)
          │
          ▼
Job 1: hello-workflow
          │
          ├── Checkout Repository
          └── Print "Hello from GitHub Actions"

          │
          ▼

Job 2: update-workflow
          │
          ├── Checkout Repository
          ├── Print Current Date & Time
          ├── Print Branch Name
          ├── (Optional) Break Pipeline
          └── Print Runner Operating System
```

---

# Learning Outcomes

After completing this workflow, you will understand:

- How to create a GitHub Actions workflow.
- How to trigger workflows manually.
- How jobs and steps work.
- How to use GitHub Actions.
- How to print environment variables.
- How to intentionally break and debug a pipeline.
- How to inspect workflow logs and identify errors.
