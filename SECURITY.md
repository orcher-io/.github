# Security policy

ORCHER is a developer preview. This policy covers the public repositories of
the [orcher-io](https://github.com/orcher-io) organization and the engine
image `ghcr.io/orcher-io/orcher`.

## Reporting a vulnerability

Please do not report security problems in public issues, discussions or pull
requests. Report them privately through GitHub instead: open the **Security**
tab of the affected repository and choose **Report a vulnerability**. Only the
maintainers can see the report.

| Affected component | Private report |
|--------------------|----------------|
| Python SDK | [orcher-io/sdk-py](https://github.com/orcher-io/sdk-py/security/advisories/new) |
| TypeScript SDK | [orcher-io/sdk-ts](https://github.com/orcher-io/sdk-ts/security/advisories/new) |
| Rust SDK | [orcher-io/sdk-rust](https://github.com/orcher-io/sdk-rust/security/advisories/new) |
| SDK core | [orcher-io/sdk-core](https://github.com/orcher-io/sdk-core/security/advisories/new) |
| Protocol definitions | [orcher-io/protos](https://github.com/orcher-io/protos/security/advisories/new) |
| Engine image, quickstart, or not sure | [orcher-io/quickstart](https://github.com/orcher-io/quickstart/security/advisories/new) |

Include the component and version, what an attacker can do, and the steps or
code that show it.

## Supported versions

Security fixes go into the latest minor release of each package and the
latest engine image. Please check that the problem still occurs there before
reporting.

| Package | Supported |
|---------|-----------|
| `orcher-sdk` (PyPI) | latest minor release |
| `@orcher/sdk` (npm) | latest minor release |
| `orcher-sdk` (crates.io) | latest minor release |
| `orcher-sdk-core` (crates.io) | latest minor release |
| `orcher-proto` (crates.io) | latest minor release |
| `ghcr.io/orcher-io/orcher` | latest release |

## What to expect

ORCHER is maintained by a small team, so these are aims rather than
guarantees. We aim to acknowledge a report within a week and to keep you
updated while we work on it. Once a fix is released we publish a GitHub
security advisory and credit you, unless you would rather stay anonymous.
Please give us a reasonable chance to release a fix before disclosing the
problem publicly.
