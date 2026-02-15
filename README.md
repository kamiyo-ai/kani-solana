**kani-solana**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

Reusable, zero-overhead Kani proof harnesses for core mathematical invariants in Solana DeFi protocols.

The crate provides battle-tested harnesses that verify:
- Bounds and overflow safety
- Value conservation (no creation or destruction of funds)
- Monotonicity of state transitions
- Percolator-style risk primitives: haircut ratio and profit haircut math

All code is gated behind `cfg(kani)`. Normal `cargo build` / `cargo test` builds are completely unaffected — the crate has **zero runtime cost**.

### Quick Start

#### 1. Add as a dev-dependency

```toml
[dev-dependencies]
kani-solana = { git = "https://github.com/kamiyo-ai/kani-solana.git", rev = "a9fc18fe2067c83e4c409fcc50133ea0b05f74ac" }
```

#### 2. Write a proof (example)

```rust
#![cfg(kani)]

use kani_solana::risk::{effective_pnl, haircut_ratio};

#[kani::proof]
fn payout_is_bounded_by_profit() {
    let vault: u128 = kani::any();
    let principal_total: u128 = kani::any();
    let insurance: u128 = kani::any();
    let pnl_pos_total: u128 = kani::any();
    let my_pnl: i128 = kani::any();

    let (h_num, h_den) = haircut_ratio(vault, principal_total, insurance, pnl_pos_total);
    let payout = effective_pnl(my_pnl, h_num, h_den);

    kani::assert(payout <= my_pnl.max(0) as u128);
}
```

#### 3. Run the proofs

```bash
cargo install --locked kani-verifier
cargo kani setup
cargo kani
```

### License

MIT © kamiyo-ai

---

Ready to drop into any Solana protocol repository that uses formal verification with Kani. Contributions welcome!
