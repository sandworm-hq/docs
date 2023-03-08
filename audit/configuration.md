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
      "moderate": ["cat:Weakly Protective"],
    },
    "loadDataFrom": "registry",
    "outputPath": "sandworm",
    "failOn": ["*.critical"],
  }
}
```
{% endcode %}
