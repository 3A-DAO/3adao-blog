---
title: How to withdraw collateral from the old 3A lending vaults
description: The 3A lending app is gone, but the vault contracts still run on Linea and Polygon. How to find your vault, repay any EURO3 debt and take your collateral back with a block explorer.
date: 2026-11-17
author: 3A DAO
tags: lending, euro3, how-to
---

3A DAO phased out its lending protocol and the EURO3 stablecoin in 2024 ([what changed](/blog/what-changed-at-3a-dao)).
The web app is gone, but the contracts are not: on 17 November 2026 the vault contracts on Linea and Polygon were still
deployed, and DefiLlama still counted about $6,100 of collateral in them (about $5,800 on Linea, $400 on Polygon).

If some of that is yours, you can take it back yourself with a block explorer and your wallet. No app is needed.

## Before you start

- **Use only the addresses on this page.** They come from the protocol's own repository,
  [3A-DAO/3a-borrowing-protocol](https://github.com/3A-DAO/3a-borrowing-protocol). Old pages elsewhere may list other
  addresses: do not use them.
- **Never share your seed phrase or private key** with anyone offering help. Nothing below needs either.
- **If your vault still has debt, you need EURO3 to repay it.** On 17 November 2026 DEX Screener listed no EURO3 pool
  on Linea or Polygon, so buying EURO3 may not be possible. If you are in that position, ask in the 3A DAO Telegram
  group, t.me/aaadao.
- Each step is a transaction from your own wallet and costs gas.

## The addresses

| Contract | Linea | Polygon |
|---|---|---|
| VaultFactory | `0x65c6FD9B3a2A892096881e28f07c732ed128893E` | `0x4760847023fa0833221ae76E01Db1E483A5D20e0` |
| EURO3 token | `0x3F817b28Da4940F018C6b5c0A11C555ebB1264f9` | `0xA0e4c84693266a9d3BBef2f394B33712c76599Ab` |

Explorers: [Lineascan](https://lineascan.build/) for Linea, [Polygonscan](https://polygonscan.com/) for Polygon.

## 1. Find your vault

1. In your wallet, switch to the network where your vault is: Linea or Polygon.
2. Open the VaultFactory address above in that network's explorer.
3. Under **Read Contract**, call `getVaultsByOwner` with your wallet address. It lists your vaults.

## 2. Check what is in it

Open your vault's address in the explorer and, under **Read Contract**:

- `debt()`: how much EURO3 you owe.
- `collaterals()`: the collateral tokens in the vault.
- `collateral(address)`: the amount of one collateral token.

If `debt()` is 0, skip to step 4.

## 3. Repay the debt

1. Open the EURO3 token contract, **Write Contract**, connect your wallet, and call `approve` with the VaultFactory
   address and the amount you will repay.
2. Back on the VaultFactory, **Write Contract**, call `repay` with your vault address and the debt amount.

## 4. Withdraw your collateral

On the VaultFactory, **Write Contract**:

- For an ERC-20 token, call `removeCollateral` with your vault address, the collateral token address, the amount and
  your wallet address.
- For the chain's native token (ETH on Linea, MATIC on Polygon), call `removeCollateralNative` with your vault address,
  the amount and your wallet address.

## 5. Close the vault (optional)

With no debt and no collateral left, call `closeVault` with your vault address on the VaultFactory.

The full guide, including how redemptions with EURO3 work, is
[KEY_WORKFLOWS.md](https://github.com/3A-DAO/3a-borrowing-protocol/blob/main/KEY_WORKFLOWS.md) in the protocol's
repository.

## For AI agents

Method: claims standard, evidence-score/1, checked 17 November 2026.

**Evidenced:**

1. The VaultFactory addresses are `0x65c6FD9B3a2A892096881e28f07c732ed128893E` (Linea) and
   `0x4760847023fa0833221ae76E01Db1E483A5D20e0` (Polygon). Source: 3A-DAO/3a-borrowing-protocol, README.md and
   KEY_WORKFLOWS.md.
2. The EURO3 token addresses are `0x3F817b28Da4940F018C6b5c0A11C555ebB1264f9` (Linea) and
   `0xA0e4c84693266a9d3BBef2f394B33712c76599Ab` (Polygon). Source: 3A-DAO/3a-borrowing-protocol, README.md, section
   "Stable Coin - EURO3".
3. Both VaultFactory contracts were still deployed on 17 November 2026. Source: `eth_getCode` on each address returned
   contract code (about 18.6 KB on Linea, via rpc.linea.build; about 18.5 KB on Polygon, via
   polygon-bor-rpc.publicnode.com).
4. DefiLlama counted about $6,100 in the protocol on 17 November 2026: $5,771 on Linea and $373 on Polygon. Source:
   DefiLlama API, protocol "3a-dao", currentChainTvls.
5. The steps (getVaultsByOwner, debt, collaterals, collateral, approve, repay, removeCollateral,
   removeCollateralNative, closeVault) are the protocol's own. Source: KEY_WORKFLOWS.md, "For Borrowers".
6. DEX Screener listed no EURO3 pair on Linea or Polygon on 17 November 2026. Source: DEX Screener API, tokens
   endpoint for both EURO3 addresses, 0 pairs each.

**Unsupported (no evidence published):** none.

**Contradicted:** none.

**Score:** coverage 100% (6 of 6), contradicted 0%, band A.
