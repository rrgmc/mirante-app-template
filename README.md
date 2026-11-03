# Mirante app template

Make your own build of [Mirante](https://github.com/rrgmc/mirante), a personal desktop dashboard, with the modules you choose. This repository holds only a manifest, `mirante.json`, and the few files that build it. GitHub Actions builds, tests and packages your app for Windows, macOS and Linux, and publishes each release.

Custom builds are on trial. Until the trial ends, the official app stays [`mirante`](https://github.com/rrgmc/mirante), and this template may change.

## Making your app

1. Choose **Use this template** on GitHub, and make your repository from it.
2. In `LICENSE`, put your own name, or choose another licence. Your modules decide what you may choose: see [licences](#licences).
3. In `mirante.json`, list the modules you want. See [the manifest](#the-manifest).
4. In `CMakeLists.txt`, set `MIRANTE_VERSION` to your first version, such as `0.1.0`.
5. Set up the token for updates. See [updates](#updates).
6. Protect your default branch, so that changes reach it only through pull requests whose checks pass: in **Settings > Rules**, require the checks `build / build (windows-2022)`, `build / build (macos-15)` and `build / build (ubuntu-24.04)`.

Each push to the default branch that raises `MIRANTE_VERSION` publishes the release `vX.Y.Z`, with its packages. Pushing a tag `vX.Y.Z-rc.N` publishes a prerelease.

## The manifest

```json
{
  "qt": ["Pdf", "PdfWidgets"],
  "core": { "repo": "https://github.com/rrgmc/mirante-core", "ref": "<tag or commit>" },
  "modules": [
    {
      "name": "mirante_modules",
      "repo": "https://github.com/rrgmc/mirante-modules",
      "ref": "<tag or commit>",
      "folders": ["github", "web"]
    }
  ]
}
```

- **`core`** is the core of Mirante. Its `ref` is also in `.github/workflows/build.yml` and `update.yml`, and the build fails if they differ. The update job changes all of them.
- **`modules`** lists repositories of modules. Each has a `name` of your choice, in lowercase letters, digits and underscores, the repository, a `ref`, and the `folders` to build. Each folder holds one module. `"."` is the top of a repository that holds one module.
- **`qt`** lists the Qt modules that your modules need beyond what the core needs. Only the `document` module needs any: `Pdf` and `PdfWidgets`. Without it, the list can be empty.
- A **`ref`** is a tag, such as `v0.7.0`, or a whole commit hash. Pin a tag to take only releases. Pin a commit to take every change on the default branch.

The first-party modules are in [`mirante-modules`](https://github.com/rrgmc/mirante-modules). Its README lists what each one does.

## Updates

Once a week, `.github/workflows/update.yml` looks for newer versions of each pinned repository. If it finds any, it opens a pull request that moves the pins, with a link to each difference, and raises the patch number of `MIRANTE_VERSION`. CI builds it. Read the differences, then merge it, and the release is published. If CI fails, nothing is published, and the last release stays.

A pull request opened with the workflow's own token starts no CI, so the job needs a token of yours:

1. On GitHub, open **Settings > Developer settings > Personal access tokens > Fine-grained tokens**, and choose **Generate new token**.
2. Give it access to this repository only, with these permissions: **Contents**, **Pull requests** and **Workflows**, each **Read and write**.
3. In this repository, open **Settings > Secrets and variables > Actions**, and add it as the secret `MIRANTE_UPDATE_TOKEN`.

The token expires on the date you chose, and the job then fails. Make a new one and replace the secret. Without the secret, the job only warns. You can start it by hand from the **Actions** tab, under **Update**.

## Building on your computer

You need Qt 6.8 with the modules the core lists, CMake and Ninja, and on Windows MSVC 2022. [Building with modules](https://github.com/rrgmc/mirante-core/blob/master/BUILDING.md#building-with-modules) in the core says how.

```sh
cmake --preset dev -DCMAKE_PREFIX_PATH=<Qt folder>
cmake --build --preset dev
ctest --preset dev
```

## What you should know

- **Modules are trusted code.** A module runs inside the app with everything the app can reach, including your tokens and web logins. Add only modules whose authors you trust, and read their code when you change their pin. Each build lists, on its run's summary page, which modules use the keychain, the network, processes and web storage. See [reviewing a module](https://github.com/rrgmc/mirante-core/blob/master/docs/modules.md#reviewing-a-module).
- **Your packages are not signed.** Windows SmartScreen warns when they are first opened, and macOS refuses the app until you allow it in **System Settings > Privacy & Security**. The macOS package is signed ad hoc only.
- **Every build is the same app,** with the same name and settings. Two builds on one computer share their settings, workspaces and tokens. **Help > About** lists the modules of a build, with their repositories and commits.

## Licences

The core and the first-party modules are released under the MIT License. A module from someone else has its own licence. Your app must meet the terms of each, so read them before you add a module.

## Writing a module

[Writing a module](https://github.com/rrgmc/mirante-core/blob/master/docs/writing-a-module.md) in the core says how to make one in a repository of its own.
