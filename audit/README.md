---
description: Beautiful Security & License Compliance Reports For Your App's Dependencies 🪱
---

# Sandworm Audit

### Summary

* Free & open source command-line tool
* Works with any JavaScript package manager
* Scans your project & dependencies for vulnerabilities, license, and misc issues
* Outputs:
  * JSON issue & license usage reports
  * Easy to grok SVG dependency tree & treemap visualizations
    * Powered by D3
    * Overlays security vulnerabilities
    * Overlays package license info
  * CSV of all dependencies & license info

```
Sandworm 🪱
Security and License Compliance Audit
✔ Built dependency graph
✔ Got vulnerabilities
✔ Scanned licenses
✔ Tree chart done
✔ Treemap chart done
✔ CSV done
✨ Done
```

![Sandworm Treemap and Tree Dependency Charts](https://assets.sandworm.dev/showcase/treemap-and-tree.png)

### Get involved

* Have a support question? [Post it here](https://github.com/sandworm-hq/sandworm-audit/discussions/categories/q-a).
* Have a feature request? [Post it here](https://github.com/sandworm-hq/sandworm-audit/discussions/categories/ideas).
* Did you find a security issue? [See SECURITY.md](../contributing/security.md).
* Did you find a bug? [Post an issue](https://github.com/sandworm-hq/sandworm-audit/issues/new/choose).
* Want to write some code? See [CONTRIBUTING.md](../contributing/README.md).

## Install

```bash
npm install -g @sandworm/audit
# or yarn global add @sandworm/audit
# or pnpm add -g @sandworm/audit
```

## What you get

* A CSV of all dependencies, with license, size, and parent info for each package;
* A treemap chart of all dependencies, to help you visualize your bundle size structure;
* A tree chart of all dependencies, to easily grok hierarchies and identified issues;
* A machine-readable JSON output of all the audit data.

## How Sandworm audits your project

Sandworm starts by parsing your app manifest (the content of your `package.json` file), as well as your lockfile (`package-lock.json`, `yarn.lock`, or `pnpm-lock.yaml`, depending on what package manager you use).

> **Note**
> Make sure that Sandworm runs against the most recent lockfile for your app. The lockfile represents the source-of-truth for information about what packages and what versions are currently in use.

> **Warning**
> Sandworm doesn't currently support [workspaces](https://docs.npmjs.com/cli/v9/using-npm/workspaces). You need to run Sandworm against the final built manifest and lockfile.

Sandworm then builds a standardized dependency graph object, representing the hierarchical structure of all your dependencies, and adds package metadata on top. By default, this metadata is obtained from [npm's registry API](https://github.com/npm/registry/blob/master/docs/REGISTRY-API.md), but an offline alternative exists that's able to retrieve less info, but locally, from the `node_modules` directory.

Multiple types of issue scans are then performed:
  * **CVE vulnerabilities**, retrieved via your package manager's command-line tool, by reading the results of `npm audit`, `yarn audit`, or `pnpm audit`.

> **Note**
> Identified vulnerabilities might vary between different package managers.

  * All **dependency licenses** are verified to be valid SPDX, OSI-approved, and compatible with the current project license policy, amongst other license-related checks.

> **Warning**
> When identifying a package's license, Sandworm currently only looks at the `license` field in the manifest file. Reading package `LICENSE` files is on the roadmap.

* **Dependency metadata** is also verified against common indicators of risk or poor quality, such as deprecated packages, or packages with non-registry URLs.

## Run Sandworm in the terminal

To use Sandworm Audit as a command-line tool, simply run `sandworm-audit` in the root directory of your project, or use the `-p` option to indicate the root dir.

```
Options:
      --version          Show version number                           [boolean]
      --help             Show help                                     [boolean]
  -o, --output           The name of the output directory, relative to the
                         application path        [string] [default: ".sandworm"]
  -d, --include-dev      Include dev dependencies     [boolean] [default: false]
  -v, --show-versions    Show package versions        [boolean] [default: false]
  -p, --path             The application path    [string] [default: current dir]
      --md, --max-depth  Max depth to represent                         [number]
```

## Detected issues types

| Type | Issue | Severity |
|---|---|---|
| Vulnerability | All CVEs | Same as CVE severity |
| License | No license specified | `critical` |
| License | Not licensed for use | `critical` |
| License | License not OSI approved | `low` |
| License | License is deprecated | `low` |
| License | Atypical license | `high` |
| License | Invalid SPDX license | `high` |
| License | Custom license expression | `high` |
| License | License policy violations | Policy severity |
| Meta | Deprecated package | `high` |
| Meta | Uses install scripts | `high` |
| Meta | Has no repository | `moderate` |
| Meta | Has HTTP dependency | `critical` |
| Meta | Has GIT dependency | `critical` |
| Meta | Has file dependency | `moderate` |

## Chart types

### Treemap
![Sandworm Treemap](https://assets.sandworm.dev/showcase/treemap-snip.png)
* [Sample treemap for Express@4.18.2](https://assets.sandworm.dev/charts/npm/express/4.18.2/treemap.svg)
* Node colors represent the dependency depth;
* Node surface represents the size of the corresponding directory under `node_modules`;
* A dotted pattern in a node background means the package is a shared dependency, required by multiple packages, and present multiple times in the chart;
* Shared dependency sizes are added to every dependent package, to represent the independent size structure properly; hence, the displayed size might be larger than the actual size on disk;
* A red package background means the package has direct vulnerabilities;
* A purple package background means the package depends on other vulnerable packages;
* Click on a node to make the tooltip persist; click outside to close it;
* When representing deep dependencies, the surface area of certain packages might reach zero, making them invisible.

### Tree
![Sandworm Tree](https://assets.sandworm.dev/showcase/tree-snip.png)
* [Sample tree for Express@4.18.2](https://assets.sandworm.dev/charts/npm/express/4.18.2/tree.svg)
* Nodes are grouped by color based on the root dependency that they belong to;
* Red text in a package name means the package has direct vulnerabilities;
* Purple text in a package name means the package depends on other vulnerable packages;
* Click on a node to make the tooltip persist; click outside to close it;
* By default, the tree chart has a maximum depth of 7, meaning only seven levels of dependencies get represented, to keep the output readable; you can override this using the `--md` option.

## Beta: visualizations on sandworm.dev

Simple HTML visualizations on top of Sandworm data for all existing npm packages are available in beta on [sandworm.dev](https://sandworm.dev). Here are a few links to get you exploring:

* [Apollo Client](https://sandworm.dev/npm/package/apollo-client)
* [AWS SDK](https://sandworm.dev/npm/package/aws-sdk)
* [Express](https://sandworm.dev/npm/package/express)
* [Mocha](https://sandworm.dev/npm/package/mocha)
* [Mongoose](https://sandworm.dev/npm/package/mongoose)
* [Nest.js](https://sandworm.dev/npm/package/@nestjs/cli)
* [Redis](https://sandworm.dev/npm/package/redis)
