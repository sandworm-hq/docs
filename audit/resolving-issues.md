# Marking issues as resolved

Not all vulnerabilities and issues you discover with Sandworm are addressable by code alone. You may want to mark an issue as resolved when:

* A fix has already been started
* There is no bandwidth to work on a fix right now
* The risk is tolerable for the project
* The issue is inaccurate or incorrect
* The vulnerable code is not actually used

When running an audit, Sandworm looks for a `resolved-issues.json` file in the root directory of the app being audited. The resolved issues file should contain a json array of objects, each object describing a resolved issue.

A resolved issue is described by the following attributes:
* `id` - the GitHub advisory id or the Sandworm issue id
* `paths` - the current dependency tree paths to affected package versions
* `notes` - resolution notes

Here's an example resolved issues file:

{% code title="resolved-issues.json" overflow="wrap" lineNumbers="true" %}
```json
[
  {
    "id": "GHSA-36jr-mh4h-2g58",
    "paths": [
      "d3-node>d3>d3-brush>d3-interpolate>d3-color",
      "d3-node>d3>d3-brush>d3-transition>d3-color",
      "d3-node>d3>d3-brush>d3-transition>d3-interpolate>d3-color",
      "d3-node>d3>d3-color",
      "d3-node>d3>d3-interpolate>d3-color",
      "d3-node>d3>d3-scale>d3-interpolate>d3-color",
      "d3-node>d3>d3-scale-chromatic>d3-color",
      "d3-node>d3>d3-scale-chromatic>d3-interpolate>d3-color",
      "d3-node>d3>d3-transition>d3-color",
      "d3-node>d3>d3-transition>d3-interpolate>d3-color",
      "d3-node>d3>d3-zoom>d3-interpolate>d3-color",
      "d3-node>d3>d3-zoom>d3-transition>d3-color",
      "d3-node>d3>d3-zoom>d3-transition>d3-interpolate>d3-color"
    ],
    "notes": "Vulnerable code not actually used"
  }
]
```
{% endcode %}

{% hint style="success" %}
Commit the `resolved-issues.json` file to your repository for full historical visibility into who resolved what & when.
{% endhint %}

{% hint style="success" %}
For longer resolution notes, we suggest creating a markdown file in a dedicated directory in your repository, and pointing to that file in the resolution note.
{% endhint %}

## Using paths 
Paths help segment the packages affected by an issue into multiple categories. Paths are used to provide granular resolutions, and also help identify potentially new uses of vulnerable package versions.

Let's say your app depends on `packageA` and `packageB`, who both depend on `packageC`. A vulnerability is identified in `packageC`. After analysis, you conclude that:
- `packageA` doesn't use the `packageC` functions exposing the vulnerability;
- `packageB` does use a vulnerable function in `packageC`, but properly escapes user input so the vulnerability is avoided.

You'll want to create two resolutions: one for path `packageA>packageC` and one for path `packageB>packageC`:

{% code title="resolved-issues.json" overflow="wrap" lineNumbers="true" %}
```json
[
  {
    "id": "GHSA-one",
    "paths": [
      "packageA>packageC",
    ],
    "notes": "Vulnerable code not actually used"
  },
  {
    "id": "GHSA-one",
    "paths": [
      "packageB>packageC",
    ],
    "notes": "User input is properly escaped by `packageB` in `src/generate.js`"
  }
]
```
{% endcode %}

Now let's say you install `packageD`, and that too turns out to depend on a vulnerable version of `packageC`. Sandworm's audit would label this as a new issue, since there's no resolved path that matches the new `packageD>packageC`. To mark this as fixed, either:

* Add the `packageD>packageC` path to one of the existing resolutions, if the same notes apply to the new situation;
* Or, create a new resolution, specific for the `packageD>packageC` path.

## Using the command-line tool

![Running Sandworm Audit](https://assets.sandworm.dev/showcase/resolve-terminal-output.gif)

Instead of manually managing the `resolved-issues.json` file, you can use `sandworm resolve [issueId]`. The command-line tool makes a number of validations, and walks you through creating a new resolution or attaching a new path to an existing resolution.

The output of `sandworm audit` includes issue ids that you can `resolve`:

![](https://user-images.githubusercontent.com/5381731/224848738-1ea79289-be5d-40ad-bfd0-6b8828839905.png)

After running `sandworm resolve [issueID]` on one of the detected issues, you specify one or more paths to address:

![](https://user-images.githubusercontent.com/5381731/224849330-226ef881-ffbf-4819-ba32-e434c8358f60.png)

If any existing resolutions share the same issue id, `resolve` prompts you to either create a new resolution, or attach the new paths to an existing one:

![](https://user-images.githubusercontent.com/5381731/224849436-7517261a-8697-42f5-a454-8a781f562add.png)

## Using version wildcards in issue ids

Sandworm-generated issues [contain the package version in their ids](./issue-types.md#sandworm-issue-ids). This is to encourage developers to revisit security issues each time a dependency is updated.

This can, however, become tedious when working with internal or otherwise fully trusted packages. For such scenarios, when resolving issues via the JSON file or the CLI, you can use a wildcard for the version in the Sandworm issue id, like:

```bash
sandworm resolve "SWRM-102-azure-common-*"
```

The wildcard in the issue id matches **any current or future instance** of the Sandworm error with code 102 (license is not OSI approved) for the package named `azure-common`.

{% hint style="danger" %}
If you don't fully trust a dependency package, don't use wildcards for its resolved issue ids. Doing so is dangerous, and can leave the door open for certain types of security issues.

Imagine, for example, that you've resolved the `SWRM-102-package-name-0.9.10` issue, triggered because `package-name@0.9.10` uses `Apache 2.0` as a license, which isn't an OSI approved string. But then you update, and get a new `SWRM-102-package-name-0.9.11` issue, that has the same underlying cause as before, but needs to be marked as resolved again.

So you use `SWRM-102-package-name-*` to future-proof your audits against this issue. However, along comes `package-name@0.10.0` that changes the license string from `Apache 2.0` to `AGPL`, which is still not an OSI approved string. The core of the issue has changed, but the issue id is the same and matches your resolution wildcard. A significant change that requires attention could slip by unnoticed this way.
{% endhint %}
