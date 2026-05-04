# rustls `prefer-post-quantum` feature only does anything with `aws-lc-rs`

If you want a rustls client that negotiates `X25519MLKEM768` (the IETF-WG-blessed post-quantum hybrid kex, codepoint `0x11ec`), the obvious thing is:

```toml
rustls = { version = "0.23", features = ["std", "tls12", "ring", "prefer-post-quantum"] }
```

This compiles fine. It also negotiates plain `X25519` against every server.

## What's going on

`rustls` 0.23 has two interchangeable crypto providers: `ring` and `aws-lc-rs`. Both are picked via cargo features. `prefer-post-quantum` is a separate feature that flips the kex-group ordering so the client offers `X25519MLKEM768` first.

The trap: **`ring` does not implement ML-KEM**. The `prefer-post-quantum` flag has nothing to push to the front, so the client's kex offer is just `X25519, secp256r1, ...` and any server that supports the hybrid never gets the chance.

`aws-lc-rs` does implement ML-KEM (it's based on AWS's post-quantum-friendly fork of BoringSSL).

## Fix

```toml
rustls = { version = "0.23", default-features = false, features = ["std", "tls12", "aws-lc-rs", "prefer-post-quantum"] }
```

And install the provider:

```rust
let _ = rustls::crypto::aws_lc_rs::default_provider().install_default();
```

Now `ClientConnection::negotiated_key_exchange_group()` actually returns `X25519MLKEM768` for any server that supports it (Cloudflare, Google, OpenAI, Anthropic, ...).

## Tradeoff

`aws-lc-rs` brings a sizeable C-via-cmake dependency and roughly doubles the release-binary size compared to `ring`. For a one-shot scanner that's fine; for a daemon that just needs classical X25519, `ring` stays smaller.

This is one of those "the feature flag exists but it's a no-op against half the providers" cases that costs an hour to debug without a printed error.
