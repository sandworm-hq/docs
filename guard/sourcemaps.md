# Using with bundlers & sourcemaps

Sandworm relies on source file paths to determine caller modules for each method invocation. Unfortunately, when bundling your code with Webpack, Parcel, Rollup, or other similar tools, that information is lost, as everything gets bundled together in a single file. To re-enable Sandworm in this scenario, you'll also need to provide a [sourcemap](https://developer.chrome.com/blog/sourcemaps/).

> **Note**
> When loading sourcemaps, `Sandworm.init` becomes an async method you need to `await`. It is best to wait for it to finish before importing other modules or further initializing your app. Until then, Sandworm will not be able to correctly infer module names, potentially leading to legitimate calls being blocked.

The simplest way to instruct Sandworm to load sourcemaps is to pass `loadSourceMaps=true` to `Sandworm.init`. Setting this option to `true` will load the sourcemap defined within the currently executing js file (containing the `init`-invoking code).

> **Note**
> `loadSourceMaps` is true by default when running Sandworm in a browser.

```javascript
await Sandworm.init({ loadSourceMaps: true });
```

### Configuring sourcemaps

Ideally, your generated sourcemap:

* should be inlined with the source file itself to save an extra network request;
* should exclude the sources, as you probably don't what those to be publicly available, and they will unnecessarily inflate the file size;
* should only include line numbers to reduce the file size, as we're only ever interested in original file names.

### Multiple source files

If you're generating multiple source files in your bundling process, you'll need to let Sandworm know. Otherwise, js files distinct from the main file (the one that has initially called `init`) will be treated as entirely individual modules. To signal you need multiple code files, pass an object to the `loadSourceMaps` option with source paths (or URLs) as keys and sourcemap paths (or URLs) as values:

```javascript
await Sandworm.init({
  loadSourceMaps: {
    'http://localhost:3001/static/js/main.chunk.js':
      'http://localhost:3001/static/js/main.chunk.js.map',
    'http://localhost:3001/static/js/vendors~main.chunk.js':
      'http://localhost:3001/static/js/vendors~main.chunk.js.map',
  },
});
```