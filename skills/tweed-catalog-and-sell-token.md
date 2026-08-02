---
name: Catalog and sell a token with Tweed
description: Register a smart contract, expose its mint/sale function, wire a payout, and list a sellable item on the Tweed API (V2).
api: openapi/tweed-api-v2-openapi-original.json
operations: [addContract, addContractFunction, addPayout, addItem, getAllItems, getItemById]
generated: '2026-07-21'
method: generated
---

# Catalog and sell a token with Tweed

Base URL: `https://api-v2.prod.paytweed.com` — authenticate every call with
`Authorization: Bearer <API_KEY>:<API_SECRET>` (User Access Token from the
Management Dashboard; see `authentication/tweed-authentication.yml`).

## Steps

1. **Register the smart contract** — `POST /v1/contracts/add` (`addContract`)
   with `address`, `blockchainId`, and the contract `abi`. Look up valid
   chain ids first with `findAllBlockchains` if needed.
2. **Expose the mint/sale function** — `POST /v1/contracts/add-contract-function`
   (`addContractFunction`) with the `contractId` from step 1, the
   `functionSignature` (e.g. `safeMint(sendTo address, tokenId uint256)`), and
   any `fixedFunctionParams`.
3. **Create a payout destination** — `POST /v1/payouts/add` (`addPayout`)
   choosing `type` fiat (Stripe connected account), stablecoin
   (`blockchainId` + `tokenId` + `spender`), or native (`blockchainId`).
4. **List the item** — `POST /v1/items/add` (`addItem`) binding
   `contractFunctionId` and `payoutId` with `title`, `price`, `currency`,
   and `imageUrl`.
5. **Verify** — `GET /v1/items` (`getAllItems`) or `GET /v1/items/{id}`
   (`getItemById`).

## Rules

- No idempotency keys exist: retry only on network failure after confirming
  with a read (`getAllItems`) that the write did not land.
- Only `200`/`201` responses are documented; treat anything else as opaque
  failure and do not invent error semantics.
- Demo/testing runs on testnet chains via `Environment.demo`
  (see `sandbox/tweed-sandbox.yml`).
