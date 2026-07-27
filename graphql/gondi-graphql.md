# Gondi GraphQL API

## Description

Gondi is a decentralized, non-custodial, peer-to-peer protocol for trading and financing NFTs,
deployed on Ethereum mainnet and HypeEVM. Alongside the on-chain smart contracts, Gondi runs an
off-chain **GraphQL API** at `https://api.gondi.xyz/graphql` that powers the Gondi dApp and the
official `gondi-js` (TypeScript) and `python-sdk` client libraries. The API is the order/offer book
and indexing layer: it lets lenders and borrowers generate, sign, save, list, hide, and renegotiate
loan offers; browse collections, NFTs, listings, orders, loans, and loan activity; run global search;
and manage a user profile, linked wallets, notifications, and API keys.

The schema was captured by live introspection of the production endpoint and exposes **76 queries**
and **48 mutations** across **325 types**. Marquee surfaces:

- **Auth** — `generateSignInNonce` → `signInWithEthereum` (Sign-In With Ethereum / EIP-4361), plus
  `createApiKey` for programmatic access.
- **Loan offers** — `generateSingleNftOfferToBeSigned` / `saveSignedSingleNftOffer`,
  `generateCollectionOfferToBeSigned` / `saveSignedCollectionOffer` (the generate→sign→save pattern
  that keeps offer signing client-side and non-custodial).
- **Refinancing / renegotiation** — `generateRenegotiationOfferToBeSigned`,
  `saveRenegotiationSignedOffer`, renegotiation and top-up requests.
- **Trading / orders** — `publishOrderForNft`, `publishOrderForCollection`, `publishOrderForTrait`,
  `publishSellAndRepayOrder`, `publishBuyNowPayLaterOrder`, `publishDealOrder`.
- **Discovery** — `listCollections`, `listNfts`, `listLoans`, `listOffers`, `listListings`,
  `listOrders`, `globalSearchV2`, plus collection / artist / edition lookups.

## Endpoint

`https://api.gondi.xyz/graphql`  (POST, `application/json`)

## Authentication

Two mechanisms, both surfaced through the schema:

1. **Sign-In With Ethereum (SIWE / EIP-4361)** — call `generateSignInNonce` to obtain a nonce, sign
   the SIWE message with the user's wallet (`viem` wallet client in `gondi-js`), then
   `signInWithEthereum` to establish a session. Offer/order signing is done client-side with the
   wallet; the server only stores the signed payload (non-custodial).
2. **API keys** — `createApiKey` mints a programmatic key for server-to-server use.

The `gondi-js` SDK is initialized with a wallet client (`new Gondi({ wallet })`) and handles the SIWE
handshake automatically.

## References

- Endpoint: https://api.gondi.xyz/graphql
- Documentation: https://docs.gondi.xyz/
- SDK (JavaScript/TypeScript): https://github.com/gondixyz/gondi-js — `npm install gondi`
- SDK (Python): https://github.com/gondixyz/python-sdk
- dApp: https://www.gondi.xyz/
- Schema file: `gondi-schema.graphql` (this directory) — captured by introspection 2026-07-19.

## Notes

- **Introspection-derived.** `gondi-schema.graphql` is the verbatim SDL reconstructed from a live
  introspection query against the production endpoint; it reflects the schema as of 2026-07-19.
- **Non-custodial signing.** The `generate*OfferToBeSigned` / `saveSigned*` split is deliberate: the
  API returns the structured payload to sign, the wallet signs it locally, and only the signature +
  terms are persisted. Nothing in the API can move funds without a wallet signature.
- **On-chain settlement.** Loan origination, repayment, refinancing, and liquidation settle against
  the Gondi protocol smart contracts (see docs `Protocol Contracts`); the GraphQL API is the
  off-chain order book, indexer, and metadata layer.
