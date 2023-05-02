# Configure Sandworm Audit

Sandworm reads configurations from a local `.sandworm.config.json` file in the root directory of the app being audited. Here is an example file that includes all of the available configuration fields:

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
      "moderate": ["cat:Weakly Protective"]
    },
    "loadDataFrom": "registry",
    "outputPath": "sandworm",
    "failOn": ["*.critical"]
  }
}
```
{% endcode %}

{% hint style="warning" %}
Note that all configs need to go under the `audit` root key, and not directly in the root of the json file.
{% endhint %}

| Option | Default | Description |
|---|---|---|
| `includeDev` | `false` | Also include dev dependencies in the audit. Note that this might make audits take noticeably longer, as a lot more dependency data needs to be retrieved from the registry. |
| `showVersions` | `false` | Should tree and treemap chart node titles also include the represented package version. Version info is also available by hovering the node. |
| `maxDepth` | 7 | The maximum depth to represent in tree and treemap charts. Useful for large projects with deep dependency graphs. |
| `minDisplayedSeverity` | `high` | The minimum severity level for issues to be displayed in the tree and treemap charts. |
| `licensePolicy` | - | A [custom license policy](./license-policies.md) for the audited project. |
| `loadDataFrom` | `registry` | One of `registry` (get package info from the registry API) or `disk` (get package info from disk, from the `node_modules` directory). Setting this to `disk` can improve & bring predictability to the audit duration for large projects, but note that not all supported package information is locally available. When setting this to `disk`, make sure that your dependencies are installed. |
| `outputPath` | `sandworm` | The output path for the audit artifact files. |
| `failOn` | - | A [custom fail policy](./fail-policies.md) for the audited project. |
| `skipLicenseIssues` | `false` | Skip scanning for license issues |
| `skipMetaIssues` | `false` | Skip scanning for meta issues |
| `skipTree` | `false` | Don't output the dependency tree chart |
| `skipTreemap` | `false` | Don't output the dependency treemap chart |
| `skipCsv` | `false` | Don't output the dependencies csv file |
| `skipReport` | `false` | Don't output the JSON report |
| `skipAll` | `false` | Don't output any file |
| `showTips` | `true` | Show Sandworm tips while building the dependency graph |
