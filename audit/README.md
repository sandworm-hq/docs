---
description: Beautiful Security & License Compliance Reports For Your App's Dependencies 🪱
---

# Sandworm Audit

## Summary

* Free & open source command-line tool
* Works with any modern JavaScript package manager
* Scans your project & dependencies for vulnerabilities, license, and misc issues
* Supports custom license policies
* Configurable conditions to fail CI / GIT hook workflows
* Outputs:
  * A CSV of all dependencies, with license, size, and parent info for each package;
  * An SVG treemap chart of all dependencies, to help you visualize your bundle size structure;
  * An SVG tree chart of all dependencies, to easily grok hierarchies and identified issues;
  * A machine-readable JSON output of all the audit data, issue & license usage reports.

### Generate a report
![Running Sandworm Audit](https://assets.sandworm.dev/showcase/audit-terminal-output.gif)

### Navigate charts
![Sandworm treemap and tree dependency charts](https://assets.sandworm.dev/showcase/treemap-and-tree.png)

### CSV output
![Sandworm dependency CSV](https://assets.sandworm.dev/showcase/csv-snip.png)

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

## Beta: visualizations on sandworm.dev

Simple HTML visualizations on top of Sandworm data for all existing npm packages are available in beta on [sandworm.dev](https://sandworm.dev). Here are a few links to get you exploring:

* [Apollo Client](https://sandworm.dev/npm/package/apollo-client)
* [AWS SDK](https://sandworm.dev/npm/package/aws-sdk)
* [Express](https://sandworm.dev/npm/package/express)
* [Mocha](https://sandworm.dev/npm/package/mocha)
* [Mongoose](https://sandworm.dev/npm/package/mongoose)
* [Nest.js](https://sandworm.dev/npm/package/@nestjs/cli)
* [Redis](https://sandworm.dev/npm/package/redis)
