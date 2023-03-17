# Caller module paths

Sandworm-detected module names reflect the entire code path that led to invoking a method, from your app's level down to the actual module executing the method.

Let's say your app imports a module named `test-libB`, which depends on a method from a separate module, `test-libA`, which in turn ends up using `axios` to make an HTTP request. Internally, `axios` uses the `follow-redirects` module as a drop-in replacement for Node's `http` and `https` modules that automatically follows redirects. In this case, you should expect to see the following module name requesting to use the `https.request` permission:

```
test-libB>test-libA>axios>follow-redirects
```

Sandworm uses this path structure to create a proper security model. For example, let's say we want to grant permissions to the call described above:

* Since it initiated the call chain, we could directly grant access to the `test-libB` module. But this would enable **any of `test-libB`'s dependencies** to piggyback on this permission to execute malicious calls.
* We could also directly grant access to `follow-redirects`, but then we are effectively enabling any module in our app to use it for making any requests, including potentially malicious ones.
* The safest option is to grant explicit permissions to explicit module paths, like the one above:

```javascript
Sandworm.init({
    devMode: process.env.NODE_ENV === 'development',
    permissions: [{module: 'test-libB>test-libA>axios>follow-redirects', permissions: ['https.request', 'tls.connect', 'tls.createSecureContext', 'net.Socket', 'dns.lookup']}],
});
```

## Matching caller paths with RegEx

In some scenarios, it is helpful to be able to grant permissions in bulk - like when executing inside a test runner. While this is not generally recommended and may lead to vulnerabilities outside of a controlled environment, the permission descriptor `module` property also accepts a `RegExp` to match multiple module names. Here's a real-world example taken from our automated tests using Jest:

```javascript
Sandworm.init({
    devMode: false,
    skipTracking: true,
    permissions: [
      // Jest runner needs vm.runInContext and bind.args, we explicitly allow them below
      {module: /jest/, permissions: ['vm', 'bind', '*']},
      {module: 'root', permissions: false},
      {module: 'source-map-support', permissions: ['fs']},
    ],
  });
```

## Trusted modules

Sometimes, you might want to exclude specific module names from the caller path, as they are part of the trusted platform you're using to run your app. For example, when running React, the `react-dom` module usually sits at the bottom of the module hierarchy and is responsible for triggering most method calls. To specify trusted modules, use the `trustedModules` configuration option.

> **Note**
> When specifying a trusted module, you effectively permit it to impersonate root. Use this configuration carefully.

## Third party scripts

Sandworm interprets scripts loaded via the `<script>` tag as individual modules. This is why, for example, you might see `https://googletagmanager.com` invoking `Beacon.sendBeacon` whenever your app sends analytics data. To modify this behavior:

* add the script to the `trustedModules` config array, or
* if the script is part of the app and built with a bundler, provide the path to a source map via the `loadSourceMaps` config option.

## Browser extensions

Sandworm can also catch activity coming from local, user-installed browser extensions. To enable this, set the `ignoreExtensions` config option to `false`. By default (`ignoreExtensions: true`), any invoke that has a browser extension anywhere in the call path will be passed through.

## Aliases

Root code can be segmented into multiple "virtual" modules based on the file path by defining aliases. This can be useful, for example, when running tests, to separate core code from testing infrastructure code:

```javascript
// Say we want to run unit tests for https://github.com/expressjs/express
require("@sandworm/guard").init({
  devMode: true,
  trustedModules: ['mocha'],
  // This will make the express core source code register as `express` instead of `root`
  // Unit test code will still be labeled `root`
  aliases: [{path: 'express/lib', name: 'express'}],
});
```

To configure aliases, set the `aliases` config option to an array of objects having:
* a string `path` attribute, representing a path component shared between all source code files that should be matched by the alias;
* a string `name` attribute, representing the alias name to apply.
