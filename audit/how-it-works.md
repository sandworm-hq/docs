# How Sandworm audits your project

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