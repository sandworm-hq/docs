# License policies

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