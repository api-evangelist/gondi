---
name: Authenticate to Gondi with Sign-In With Ethereum
description: Establish an authenticated Gondi session using SIWE (EIP-4361) and optionally mint an API key.
api: graphql/gondi-schema.graphql
endpoint: https://api.gondi.xyz/graphql
operations: [generateSignInNonce, signInWithEthereum, createApiKey]
---

# Authenticate to Gondi (Sign-In With Ethereum)

Gondi's GraphQL API (`https://api.gondi.xyz/graphql`) authenticates users with
**Sign-In With Ethereum** (EIP-4361). Everything that moves value is additionally gated
by a wallet signature, so the session only proves wallet ownership.

## Steps

1. **Request a nonce.** Call the `generateSignInNonce` mutation to get a fresh nonce for
   the connecting wallet address.
2. **Build and sign the SIWE message.** Construct the EIP-4361 message with the nonce and
   sign it locally with the user's wallet (the `gondi-js` SDK uses a `viem` wallet client;
   `new Gondi({ wallet })` does this automatically).
3. **Sign in.** Call `signInWithEthereum` with the signed message/signature to establish the
   session.
4. **(Optional) Mint an API key.** For server-to-server access, call `createApiKey` once
   authenticated and store the returned key securely.

## Rules

- Never send a private key to the API. Signing is client-side; the server only verifies.
- Reuse one nonce for exactly one `signInWithEthereum` call.
- Prefer the `gondi-js` SDK, which handles the handshake; drop to raw GraphQL only when needed.
- See `authentication/gondi-authentication.yml` and `conventions/gondi-conventions.yml`.
