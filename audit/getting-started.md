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

You can also directly run without installing via `npx @sandworm/audit@latest`.

## Run Sandworm in the terminal

To use Sandworm Audit as a command-line tool, simply run `sandworm-audit` or `npx @sandworm/audit@latest` in the root directory of your app, or use the `-p` option to point to the root dir.

{% hint style="warning" %}
The app root directory should contain a manifest file (`package.json`) and a lockfile (`package-lock.json`, `yarn.lock`, or `pnpm-lock.yaml`).
{% endhint %}

You can use a [configuration file](./configuration.md), or you can pass configuration options directly to the command-line tool. Here are the available options, that you can also list by running `sandworm-audit --help`:

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

Generating a report can sometimes take a while, depending on how many direct and transient dependencies your app has in total. Sandworm fetches details about each individual dependency from the registry, so network conditions and registry availability are factors that can influence the total audit duration.

After completing a report, Sandworm:
- Outputs a summary of the identified issues to the console, as well as a list of them sorted by severity;
- Writes the following audit artefacts in the `sandworm` directory (or your custom output path):
  - `NAME@VERSION-dependencies.csv`
  - `NAME@VERSION-report.json`
  - `NAME@VERSION-tree.svg`
  - `NAME@VERSION-treemap.svg`
- Exits with an error, if a fail policy is set and the fail conditions are met.
