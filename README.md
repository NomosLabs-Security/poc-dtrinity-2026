# dTRINITY dLEND Deposit Inflation Attack — PoC

> **Educational Purpose Only** — This PoC is created for security research and education
> purposes only. It is a simplified simulation, not a fork replay against mainnet.

## Overview
- **Date:** 2026-03-17
- **Loss:** ~$257,000 (dUSD bad debt in dLEND pool)
- **Chain:** Ethereum
- **Category:** Logic / Math Errors (Deposit Inflation)
- **Affected Deployment:** dLEND on Ethereum (Fraxtal and Katana deployments unaffected)

## Quick Start

```bash
git clone https://github.com/NomosLabs-Security/poc-dtrinity-2026
cd poc-dtrinity-2026
forge install foundry-rs/forge-std --no-git
forge test -vvvv
```

## Vulnerability

The dLEND lending pool on Ethereum suffered a **deposit inflation attack** (also known as
an ERC4626 vault inflation attack or "first depositor" attack). The attack mechanism:

1. **Initial small deposit:** The attacker deposits a minimal amount (e.g., 1 wei) into
   the vault to receive 1 share.
2. **Direct donation:** The attacker transfers a large amount of dUSD directly to the
   vault contract, inflating the exchange rate without minting new shares.
3. **Rounding exploitation:** Subsequent depositors' deposits are rounded down to 0 shares
   due to the inflated exchange rate (`shares = deposit * totalShares / totalAssets`).
4. **Drain:** The attacker redeems their single share for all assets in the vault,
   including the victims' deposits.

This classic ERC4626 vulnerability drained dUSD liquidity from the lending pool, creating
approximately $257,000 in bad debt. The protocol was paused immediately after detection.

**Key details:**
- Fraxtal and Katana deployments were not affected (isolated reserves per chain)
- The dTRINITY team committed to covering 100% of losses using internal funds
- Repayment was scheduled within 24 hours of the announcement

## Expected Output

Running `forge test -vvvv` demonstrates:
- How a 1-wei deposit followed by a large donation inflates the exchange rate
- How subsequent depositors lose funds due to share rounding to zero
- How the attacker redeems all assets with a single share
- How the fixed version (with virtual shares/assets) prevents the attack

## References

- [dTRINITY Official Disclosure (X)](https://x.com/dTRINITY_DeFi)
- [Cryip — dTRINITY's dLEND Protocol Exploit Drains Around $257K](https://cryip.co/dtrinitys-dlend-protocol-exploit-drains-around-257k-on-ethereum/)
- [Cryip — dTRINITY Exploit Breakdown](https://dev.to/cryip/dtrinity-exploit-breakdown-257k-lost-due-to-share-accounting-index-sync-bug-2k7a)
- [Attack TX on Etherscan](https://etherscan.io/tx/0xba5e1e36b0305772d35509c694782fb9118d4ecc)
- [ERC4626 Inflation Attack — OpenZeppelin](https://docs.openzeppelin.com/contracts/5.x/erc4626)
- [First Depositor Attack — Mixbytes](https://mixbytes.io/blog/overview-of-the-first-deposit-attack)

## Full Analysis

- [NomosLabs Security Intelligence Archive](https://nomoslabs.io/archive/dtrinity-2026)

## License

MIT — For educational use only.
