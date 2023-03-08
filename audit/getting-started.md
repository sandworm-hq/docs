# Getting started

## Install

{% hint style="info" %}
Sandworm Audit requires Node 14.19+.
{% endhint %}
{% hint style="info" %}
When using npm, Sandworm Audit supports lockfile versions 2 and 3 (npm 7+).
{% endhint %}

Install `sandworm-audit` globally via your favorite package manager:

```bash
npm install -g @sandworm/audit
# or yarn global add @sandworm/audit
# or pnpm add -g @sandworm/audit
```

Or directly run without installing via `npx @sandworm/audit@latest`.

## Run Sandworm in the terminal

To use Sandworm Audit as a command-line tool, simply run `sandworm-audit` or `npx @sandworm/audit@latest` in the root directory of your project, or use the `-p` option to indicate the root dir.

```
Options:
      --version               Show version number                      [boolean]
      --help                  Show help                                [boolean]
  -o, --output-path           The path of the output directory, relative to the
                              application path    [string] [default: "sandworm"]
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
    "outputPath": "sandworm",
    "failOn": ["*.critical"],
  }
}
```
{% endcode %}