# Getting started

Add the following lines to **the very start of your app's entry point** to load Sandworm in dev mode. In dev mode, all calls will be intercepted and tracked to the inspector tool, but no enforcement will happen (all calls will be allowed).

```javascript
require('@sandworm/guard').init({devMode: true});
```

> **Warning**
> The code above needs to be the first thing your app runs when it boots so that Sandworm can adequately set up API interception and enforcement can begin if needed. If you load other modules or execute other code before this, you're no longer safe, as others will have had the opportunity to bypass Sandworm's process at that point.

> **Note**
> You can only call `init` once per your app's lifecycle.

> **Note**
> Only your app's code (at the `root` module level) will be allowed to call `init`.

Next, start the inspector tool by running:

```bash
yarn sandworm # or npm run sandworm
```

The inspector interface is now available in your browser at http://localhost:7071/. It will update in real-time with details about module activity and used permissions as your app executes. The UI also allows you to generate the JSON permissions array you need to provide to support enforcing access when moving to production.

If your automated test process has good coverage, this is an excellent time to run it, have it walk through all code paths and functionality in your app, and collect all activity within the inspector.

![Sandworm Inspector](../cli/screenshot.png)

In the left, you'll see a list of all caller paths that have been intercepted. The Permissions tab displays an aggregated list of all required permissions, while the Activity tab hosts a list of all intercepted calls. For each method call, you can see the associated arguments as well as a stack trace that can help you figure out exactly who called what.
