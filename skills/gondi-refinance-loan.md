---
name: Refinance / renegotiate a Gondi loan
description: Offer to refinance or renegotiate an outstanding loan by improving the APR.
api: graphql/gondi-schema.graphql
endpoint: https://api.gondi.xyz/graphql
operations: [listLoans, generateRenegotiationOfferToBeSigned, saveRenegotiationSignedOffer]
---

# Refinance / renegotiate a Gondi loan

Lenders can improve an outstanding loan's terms (lower APR) via full, partial (tranche), or
trim-the-top refinancing. Like offers, renegotiations are signed client-side.

## Steps

1. **Find the loan.** Query `listLoans` to locate the target loan and its current terms/tranches.
2. **Generate the renegotiation payload.** Call `generateRenegotiationOfferToBeSigned` with the
   loan and the improved terms; it returns the payload to sign.
3. **Sign locally.** Sign the payload with the lender's wallet.
4. **Save it.** Call `saveRenegotiationSignedOffer` with the signature to publish the renegotiation
   offer.

## Rules

- Refinancing must improve terms for the borrower (e.g., reduce APR) per protocol rules.
- Non-custodial: the wallet signs; the API only stores the signed payload.
- Settlement occurs against the Gondi protocol contracts on-chain.
- See docs `Refinancing` / `Renegotiations` and `conventions/gondi-conventions.yml`.
