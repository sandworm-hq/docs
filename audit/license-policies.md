# License policies

Sandworm uses license policies to determine what sort of issues to raise when scanning your app's dependency licenses. A license policy object links specific license strings or license categories to issue severity levels. Any usage of such licenses will, upon audit, generate a license issue of the specified severity.

## Default license categories

The default license categories are:

* `Public Domain`
* `Permissive`
* `Weakly Protective`
* `Strongly Protective`
* `Network Protective`
* `Uncategorized`

{% hint style="info" %}
The default license category names are reserved. You can't define custom license categories with the same name as a default one, but you can override the default category assignment by Sandworm - see [custom license categories](#custom-license-categories).
{% endhint %}

See Sandworm's built-in [SPDX license database](https://github.com/sandworm-hq/sandworm-audit/blob/main/src/issues/licenses.json) for the full classification breakdown. 

{% hint style="danger" %}
While we do our best to keep license info accurate and up-to-date, you should still carefully review all of the terms and conditions of the actual license before using the licensed material. Sandworm isn't a law firm and doesn't provide legal services.
{% endhint %}

## The default license policy

The default license policy:
- Generates `high` severity license issues for licenses labeled as `Network Protective` or `Strongly Protective`;
- Generates `moderate` severity license issues for licenses labeled as `Weakly Protective`.

{% hint style="info" %}
Un-categorized licenses always result in a `high` severity error. See [issue types](https://docs.sandworm.dev/audit/issue-types).
{% endhint %}

## Custom license policies

You can configure Sandworm to use a custom license policy. The policy object:
- has keys that match one of the supported issues severities: `critical`, `high`, `moderate`, or `low`;
- has values that are arrays;
- each array value is a string that represents:
  - a specific license - for example `MIT`;
  - a category of licenses, prefixed by "cat:" - for example `cat:Network Protective`.

As an example, here is the representation of the default license policy that Sandworm applies:

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

Or using a configuration file:

{% code title=".sandworm.config.json" overflow="wrap" lineNumbers="true" %}
```json
{
  "audit": {
    "licensePolicy": {
      "critical": ["MIT"]
    }
  }
}
```
{% endcode %}

## Custom license categories

Apart from the default categories above, Sandworm also supports user-defined license categories. To define categories, set the `categories` key of the license policy JSON to an array of `{name: 'string', licenses: ['string', ...]}` objects:

{% code title=".sandworm.config.json" overflow="wrap" lineNumbers="true" %}
```json
{
  "audit": {
    "licensePolicy": {
      "categories": [
        {
          "name": "Text Licenses",
          "licenses": ["CC-BY-3.0"]
        }
      ],
      "high": ["cat:Network Protective", "cat:Strongly Protective"],
      "moderate": ["cat:Weakly Protective"]
    }
  }
}
```
{% endcode %}

{% hint style="warning" %}
Any license policy you specify overwrites the default one - even if you just define custom license categories within. If you want to keep the default behavior, you need to also specify the `high` and `moderate` trigger categories, like in the example above.
{% endhint %}

The default license category names are reserved. When using a default name in a custom category definition, the associated licenses are moved from their pre-assigned category to the specified one. For example, to move the `CC-BY-3.0` license from `Uncategorized` to `Permissive`, you can use:

{% code title=".sandworm.config.json" overflow="wrap" lineNumbers="true" %}
```json
{
  "audit": {
    "licensePolicy": {
      "categories": [
        {
          "name": "Permissive",
          "licenses": ["CC-BY-3.0"]
        }
      ],
      "high": ["cat:Network Protective", "cat:Strongly Protective"],
      "moderate": ["cat:Weakly Protective"]
    }
  }
}
```
{% endcode %}

{% hint style="info" %}
Within the default categories, a license can only be assigned to a single category. A license can, however, belong to multiple user categories.
{% endhint %}
