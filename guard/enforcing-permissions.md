# Enforcing permissions

To use in production mode and start enforcing module API access restrictions, provide a `permissions` array to `Sandworm.init`:

```javascript
const Sandworm = require('@sandworm/guard');
Sandworm.init({
    devMode: process.env.NODE_ENV === 'development',
    permissions: [{module: 'react-use', permissions: ['Storage.getItem', 'Storage.setItem']}],
});
```

* Update the `devMode` config to reflect your environment by using environment vars or any other available signal;
* Provide an array of permission descriptors in the form of objects with a `module` name and a `permissions` array of strings corresponding to the allowed methods.
* The inspector can generate a baseline permissions array for you based on the activity captured in dev mode.

When detecting an unauthorized execution attempt, Sandworm throws a `SandwormError`. Besides the `message` attribute, this error object also includes more details about the event:
* `module`: the invoking module name or path
* `method`: the invoked method, for example `fs.readFile`

Note that errors might be swallowed by third party code and not reach root level, so catching a `SandwormError`, while recommended, will not always work. To make sure your app code gets notified about every unauthorized execution, use the `onAccessDenied` configuration option to register a callback method that will always be triggered right before Sandworm throws, and passed the `SandwormError` object as an argument.

```javascript
const Sandworm = require('@sandworm/guard');
Sandworm.init({
    devMode: process.env.NODE_ENV === 'development',
    permissions: [...],
    onAccessDenied: (error) => {
      trackOrLogError(error.module, error.method);
    },
});
```
