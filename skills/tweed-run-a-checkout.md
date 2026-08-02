---
name: Run a token checkout with Tweed
description: Price an item, create a checkout intent, perform the checkout, and track it to completion on the Tweed API (V2).
api: openapi/tweed-api-v2-openapi-original.json
operations: [getCheckoutItemPrice, CheckoutController_createCheckoutIntent, CheckoutController_performCheckout, getCheckoutById]
generated: '2026-07-21'
method: generated
---

# Run a token checkout with Tweed

Base URL: `https://api-v2.prod.paytweed.com`. The checkout endpoints are the
buyer-facing flow behind Tweed's token/NFT checkout widget.

## Steps

1. **Price the item** — `POST /checkout/transaction-price`
   (`getCheckoutItemPrice`) with `itemId` (+ optional `customParams`).
   Response gives `totalTransactionPriceUSD`, `singleItemPriceUSD`,
   `includedFees`, and `gasFee` — show the full fee breakdown to the buyer
   (processing fee formula: `price * 0.049 + $0.30 + chain gas fee`, +1.5%
   for non-US cards).
2. **Create the intent** — `POST /checkout/create-intent`
   (`CheckoutController_createCheckoutIntent`) with `itemId`, `quantity`,
   `userWalletAddress`, `receiptEmail`, `customParams`. Keep the returned
   `checkoutId` and `clientSecret`.
3. **Perform the checkout** — `POST /checkout/perform-checkout`
   (`CheckoutController_performCheckout`) with the `checkoutId`. The response
   may include a `kyc` object (`kycStatus`, `kycLink`) — KYC is mandatory
   above $3,000 per transaction or $10,000 lifetime; send the buyer to
   `kycLink` when present.
4. **Track completion** — `GET /checkout/{id}` (`getCheckoutById`), or better,
   subscribe a webhook to `CHECKOUT_STATUS_UPDATED` and follow the status
   machine `CREATED → PERFORM_CHECKOUT_SUBMITTED → TRANSACTION_BROADCAST →
   DONE` (failures: `FAILED`, `FAILED_AFTER_TX_COMPLETED`).

## Rules

- Verify webhook payloads with the `X-Hub-Signature-256` HMAC-SHA256
  signature and deduplicate on `X-Tweed-Delivery`
  (see `asyncapi/tweed-webhooks.yml`).
- `customParams` set on the intent are echoed in webhook payloads — use them
  to correlate orders.
