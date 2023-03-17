# Configuration options

| Option | Default | Description |
| --- | --- | --- |
| `loadSourceMaps` | `true` in browsers `false` in Node | Set this to true to automatically load the sourcemap declared in the caller js file. Alternatively, to load multiple sources and sourcemaps, set this to an object with source file paths/URLs as keys and sourcemap file paths/URLs as values. |
| `devMode` | `false` | In dev mode, all calls are captured, allowed, and tracked to the inspector. When dev mode is false, Sandworm will enforce user-provided permissions and will not track calls. |
| `verbose` | `false` | The default logger level is `warn`; setting this to `true` lowers the level to `debug`. |
| `skipTracking` | `false` | Set this to true to stop event tracking to the inspector in dev mode. |
| `trackingIP` | `127.0.0.1` | The IP address for the inspector. |
| `trackingPort` | `7071` | The port number for the inspector. |
| `ignoreExtensions` | `true` | Ignore activity from browser extensions. |
| `trustedModules` | `[]` | Utility or platform modules that Sandworm should remove from a caller path. |
| `permissions` | `[]` | Module permissions to enforce if dev mode is false. |
| `onAccessDenied` | `undefined` | A function that will be invoked right before throwing on access denied. The error itself will be passed as the first arg. |
| `aliases` | `[]` | An array of alias definitions - see [aliases](#aliases). |
