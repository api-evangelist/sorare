---
name: Sign in to the Sorare API and query your account
description: Authenticate against the Sorare GraphQL API with JWT (including 2FA) and read the current user's cards and offers.
api: https://api.sorare.com/graphql
operations: [signIn, currentUser]
method: generated
source: https://github.com/sorare/api
---

# Sign in to the Sorare API

Sorare exposes a single GraphQL endpoint at `https://api.sorare.com/graphql`. Use this skill to obtain a JWT session and read the authenticated user.

## Steps

1. **Fetch the password salt.** `GET https://api.sorare.com/api/v1/users/<email>` returns the per-user bcrypt salt.
2. **Hash the password client-side** using that salt (bcrypt).
3. **Call the `signIn` mutation** with the email, the hashed password, and a mandatory `aud` audience identifier for your app.
   - If the account has 2FA, `signIn` returns an `otpSessionChallenge`. Call `signIn` again supplying `otpSessionChallenge` and the one-time `otpAttempt` code.
4. **Store the JWT** (valid 30 days). Send it on every request as `Authorization: Bearer <token>` together with `JWT-AUD: <aud>`.
5. **Query `currentUser`** to read the account's nickname, wallet address, cards, achievements, auctions and offers.

## Rules
- Always send both the `Authorization: Bearer` and `JWT-AUD` headers — the token is bound to its audience.
- Respect rate limits: 60 req/min authenticated (429 + `Retry-After`), max query depth 12 / complexity 30,000.
- Never invent fields — download the live schema from `https://api.sorare.com/graphql/schema`.
