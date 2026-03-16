# LP Rescue Executor (PancakeSwap V3)

This module implements your "one-time approval + fully automatic execution" flow on BNB Chain:

1. User approves NFT once (`approve(executor, tokenId)`).
2. Bot calls executor.
3. Executor decreases liquidity, collects tokens, and sends proceeds to:

`0x32eb462158F7A121d407510C340928404d863E94`

---

## 1) Install

```bash
npm install
```

Copy env:

```bash
cp .env.example .env
```

Fill all required variables.

---

## 2) Compile and Deploy

```bash
npm run compile
npm run deploy
```

Deployment writes address to console. Put it into `.env` as `EXECUTOR_ADDRESS`.

---

## 3) Initial security setup

After deploy, from owner account:

- call `setAllowedCaller(botAddress, true)` to authorize your backend bot.
- call `setAllowedTokenId(tokenId, true)` for each LP NFT that can be rescued.

This ensures the bot can only operate allowed token IDs.

---

## 4) User one-time approval

From the NFT owner wallet:

```js
await positionManager.approve(executorAddress, tokenId);
```

Once approved, user does not need to sign each rescue action.

---

## 5) Run rescue

```bash
npm run rescue
```

Script behavior:

- `RESCUE_MODE=all`: calls `rescueAllLiquidity`
- `RESCUE_MODE=partial`: calls `rescuePartialLiquidity`

---

## Contract behavior summary

- restricts callable bots via `allowedCaller`.
- restricts operable LP NFTs via `allowedTokenId`.
- supports slippage controls (`amount0Min`, `amount1Min`).
- collects directly to beneficiary address.

---

## Important safety notes

- Never expose private keys in source code or chat.
- Use a dedicated bot wallet with minimal funds.
- Set non-zero `amount0Min/amount1Min` in production to avoid MEV/slippage issues.
