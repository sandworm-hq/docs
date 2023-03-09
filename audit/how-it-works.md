# How Sandworm audits your project

Sandworm starts by parsing your app manifest (the content of your `package.json` file), as well as your lockfile (`package-lock.json`, `yarn.lock`, or `pnpm-lock.yaml`, depending on what package manager you use).

{% hint style="info" %}
Make sure that Sandworm runs against the most recent lockfile for your app. The lockfile represents the source-of-truth for information about what packages and what versions are currently in use.
{% endhint %}

Sandworm then builds a standardized dependency graph object, representing the hierarchical structure of all your dependencies, and adds package metadata on top. By default, Sandworm obtains metadata from [npm's registry API](https://github.com/npm/registry/blob/master/docs/REGISTRY-API.md), but an offline alternative exists that's able to retrieve less info, but locally, from the `node_modules` directory.

Multiple types of issue scans are then performed - see [detected issue types](./issue-types.md) for the full list.

## CVE vulnerabilities

Sandworm retrieves **CVE vulnerabilities** via your package manager's command-line tool, by reading the results of `npm audit`, `yarn audit`, or `pnpm audit`. Vulnerabilities come from GitHub's [Advisory Database](https://github.com/advisories). The entire original advisory content is attached to each vulnerability that Sandworm outputs to the JSON report file.

{% hint style="info" %}
Identified vulnerabilities might vary between different package managers.
{% endhint %}

## Licenses

Sandworm verifies **dependency licenses** to be:
- explicitly specified
- valid SPDX
- OSI-approved
- non-deprecated

You can also define a custom [license policy](./license-policies.md) by specifying allowed licenses vs. licenses or categories that should trigger audit issues.

{% hint style="info" %}
If possible, aim to use OSI-approved licenses. The goal of the OSI License Review Process is to ensure that licenses and software labeled as “open source” conform to existing community norms and expectations. Read more about the [license review process](https://opensource.org/approval/).
{% endhint %}

{% hint style="warning" %}
When identifying a package's license, Sandworm currently only looks at the `license` field in the manifest file. Reading package `LICENSE` files is on the roadmap.
{% endhint %}

## Package metadata

**Dependency metadata** is also verified against common indicators of risk or poor quality, such as deprecated packages, or packages with non-registry URLs.
