# clone-lin-uniswap-v3-sqrt

Experimental i64-scale LIN clone of [Uniswap/v3-core](https://github.com/Uniswap/v3-core) `v1.0.0` SqrtPriceMath amount-delta math:
`getAmount0Delta` (sequential `(L*Q*(sb-sa)/sb)/sa`) and `getAmount1Delta` (`L*(sb-sa)/Q`).

- **LIN clone:** [`src/lin_uniswap_v3_sqrt.lin`](src/lin_uniswap_v3_sqrt.lin)
- **C11 oracle:** [`test/oracles/uniswap_v3_sqrt_c11.c`](test/oracles/uniswap_v3_sqrt_c11.c)
- **Proofs (results only) live in lin-open:** https://github.com/kbelludoo/lin-open
- **Event receipt:** `docs/events/EVENT_UNISWAP_V3_SQRT_CLONE_LIN.rulel` in lin-open

This repository is the standalone LIN copy. It is **not** Uniswap V3, **not** a Pool / SwapRouter / NFT position manager, and does **not** claim Solidity uint256 / mainnet / Q64.96 parity (`Q96=2^96` exceeds signed i64 and is rejected). It is **not** the OpenZeppelin `Math.mulDiv` kernel (`clone-lin-oz-muldiv`) and **not** Uniswap V2 `getAmountOut` (`lin_uniswap_v2_library.lin`).

Upstream was BUSL-1.1 with Change Date 1 April 2023; the Change License is GPL-2.0-or-later (this run is 2026-10-12). This LIN kernel is an original uint64-scale implementation of the published amount-delta formulas at Q32=2^32, not a verbatim Solidity dump.

Pinned upstream: commit `e3589b192d0be27e100cd0daaf6c97204fdb1899` (tag `v1.0.0`), `contracts/libraries/SqrtPriceMath.sol` sha256 `ddd62e3a94346248677f30f1ab009ef015e71e4b8696dcca890eeabc9dc6c149` blob `685f485da43402235c3a9ce7fd85a49597b6373a`.

Canonical goldens (uint64-scale, 128-bit product, Q32=2^32, L=1000000 unless noted):

- `amount0(Q, 2Q, 1e6, floor)` = **500000** (price 1→4 token0)
- `amount1(Q, 2Q, 1e6, floor)` = **1000000** (price 1→4 token1)
- `naive_amt0(Q, 2Q, 1e6)` = **0** (named rejected path: `(L*(sb-sa))/(sa*sb)` overflows uint64)
- `amount0(Q, Q+1, 1, floor)` = **0**; `ceil` = **1** (1-unit solvency gap)
- `amount0(Q, 1.5Q, 1e6, floor)` = **333333**; `ceil` = **333334**
- unordered `(2Q, Q)` matches `(Q, 2Q)`
- `sa=0` and equal prices fail-close / return **0**
- `1e18 * 1e18 / 1` fail-closes to **0**
