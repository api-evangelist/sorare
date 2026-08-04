---
name: Bid on an auction or make an offer for a Sorare card
description: Discover open auctions and cards, then bid or create a Single Sale / Direct offer, signing the blockchain payload with StarkWare.
api: https://api.sorare.com/graphql
operations: [auctions, card, bid, createSingleSaleOffer, createDirectOffer, acceptOffer]
method: generated
source: https://github.com/sorare/api
---

# Bid on an auction or make an offer

Trading operations touch the blockchain and require a StarkWare signature in addition to a JWT session.

## Steps

1. **Authenticate** — obtain a JWT (see the sorare-sign-in skill). OAuth's default scope cannot bid or sell, so use a user JWT session.
2. **Discover inventory** — query `auctions` for open English auctions, or query `card` for a specific card by slug.
3. **Sign the action** — build and sign the trade payload with your StarkWare private key using the `@sorare/crypto` library.
4. **Submit the action:**
   - Bid on an English auction with the `bid` mutation.
   - Sell a card with `createSingleSaleOffer`, or offer it to a specific user with `createDirectOffer`.
   - Accept an incoming offer with `acceptOffer`.
5. **Subscribe to updates** — use GraphQL subscriptions (`@sorare/actioncable`) for auction/offer updates to react to outbids and acceptances in real time.

## Rules
- Every trade mutation requires a valid StarkWare signature — the JWT alone is not sufficient.
- Only the documented mutations above exist; verify the exact input shape against the live schema (`https://api.sorare.com/graphql/schema`).
- Handle HTTP 429 (`Retry-After`) and GraphQL `errors[]` responses; a 200 with an `errors[]` entry means the mutation failed.
