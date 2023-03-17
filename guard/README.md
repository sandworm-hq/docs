---
description: Easy auditing & sandboxing for your JavaScript dependencies 🪱
---

# Sandworm Guard

[![npm][npm-version-image]][npm-version-url]
[![License][license-image]][license-url]
[![CircleCI][ci-image]][ci-url]
[![Maintainability][cc-image]][cc-url]
[![Test Coverage][coverage-image]][coverage-url]

### Summary

* Sandworm Guard intercepts all potentially harmful Node & browser APIs, like arbitrary code execution (`child_process.exec`) or network calls (`fetch`). It knows what packages are responsible for each call.
* Simple obfuscation techniques can confuse static analysis tools, but Sandworm's dynamic analysis will always intercept risky calls at run time.
* You can use Sandworm Guard to:
  * audit your dependencies, monitor activity and permissions, and see what your code is doing under the hood using the Inspector;
  * generate a security profile automatically from your test suite and do snapshot testing against it;
  * secure your app against supply chain attacks by enforcing per-module permissions.
* Install it as an `npm` module in your existing Node or browser app.
* Works in Node v15+ and [modern browsers](https://browsersl.ist/#q=defaults). Beta support for browsers and sourcemaps.

#### Get involved

* Have a support question? [Post it here](https://github.com/sandworm-hq/sandworm-guard-js/discussions/categories/q-a).
* Have a feature request? [Post it here](https://github.com/sandworm-hq/sandworm-guard-js/discussions/categories/ideas).
* Did you find a security issue? [See SECURITY.md](../contributing/security.md).
* Did you find a bug? [Post an issue](https://github.com/sandworm-hq/sandworm-guard-js/issues/new/choose).
* Want to write some code? See [CONTRIBUTING.md](../contributing/README.md).

## The permission database project

A longer-term goal for Sandworm is to provide an open, public database of per-package permission requirements, based on:
* running automated tests with Sandworm enabled for public packages;
* anonymous info about requirements collected from real-world apps by the inspector.

For every method call that Sandworm intercepts, the inspector will share the following info:

```json
{
  "module": "CALLER_MODULE_NAME",
  "family": "INVOKED_METHOD_FAMILY",
  "method": "INVOKED_METHOD_NAME",
  "sessionId": "INSPECTOR_SESSION_ID"
}
```

This will make it easier for everyone to audit packages and set up Sandworm. To opt out of sharing data with the community, run the inspector with the `--no-telemetry` option. You can also audit [what's getting sent](https://github.com/sandworm-hq/sandworm-guard-js/blob/main/cli/index.js#L19) and the [server code](https://github.com/sandworm-hq/sandworm-collect).

## How Sandworm is tested

Sandworm has several layers of automated testing:

* Jest is used to run Node.js capture & enforce tests for all supported Node APIs (tests run on Node 16.10 and above). See the `tests/node` directory.
* Playwright is used to run browser capture & enforce tests for all supported browser APIs (tests run on WebKit, Chromium, and Firefox). See the `tests/web` directory.
* Jest is used to run unit tests on the core Sandworm source files. See the `tests/unit` directory.

Check out our latest test run inside our [CircleCI pipeline](https://app.circleci.com/pipelines/github/sandworm-hq/sandworm-guard-js).

[npm-version-image]: https://img.shields.io/npm/v/sandworm?style=flat-square
[npm-version-url]: https://www.npmjs.com/package/sandworm
[license-image]: https://img.shields.io/npm/l/sandworm?style=flat-square
[license-url]: https://github.com/sandworm-hq/sandworm-guard-js/blob/main/LICENSE
[ci-image]: https://img.shields.io/circleci/build/github/sandworm-hq/sandworm-guard-js?style=flat-square
[ci-url]: https://app.circleci.com/pipelines/github/sandworm-hq/sandworm-guard-js
[cc-image]: https://api.codeclimate.com/v1/badges/edff60f7f06bb0c589aa/maintainability
[cc-url]: https://codeclimate.com/github/sandworm-hq/sandworm-guard-js/maintainability
[coverage-image]: https://api.codeclimate.com/v1/badges/edff60f7f06bb0c589aa/test_coverage
[coverage-url]: https://codeclimate.com/github/sandworm-hq/sandworm-guard-js/test_coverage
