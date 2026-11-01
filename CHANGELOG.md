# Changelog

> Following [Keep a Changelog](https://keepachangelog.com/en/1.1.0/)
> and [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added

- `api::EventSeal`, a seal that requires `i`, `s` and `d` and writes them in that order.
- `api::StatusAnchor`, the anchor of a credential status, whose `s` is a number.
- `api::UnbackedIssuance` and `api::BackedIssuance`, the two shapes of an issuance event.
- `api::IssuanceIlk`, `api::BackedIssuanceIlk` and `api::ISSUANCE_ILKS`, the `t` values of an issuance event.
- `IssuanceEvent::kind`, `digest`, `credential` and `registry`, which read what both shapes share.
- `api::RevocationEvent`, a revocation of either shape, with `kind`, `digest`, `credential` and `registry`.
- `api::BackedRevocationRecord`, a revocation in a registry with backers.
- `api::BackedRevocationIlk` and `api::REVOCATION_ILKS`, the `t` values of a revocation event.
- `api::ResolvedOobi`, the result of an OOBI operation, with `key_state` and `into_key_state`.
- `api::ThresholdWeight` and `api::NestedWeight`, the entries of a weighted threshold clause.

### Fixed

- `RegistryMetadata.anchor` and `RegistryResponse.anchor` are now `EventSeal`, not `Anchor`. KERIA sends
  the registry's `{i, s, d}` seal there, so a real registry operation failed to decode with ``missing field `pre` ``.
  The schema's `{pre, sn, d}` is now refused, and `RegistryMetadata` writes `anchor` before `depends`, as KERIA does.
- `UnbackedCredentialState.anchor` and `BackedCredentialState.anchor` are now `StatusAnchor`, not `Seal`. KERIA sends
  `s` as a number there, so a real credential or credential status failed to decode with ``invalid type: integer``.
  The schema's string `s` is now refused.
- `IssuanceEvent` is now an enum of `UnbackedIssuance` and `BackedIssuance`, not a struct, so what both share is read
  through its accessors. A `bis` issuance carries `ii` and `ra` rather than `ri`, so a credential from a registry with
  backers failed to decode with ``missing field `ri` ``. The schema's `bis` with `ri` is now refused.
- `MultisigRevocationEmbed.rev` is now `RevocationEvent`, not `RevocationRecord`, which accepted only `rev`, so a
  revocation proposal for a registry with backers failed to decode.
- `OobiOperation` now completes with `ResolvedOobi`, not `KeyState`. KERIA answers `{oobi}` when it knows no controller
  for the OOBI, so resolving an OOBI for a credential schema failed to decode with ``missing field `i` ``.
- `SubmitMetadata` is now a struct of `alias` and `sn`, not an alias of `LogPositionMetadata`. KERIA writes `alias` in
  place of `pre`, so a real submit operation failed to decode with ``missing field `pre` ``. The schema's `{pre, sn}` is
  now refused.
- `Threshold::Clause` and `Threshold::Clauses` now hold `ThresholdWeight`, not `String`. keripy writes nested weights
  such as `[{"1/2":["1/2","1/2"]},"1/2"]`, so any key event or key state with such a threshold failed to decode.
- `DelegatorMetadata.anchor` is now `Option<KeyEvent>`, not `Option<Anchor>`. KERIA echoes the approving
  event there, usually an `ixn`, so a real delegator operation failed to decode with ``missing field `pre` ``.

## [0.1.0] - 2026-10-06

First release, extracted from the signify-rs project with its history.

### Added

- KERIA agent API record types under `api`: contacts, challenges, OOBIs and end roles; credentials,
  registries and credential status; exchanges; identifiers and key-manager state; key states; KERI and
  ACDC messages; operations.
- Bounded decoding with `from_json_slice`, refusing documents above 4 MiB and nesting beyond 128.
- A frozen snapshot of the types as declared by [signify-ts] at commit `91a04a5`, used as test data.

[signify-ts]: https://github.com/WebOfTrust/signify-ts
