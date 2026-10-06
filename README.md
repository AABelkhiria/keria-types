# keria-types

Rust types for the records a KERIA agent sends and receives over its HTTP API: identifiers, key
states, key events, operations, credentials, registries, contacts, exchanges and notifications. Built
on serde, with no other dependencies.

Extracted from the signify-rs project.

## Modules

Everything lives under `api`:

| Module | Records |
|---|---|
| `contact` | Contacts, challenges, OOBIs, end roles, agent configuration and notifications |
| `credential` | ACDC credentials, registries, issuance events and credential status |
| `error` | `ApiError`, the typed decoding errors |
| `exchange` | Exchange resources and their multisig embeds |
| `identifier` | Identifiers (habs) and their key-manager state: salty, randy, group or external |
| `key_state` | Key states and the agent's controller record |
| `message` | KERI and ACDC messages: key events, exchange, reply and registry messages, ACDC records, schemas, seals and thresholds |
| `operation` | Long-running operations and their metadata |

Decoding is bounded: `from_json_slice` refuses a document above `MAX_DOCUMENT_BYTES` (4 MiB) before
parsing. Unknown object fields are ignored, except where the schema declares open fields, which are
kept. Unknown values of a closed code table are refused.

## Use

```bash
cargo add keria-types
```

```rust
use keria_types::api::{KeyState, from_json_slice};

fn main() -> Result<(), Box<dyn std::error::Error>> {
    let body = br#"{"vn":[1,0],"i":"EEXekkGu9IAzav6pZVJhkLnjtjM5v3AcyA-pdKUcaGei","s":"0","p":"",
      "d":"EEXekkGu9IAzav6pZVJhkLnjtjM5v3AcyA-pdKUcaGei","f":"0","dt":"2023-08-19T21:04:57.948863+00:00",
      "et":"dip","kt":"1","k":["DMZh_y-H5C3cSbZZST-fqnsmdNTReZxIh0t2xSTOJQ8a"],"nt":"1",
      "n":["EM9M2EQNCBK0MyAhVYBvR98Q0tefpvHgE-lHLs82XgqC"],"bt":"0","b":[],"c":[],
      "ee":{"s":"0","d":"EEXekkGu9IAzav6pZVJhkLnjtjM5v3AcyA-pdKUcaGei","br":[],"ba":[]},
      "di":"ELI7pg979AdhmvrjDeam2eAO2SR5niCgnjAJXJHtJose"}"#;

    let state: KeyState = from_json_slice(body)?;
    assert_eq!(state.sequence_number, "0");
    assert_eq!(state.signing_keys.len(), 1);
    Ok(())
}
```

A record is the decoded shape of what the agent sent, not the protocol artifact. Verify a SAID or
signature against the bytes received, never against a record that went through a round trip.

## Prerequisites

- Rust 1.97 or newer (`rustup` installs the toolchain pinned in `rust-toolchain.toml`).
- Nightly Rust and `cargo-fuzz`, only for fuzzing.

## Build and test

```bash
cargo fmt --all -- --check
cargo clippy --all-targets --all-features
cargo test --all-features
cargo deny check
```

## Test fixtures

`fixtures/types-keria-api/v1.json` is a frozen snapshot of the types as signify-ts declared them at
commit `91a04a5`, with the TypeScript compiler's verdict on 82 documents. The tests check the Rust
types against it and allow 14 recorded differences. It will be replaced by fixtures derived from
KERIA and keripy.

## Fuzzing

```bash
cargo +nightly fuzz run keria_api
```

## License

Either of

- Apache License, Version 2.0 ([LICENSE-APACHE](LICENSE-APACHE))
- MIT license ([LICENSE-MIT](LICENSE-MIT))

at your option. Contributions are dual licensed the same way unless you say otherwise.
