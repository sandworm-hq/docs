# Custom registries

Sandworm supports collecting package information from custom/private registries. If you've already configured the custom registry using your `.npmrc` file, Sandworm audits should just work.

Sandworm currently looks at the following configuration files:
* per-project config file: `/path/to/my/project/.npmrc`
* per-user config file: `~/.npmrc`

The currently supported auth related settings are:
* `registry`
* `_authToken`

Scopes can be associated with a separate registry. This allows you to seamlessly use a mix of packages from the primary npm registry and one or more private registries, such as [GitHub Packages](https://github.com/features/packages) or the open source [Verdaccio](https://verdaccio.org/) project.

In order to scope auth tokens, they must be prefixed by a URI fragment. If the credential is meant for any request to a registry on a single host, the scope may look like `//registry.npmjs.org/:`. If it must be scoped to a specific path on the host that path may also be provided, such as `//my-custom-registry.org/unique/path:`.

```ini
; bad config
_authToken=MYTOKEN

; good config
@myorg:registry=https://somewhere-else.com/myorg
@another:registry=https://somewhere-else.com/another
//registry.npmjs.org/:_authToken=MYTOKEN
; would apply to both @myorg and @another
; //somewhere-else.com/:_authToken=MYTOKEN
; would apply only to @myorg
//somewhere-else.com/myorg/:_authToken=MYTOKEN1
; would apply only to @another
//somewhere-else.com/another/:_authToken=MYTOKEN2
```

Environment variables can be replaced using ${VARIABLE_NAME}. For example:

```ini
//registry.npmjs.org/:_authToken=${DEFAULT_REG_TOKEN}
```

Sandworm also supports the `NPM_CONFIG_REGISTRY` environment variable, to set the default registry URL directly.
