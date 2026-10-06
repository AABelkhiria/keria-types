# Security Policy

## Supported versions

Security fixes go to the latest `0.x` release. Older `0.x` versions are not patched.

## Reporting a vulnerability

Please don't open a public issue for something you think is exploitable.

Use GitHub's [private vulnerability reporting][gh-pvr] on this repository instead: *Security* →
*Report a vulnerability*.

[gh-pvr]: https://docs.github.com/en/code-security/security-advisories/guidance-on-reporting-and-writing-information-about-vulnerabilities/privately-reporting-a-security-vulnerability

Useful things to include: the version and platform you were using, a document we can decode to
reproduce it, and what you think the impact is. Strip real identifiers, keys and passcodes from any
document you send.

## Threat model

Every document this crate decodes comes from a KERIA agent, or from whatever sits between the client
and the agent, so all of it is assumed hostile: its size, its nesting, its field names and values,
and its claims about which kind of record it is.

Malformed input shouldn't be able to panic or abort the process, so crate-owned code contains no
`unsafe`, `unwrap`, `expect`, panicking macro, or indexing that can fail, and every rejection is a
typed `ApiError`. `from_json_slice` refuses a document above `MAX_DOCUMENT_BYTES` (4 MiB) before
parsing it, and nesting beyond `MAX_NESTING_DEPTH` (128) is a typed error, never a stack overflow.

Unknown object fields are ignored, so a newer agent adding a field doesn't break an older client.
Unknown values of a closed code table, such as an event type or a key tier, are refused, because they
decide how the rest of the document is read.

## Known limitations

A decoded record is the shape of what the agent sent, not the protocol artifact. Re-serializing it is
not guaranteed to reproduce the bytes received, so a SAID or a signature must be verified against the
original bytes, never against a record that went through this crate.

Identifier records can carry encrypted key-manager state, such as the salt (`sxlt`) and the
current and next signing keys (`prxs`, `nxts`). This crate stores those values as received and never
decrypts or checks them; that is up to the caller.

The types follow a frozen snapshot of signify-ts's declarations, not KERIA itself, so they can lag
behind what an agent actually sends.

## Dependencies

The only production dependencies are `serde` and `serde_json`. `cargo deny` and the resolved graph get
reviewed before each release.
