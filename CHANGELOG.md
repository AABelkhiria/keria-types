# Changelog

> Following [Keep a Changelog](https://keepachangelog.com/en/1.1.0/)
> and [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.1.0] - 2026-10-06

First release, extracted from the signify-rs project with its history.

### Added

- KERIA agent API record types under `api`: contacts, challenges, OOBIs and end roles; credentials,
  registries and credential status; exchanges; identifiers and key-manager state; key states; KERI and
  ACDC messages; operations.
- Bounded decoding with `from_json_slice`, refusing documents above 4 MiB and nesting beyond 128.
- A frozen snapshot of the types as declared by [signify-ts] at commit `91a04a5`, used as test data.

[signify-ts]: https://github.com/WebOfTrust/signify-ts
