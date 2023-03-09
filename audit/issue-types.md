# Detected issue types

| Type | Issue | Severity | Code |
|---|---|---|---|
| Vulnerability | All CVEs | Same as CVE severity | - |
| License | No license specified | `critical` | 100 |
| License | Not licensed for use | `critical` | 101 |
| License | License not OSI approved | `low` | 102 |
| License | License is deprecated | `low` | 103 |
| License | Atypical license | `high` | 104 |
| License | Invalid SPDX license | `high` | 105 |
| License | Custom license expression | `high` | 106 |
| License | License policy violations | Policy severity | 150/151 |
| Meta | Deprecated package | `high` | 200 |
| Meta | Uses install scripts | `high` | 201 |
| Meta | Has no repository | `moderate` | 202 |
| Meta | Has HTTP dependency | `critical` | 203 |
| Meta | Has GIT dependency | `critical` | 204 |
| Meta | Has file dependency | `moderate` | 205 |


## Sandworm issue ids

Sandworm assigns unique ids to `license` and `meta` type issues, via the `sandwormIssueId` issue property. Sandworm doesn't assign ids to vulnerabilities, as they already have a unique GitHub Advisory id, under `github_advisory_id`. All Sandworm-detected issues are also assigned a code - `sandwormIssueCode` - and an optional specifier - `sandwormIssueSpecifier`.

{% hint style="info" %}
Sandworm currently assigns license issues `1XX` codes and meta issues `2xx` codes.
{% endhint %}

For most issues, the Sandworm id is a combination of issue code + package name + package version:

```
SWRM-102-spdx-license-ids-3.0.12
```

Some issue types might trigger more than once for a single package version, so they also append a specifier to the id:

- `SWRM-201` install scripts issue is created once for each install script used - preinstall or postinstall - and generates ids like `SWRM-201-core-js-3.29.0-postinstall`
- `SWRM-203`, `SWRM-204`, and `SWRM-205` are created once for each http/GIT/file dependency in a manifest, and generate ids like `SWRM-203-core-js-3.29.0-react`
