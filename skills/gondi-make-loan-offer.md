---
name: Make a loan offer on Gondi
description: Create and submit a signed single-NFT or collection-wide loan offer as a lender.
api: graphql/gondi-schema.graphql
endpoint: https://api.gondi.xyz/graphql
operations: [signInWithEthereum, generateSingleNftOfferToBeSigned, saveSignedSingleNftOffer, generateCollectionOfferToBeSigned, saveSignedCollectionOffer, listOffers, hideOffer]
---

# Make a loan offer on Gondi

As a lender, propose loan terms (principal, APR, due date, optional origination fee) on a
single NFT or across a whole collection. Signing is non-custodial: the API returns the payload
to sign, the wallet signs it, and the signed offer is persisted.

## Prerequisites

- An authenticated session (see `gondi-authenticate.md`).
- A wallet funded in the loan currency (WETH, HYPE, or USDC).

## Steps — single-NFT offer

1. **Generate the payload.** Call `generateSingleNftOfferToBeSigned` with the NFT and the loan
   terms; it returns the structured offer to sign.
2. **Sign locally.** Sign the returned payload with the lender's wallet.
3. **Save the signed offer.** Call `saveSignedSingleNftOffer` with the signature to publish it.

## Steps — collection-wide offer

1. Call `generateCollectionOfferToBeSigned` with the collection and terms.
2. Sign the payload with the wallet.
3. Call `saveSignedCollectionOffer` to publish.

## Manage offers

- List your offers with the `listOffers` query.
- Withdraw one from the book with the `hideOffer` mutation.

## Rules

- Follow the generate → sign → save convention; never fabricate the payload to sign.
- The signed offer is unique, which de-duplicates it (Gondi documents no Idempotency-Key header).
- See `conventions/gondi-conventions.yml` for the signing model.
