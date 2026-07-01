# Real-Life Production Issue: `--legacy-peer-deps`

## Scenario

A company has a React application deployed through a GitHub Actions
CI/CD pipeline.

Every time a developer pushes code, the pipeline runs:

``` bash
npm ci
```

For months, everything works without issues.

One day, a developer upgrades a frontend package such as Material UI.

The pipeline suddenly fails with:

``` text
npm ERR! ERESOLVE unable to resolve dependency tree
```

## What Happened?

The project is using **React 18**, but the updated package now expects
**React 19** as a peer dependency.

Example:

-   Project: React 18
-   Updated Package: Requires React 19

Since npm (v7+) strictly checks peer dependencies, it stops the
installation.

## Production Impact

Because `npm ci` fails:

-   Docker image is **not built**
-   Docker image is **not pushed**
-   Kubernetes deployment **does not happen**
-   The production release is blocked

Although the application code is correct, users cannot receive the
latest bug fixes or features.

## Temporary Fix

To unblock the release, the DevOps engineer changes:

``` bash
npm ci
```

to

``` bash
npm ci --legacy-peer-deps
```

This tells npm to ignore peer dependency conflicts and continue
installing packages.

The pipeline succeeds, allowing the deployment to continue.

## Permanent Fix

`--legacy-peer-deps` is only a temporary workaround.

The development team should:

-   Update package versions so they are compatible.
-   Downgrade the conflicting package if necessary.
-   Remove `--legacy-peer-deps` once dependencies are fixed.

## Interview Answer

> In a production CI/CD pipeline, a dependency update introduced a peer
> dependency conflict. As a result, `npm ci` failed with an `ERESOLVE`
> error, blocking Docker image creation and deployment. To restore the
> release pipeline quickly, we temporarily used
> `npm ci --legacy-peer-deps`. The long-term solution was to update the
> project's dependencies so that all peer dependencies were compatible.
