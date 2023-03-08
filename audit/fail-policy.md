# Failing on identified issues

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