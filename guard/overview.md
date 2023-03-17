# Overview

Sandworm Guard is a sandboxing & malware detection tool for npm packages. Rather than relying on CVE advisories, Sandworm watches lower-level APIs like the Node VM and browser APIs like DOM manipulation, fetch, etc., and throws when a package unexpectedly accesses these APIs. While this won't protect against all classes of vulnerabilities, it assures that your project is safe from hand-crafted, zero-day vulnerabilities that leave your data open to attack until a CVE is issued and a fix is published.

Most tools in this space currently use static analysis to scan a package's source and infer potential threats by looking at code patterns, invoked methods, or loaded modules. However, it's generally simple to trick such analysis tools using [various obfuscation techniques](https://swag.cispa.saarland/papers/moog2021statically.pdf). Static analysis is, therefore, not a definitive security solution and should be used in tandem with dynamic tools like Sandworm.

Sandworm Guard does dynamic analysis in the runtime - it knows about what happens when it happens:

* It can't let you know about possible vulnerabilities before it sees the code run;
* It also can't capture information about "dormant" code that doesn't get executed;
* No obfuscation or workaround can fool our interceptors, though: as soon as any code segment attempts to invoke a sensitive method, Sandworm will capture that call and be able to allow or deny access.
