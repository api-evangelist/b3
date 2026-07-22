---
name: Query B3 ecosystem onchain data
description: >-
  Use the free public B3 Data API (Blockscout + Thirdweb Insights cached proxy)
  to look up transactions, blocks, addresses, tokens and NFTs across B3 chains.
api: openapi/b3-data-api-openapi.json
operations:
  - search
  - get_stats
  - get_txs
  - get_tx
  - get_blocks
  - get_block
  - get_address
  - get_address_txs
  - get_address_token_balances
  - get_address_nft
  - get_tokens_list
  - get_token
generated: '2026-07-22'
method: generated
---

# Query B3 ecosystem onchain data

Base URL: `https://data-api.b3.fun`. Public endpoints need **no authentication**.
Responses are cached at the edge (24-hour cache with automatic multi-chain
fallback). All operations are read-only GETs — safe for agent use.

## Steps

1. **Find the thing.** `search` (`GET /blockscout/search?q=...`) resolves free-text
   queries to addresses, tokens, blocks or transactions.
2. **Chain stats.** `get_stats` (`GET /blockscout/stats`) returns network counters;
   `get_txs_chart` / `get_market_chart` return time series.
3. **Transactions.** `get_txs` lists recent transactions; drill in with
   `GET /blockscout/transactions/{transaction_hash}` and its token-transfers,
   logs, internal-transactions and summary sub-resources.
4. **Blocks.** `get_blocks` / `get_block` (`GET /blockscout/blocks/{block_number_or_hash}`)
   plus per-block transactions and withdrawals.
5. **Addresses.** `get_address` for info, `get_address_txs` for activity,
   `get_address_token_balances` for ERC-20 holdings, `get_address_nft` /
   `get_address_nft_collections` for NFTs.
6. **Tokens.** `get_tokens_list` to browse, `get_token` for one token, and
   `GET /blockscout/tokens/{address_hash}/transfers` for its transfer history.
7. **Richer analytics.** The `/insights/*` operations (Thirdweb Insights) cover
   decoded events, contract metadata and NFT analytics; thirdweb
   `x-client-id` / `x-secret-key` headers are declared for Insights passthrough.

## Rules

- Multi-chain: the API routes across supported chains (B3 mainnet, B3 Testnet
  chainId 1993, partner appchains) — see `https://data-api.b3.fun/` for the live
  `supported_chains` list; testnet chains are the test surface
  (`sandbox/b3-sandbox.yml`).
- Data is cached — do not treat responses as real-time confirmation of a
  just-submitted transaction.
