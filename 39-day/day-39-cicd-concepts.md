# Task 1: The Problem

## 1. What can go wrong?

When multiple developers push code and deploy manually, several issues can occur:

* Code conflicts and merge conflicts.
* Developers may overwrite each other's changes.
* Human errors during deployment.
* Forgetting to run tests before deployment.
* Deploying the wrong version of the application.
* Inconsistent deployment steps across team members.
* Increased production downtime due to manual mistakes.
* Difficult rollback if a deployment fails.
* Lack of deployment history and traceability.

---

## 2. What does "It works on my machine" mean, and why is it a real problem?

"It works on my machine" means the application runs correctly on a developer's local computer but fails on another developer's machine, the testing environment, or the production server.

This happens because environments may differ in:

* Operating system
* Software versions
* Installed dependencies
* Environment variables
* Configuration files
* Database versions

It is a real problem because software should behave consistently in every environment. Differences between local and production environments can lead to bugs, failed deployments, and wasted debugging time.

---

## 3. How many times a day can a team safely deploy manually?

Manual deployments are slow and error-prone, so most teams can safely deploy only a few times per day (often 1–5 deployments), depending on the application's complexity and the team's experience.

With automated CI/CD pipelines, deployments can happen dozens or even hundreds of times per day because testing, building, and deployment are automated, consistent, and reliable.


# Continuous Integration, Continuous Delivery, and Continuous Deployment

## Continuous Integration (CI)

Continuous Integration is the practice of frequently merging code changes into a shared repository, often several times a day. Every change automatically triggers a build and tests to detect bugs, integration issues, and code conflicts early.
.Each merge automatically triggers a build and testing sequence. This process catches integration bugs early, verifies code quality, and provides immediate feedback to the developers.
---

## Continuous Delivery (CD)

Unlike CI, which focuses purely on code integration and testing, Continuous Delivery ensures that every passing build is always ready to be manually pushed to production. "Delivery" means the software is automatically built, packaged, and deployed to a staging environment for human approval before release.

## Continuous Deployment

Continuous Deployment goes a step further than Delivery by automating the final release step entirely. Teams use it to automatically push every change that passes all tests straight to the live production environment without any manual intervention.
