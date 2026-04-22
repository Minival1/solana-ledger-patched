# solana-ledger-patched

Fork of [anza-xyz/agave](https://github.com/anza-xyz/agave)'s `solana-ledger`
crate, version **2.3.1**, with widened `pub` visibility on shred-internal APIs
that external shred decoders need.

Mirrors what jito's `eric/v2.2-merkle-recovery` branch did for 2.2.x — letting
downstream crates pattern-match on `merkle::Shred` / `merkle::{ShredData,
ShredCode}` fields directly, call `merkle::recover` etc. without going through
the very narrow public surface agave ships.

## Patches (vs upstream 2.3.1)

Pure visibility changes — no functional behaviour change.

| File | Change |
|---|---|
| `src/shred.rs` | `mod merkle;` → `pub mod merkle;` |
| `src/shred.rs` | `mod traits;` → `pub mod traits;` |
| `src/shred.rs` | `const SIZE_OF_SIGNATURE` → `pub const` |
| `src/shred/merkle.rs` | `ShredData`/`ShredCode` fields → `pub` |
| `src/shred/merkle.rs` | `pub(super) enum Shred` → `pub enum Shred` |
| `src/shred/merkle.rs` | `pub(super) fn recover` → `pub fn recover` |
| `src/shred/merkle.rs` | `pub(super) fn {common_header,payload}` → `pub fn …` |
| `src/shred/merkle.rs` | `fn {index,shred_type,fec_set_index}` → `pub fn …` |
| `src/shred/merkle.rs` | `pub use SIZE_OF_MERKLE_PROOF_ENTRY` re-export |
| `src/shred/merkle.rs` | **Added** `ShredData::{data_complete,last_in_slot}` (was only on the wrapper in 2.3.1; jito's v2.2 branch had them on `merkle::ShredData` — external decoders rely on that) |
| `src/shred/merkle_tree.rs` | `pub(crate) const SIZE_OF_MERKLE_PROOF_ENTRY` → `pub const` |
| `src/shred/traits.rs` | `pub(super) trait Shred` → `pub trait Shred` |

## Usage

Add to your workspace's top-level `Cargo.toml`:

```toml
[patch.crates-io]
solana-ledger = { git = "https://github.com/Minival1/solana-ledger-patched", branch = "main" }
```

Any crate depending on `solana-ledger = "=2.3.1"` (either direct or transitive)
will then resolve to this patched copy, and both the patched and stock
versions unify to a single `solana_ledger` type in the build graph.

## License

Inherits `Apache-2.0` from upstream `anza-xyz/agave`.
