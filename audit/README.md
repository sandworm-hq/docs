---
description: Beautiful Security & License Compliance Reports For Your App's Dependencies 🪱
---

# Sandworm Audit

## Summary

* Free & open source command-line tool
* Works with any JavaScript package manager
* Scans your project & dependencies for vulnerabilities, license, and misc issues
* Supports custom license policies
* Outputs:
  * JSON issue & license usage reports
  * Easy to grok SVG dependency tree & treemap visualizations
    * Powered by D3
    * Overlays security vulnerabilities
    * Overlays package license info
  * CSV of all dependencies & license info

### Generate a report
![Running Sandworm Audit](https://assets.sandworm.dev/showcase/audit-terminal-output.gif)

### Navigate charts
![Sandworm treemap and tree dependency charts](https://assets.sandworm.dev/showcase/treemap-and-tree.png)

### JSON output
{% code title="report.json" overflow="wrap" lineNumbers="true" %}
```json
{
  "createdAt": "...",
  "packageManager": "...",
  "name": "...",
  "version": "...",
  "rootVulnerabilities": [...],
  "dependencyVulnerabilities": [...],
  "licenseUsage": {...},
  "licenseIssues": [...],
  "metaIssues": [...],
  "errors": [...],
}
```
{% endcode %}

### Get involved

* Have a support question? [Post it here](https://github.com/sandworm-hq/sandworm-audit/discussions/categories/q-a).
* Have a feature request? [Post it here](https://github.com/sandworm-hq/sandworm-audit/discussions/categories/ideas).
* Did you find a security issue? [See SECURITY.md](../contributing/security.md).
* Did you find a bug? [Post an issue](https://github.com/sandworm-hq/sandworm-audit/issues/new/choose).
* Want to write some code? See [CONTRIBUTING.md](../contributing/README.md).

## Install

Install `sandworm-audit` globally via your favorite package manager:

```bash
npm install -g @sandworm/audit
# or yarn global add @sandworm/audit
# or pnpm add -g @sandworm/audit
```

Or directly run without installing via `npx @sandworm/audit`.

## What you get

* A CSV of all dependencies, with license, size, and parent info for each package;
* A treemap chart of all dependencies, to help you visualize your bundle size structure;
* A tree chart of all dependencies, to easily grok hierarchies and identified issues;
* A machine-readable JSON output of all the audit data.

## How Sandworm audits your project

Sandworm starts by parsing your app manifest (the content of your `package.json` file), as well as your lockfile (`package-lock.json`, `yarn.lock`, or `pnpm-lock.yaml`, depending on what package manager you use).

{% hint style="info" %}
Make sure that Sandworm runs against the most recent lockfile for your app. The lockfile represents the source-of-truth for information about what packages and what versions are currently in use.
{% endhint %}

{% hint style="warning" %}
Sandworm doesn't currently support [workspaces](https://docs.npmjs.com/cli/v9/using-npm/workspaces). You need to run Sandworm against the final built manifest and lockfile.
{% endhint %}

Sandworm then builds a standardized dependency graph object, representing the hierarchical structure of all your dependencies, and adds package metadata on top. By default, this metadata is obtained from [npm's registry API](https://github.com/npm/registry/blob/master/docs/REGISTRY-API.md), but an offline alternative exists that's able to retrieve less info, but locally, from the `node_modules` directory.

Multiple types of issue scans are then performed - see [detected issue types](#detected-issue-types) for the full list.
  * **CVE vulnerabilities**, retrieved via your package manager's command-line tool, by reading the results of `npm audit`, `yarn audit`, or `pnpm audit`.

{% hint style="info" %}
Identified vulnerabilities might vary between different package managers.
{% endhint %}

  * All **dependency licenses** are verified to be valid SPDX, OSI-approved, and compatible with the current project license policy, amongst other license-related checks.

{% hint style="warning" %}
When identifying a package's license, Sandworm currently only looks at the `license` field in the manifest file. Reading package `LICENSE` files is on the roadmap.
{% endhint %}

* **Dependency metadata** is also verified against common indicators of risk or poor quality, such as deprecated packages, or packages with non-registry URLs.

## Run Sandworm in the terminal

To use Sandworm Audit as a command-line tool, simply run `sandworm-audit` or `npx @sandworm/audit` in the root directory of your project, or use the `-p` option to indicate the root dir.

```
Options:
      --version               Show version number                      [boolean]
      --help                  Show help                                [boolean]
  -o, --output-path           The path of the output directory, relative to the
                              application path   [string] [default: ".sandworm"]
  -d, --include-dev           Include dev dependencies[boolean] [default: false]
  -v, --show-versions         Show package versions in chart names
                                                      [boolean] [default: false]
  -p, --path                  The path to the application to audit      [string]
      --md, --max-depth       Max depth to represent in charts          [number]
      --ms, --min-severity    Min issue severity to represent in charts [string]
      --lp, --license-policy  Custom license policy JSON string         [string]
  -f, --from                  Load data from "registry" or "disk"
                                                  [string] [default: "registry"]
      --fo, --fail-on         Fail policy JSON string   [string] [default: "[]"]
```

Sandworm also reads configs from a local `.sandworm.config.json` file in the root dir of the app. Here is an example file that includes all of the available configuration fields:

{% code title=".sandworm.config.json" overflow="wrap" lineNumbers="true" %}
```json
{
  "audit": {
    "includeDev": false,
    "showVersions": false,
    "maxDepth": 10,
    "minDisplayedSeverity": "high",
    "licensePolicy": {
      "high": ["cat:Network Protective", "cat:Strongly Protective"],
      "moderate": ["cat:Weakly Protective"],
    },
    "loadDataFrom": "registry",
    "outputPath": ".sandworm",
    "failOn": ["*.critical"],
  }
}
```
{% endcode %}

## License policies

Sandworm uses license policies to determine what sort of issues to raise when scanning your app's dependency licenses. A license policy object links specific license strings or license categories to an issue severity level. Any usage of such licenses will, upon audit, generate a license issue of the specified severity.

The default license policy:
- Generates `high` severity license issues for licenses labeled as `Network Protective` or `Strongly Protective`;
- Generates `moderate` severity license issues for licenses labeled as `Weakly Protective`.

{% hint style="info" %}
Un-categorized licenses always result in a `high` severity error.
{% endhint %}

See Sandworm's built-in [SPDX license database](https://github.com/sandworm-hq/sandworm-audit/blob/main/src/issues/licenses.json) for the full classification breakdown.

{% hint style="danger" %}
While we do our best to keep license info accurate and up-to-date, you should still carefully review all of the terms and conditions of the actual license before using the licensed material. Sandworm isn't a law firm and doesn't provide legal services.
{% endhint %}

You can configure Sandworm to use a custom license policy. The policy object:
- has keys that match one of the supported issues severities: `critical`, `high`, `moderate`, or `low`;
- has values that are arrays;
- each array value is a string that represents:
  - a specific license - for example `MIT`;
  - a category of licenses, prefixed by "cat:" - for example `cat:Network Protective`.

As an example, here is the default license policy that Sandworm applies:
```json
{
  "high": ["cat:Network Protective", "cat:Strongly Protective"],
  "moderate": ["cat:Weakly Protective"],
}
```

To provide a custom license policy, use the `--license-policy` command-line option, or the `audit.licensePolicy` field in the `.sandworm.config.json` configuration file. For example, to generate a critical issue for any dependency using the `MIT` license:

```bash
sandworm-audit --license-policy '{"critical": ["MIT"]}'
```

## Detected issue types

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

## Fail on identified issues

When running in the command line, Sandworm can be configured to fail by exiting with code 1 when identifying specific issue types and/or severities. This makes it easy to integrate Sandworm as a part of your CI or Git hook flow.

To provide fail conditions, use the `--fail-on` command-line option, or the `audit.failOn` field in the `.sandworm.config.json` configuration file. You should provide an array of string conditions. Each condition has a required type and a required severity, joined by a dot. Possible types are `*`, `root`, `dependencies`, `license`, and `meta`. Possible severities are `*`, `critical`, `high`, `moderate`, and `low`. Using these, you can construct fail conditions like:
- `*.*` - fail on any issue;
- `dependencies.*` - fail on any vulnerability identified with the app dependencies;
- `root.*` - fail on any vulnerability identified with the app itself;
- `*.critical` - fail on any critical severity issue.

For example, to fail on any critical or high severity issues:

```bash
sandworm-audit --fail-on='["*.critical", "*.high"]'
```

{% hint style="info" %}
No fail conditions are set by default.
{% endhint %}

{% hint style="info" %}
Sandworm will also exit with code 1 if it encounters any errors that potentially alter the audit result.
{% endhint %}

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
