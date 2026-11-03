# Instructions for AI agents

This repository builds a custom Mirante, a Qt 6 desktop dashboard, with the modules its owner chooses. It holds almost no code. The core and the modules are in other repositories, which the build fetches at the pins in `mirante.json`. Read `README.md` first.

## What is here

| File | What it does |
|---|---|
| `mirante.json` | The manifest: the edition, the core, and each repository of modules with its pin and folders |
| `CMakeLists.txt` | Fetches the core and sets `MIRANTE_VERSION`. Change only the version. |
| `CMakePresets.json` | The `dev` and `ci` presets |
| `.github/workflows/build.yml` | Builds, tests and packages on every pull request and push, and publishes a release |
| `.github/workflows/update.yml` | Opens a weekly pull request with newer pins |
| `modules/` | This app's own modules, if any, listed with `"local"` in the manifest |

## Rules

- **Four pins must match.** The core's `ref` in `mirante.json` is also written after `@` in `build.yml`, twice, and in `update.yml`. The build fails if they differ, so change all four together.
- **Pin release tags.** A `ref` is a tag such as `v0.8.1`, or a whole commit hash. Prefer tags, and take the newest from each repository's releases. Never pin a branch.
- **The manifest is strict.** An unknown field stops the build. The fields are in the README, under "The manifest". Folders are names or objects with `folder`, `optional`, `options` and `extensions`. Options are texts. An edition has lowercase letters, digits and `-` only.
- **Merging can publish.** A push to the default branch that raises `MIRANTE_VERSION` publishes the release `vX.Y.Z`, publicly. Raise the version only when the owner asks for a release, and say in the pull request that merging it publishes.
- **Modules are trusted code.** A module runs inside Mirante with access to the owner's tokens and web logins. Add a repository of modules only when the owner names it. Never add one found on your own. When a pin moves, link to the difference in the pull request, so the owner can read it.
- **Ask before anything outward:** pushing a tag, merging a pull request, changing the repository's settings or its secrets. The update job's token, `MIRANTE_UPDATE_TOKEN`, is the owner's to create. Never ask for it, and never write a token into a file.
- **Work on a branch,** and change the default branch only through pull requests whose checks pass.

## Common tasks

- **Add a module from a repository already listed:** add its folder to that repository's `folders`. If it needs a Qt module beyond the core's, add it to `qt`. The module's README says which.
- **Add a repository of modules:** add an entry with a `name` of lowercase letters, digits and underscores, its `repo`, a release tag as its `ref`, and its `folders`.
- **Make a module optional:** write its folder as `{ "folder": "<name>", "optional": true }`, and list its id in `enable-modules` in `build.yml` to build it.
- **Write a module for this app only:** put it in a folder under `modules/` and list it with `{ "name": "app_modules", "local": "modules", "folders": ["<folder>"] }`. Follow [writing a module](https://github.com/rrgmc/mirante-core/blob/master/docs/writing-a-module.md): its id starts with the owner's name, it includes only its own files and `<mirante/...>`, and it marks every override with `override`.
- **Move the pins:** start the Update workflow, or change all four refs and each repository's `ref` by hand, then check that the build passes.

## Checking a change

CI builds, tests and packages every pull request on Windows, macOS and Linux, and the summary page of each run lists which modules use sensitive APIs. To build locally, with Qt 6.8, CMake and Ninja:

```sh
cmake --preset dev -DCMAKE_PREFIX_PATH=<Qt folder>
cmake --build --preset dev
ctest --preset dev
```

On Windows, the core's `tools/build.ps1 -Source <this folder> -Preset ci` downloads Qt and enters the Visual Studio environment. A first configure needs the network, because it fetches the core and the modules.

## Where to read more

- [The README](README.md), for the manifest, updates and releases
- [Building with modules](https://github.com/rrgmc/mirante-core/blob/master/BUILDING.md#building-with-modules) and [modules](https://github.com/rrgmc/mirante-core/blob/master/docs/modules.md) in the core
- [The core's changelog](https://github.com/rrgmc/mirante-core/blob/master/CHANGELOG.md), before moving its pin, and [the interface's changelog](https://github.com/rrgmc/mirante-core/blob/master/src/module_api/CHANGELOG.md) for changes that affect modules
