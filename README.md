# EVM Explorer

A block explorer for Ethereum compatible chains, based on [Blockscout](https://github.com/blockscout/blockscout) 4.1.

It indexes a chain through its JSON-RPC node and gives you an Etherscan style site: search for blocks, transactions, addresses and tokens, see balances and token transfers, read contract state and verify contract source code. It works with any EVM chain, including private networks and testnets.

## What's in it

- Blocks, transactions and internal transactions, updated live as new blocks come in
- Address pages with balances, token holdings and transaction history
- ERC-20, ERC-721 and ERC-1155 token pages with holders and transfers
- Smart contract verification, plus read and write tabs for verified contracts
- A REST and GraphQL API, including Etherscan compatible endpoints
- Charts for transactions per day and market data

## Tech stack

- Elixir 1.13 and Erlang/OTP 24 (Phoenix web app)
- PostgreSQL
- Node.js 16 for the frontend assets

The code is an Elixir umbrella app split into `apps/explorer` (data and database), `apps/indexer` (pulls data from the node), `apps/ethereum_jsonrpc` (talks to the node) and `apps/block_scout_web` (website and API).

## Running it with Docker

The quickest way is Docker Compose. You'll need Docker 20.10 or newer and a running JSON-RPC node for your chain.

```bash
git clone https://github.com/AI-pro017/evm-explorer.git
cd evm-explorer/docker-compose
docker-compose up --build
```

This starts PostgreSQL on port 7432 and the explorer at http://localhost:4000. Point it at your node by editing `docker-compose/envs/common-blockscout.env` (mainly `ETHEREUM_JSONRPC_HTTP_URL`, `ETHEREUM_JSONRPC_WS_URL` and `ETHEREUM_JSONRPC_VARIANT`).

There are ready made compose files for common setups:

| File | Use it for |
| --- | --- |
| `docker-compose-no-build-ganache.yml` | A local Ganache chain |
| `docker-compose-no-build-hardhat-network.yml` | A Hardhat node |
| `docker-compose-no-build-geth.yml` | Geth |
| `docker-compose-no-build-open-ethereum-nethermind.yml` | OpenEthereum or Nethermind |
| `docker-compose-no-build-no-db-container.yml` | Using your own database through `DATABASE_URL` |

## Running it without Docker

Install the versions in `.tool-versions` (asdf works well for this) and PostgreSQL, then:

```bash
mix do deps.get, local.rebar --force, deps.compile
mix do ecto.create, ecto.migrate
cd apps/block_scout_web/assets && npm install && node_modules/webpack/bin/webpack.js --mode production && cd -
cd apps/explorer && npm install && cd -
mix phx.server
```

Set `ETHEREUM_JSONRPC_HTTP_URL` and `DATABASE_URL` before starting. The full list of settings is in the [Blockscout docs](https://docs.blockscout.com/for-developers/information-and-settings/env-variables).

## License

GPL-3.0, same as Blockscout. See [LICENSE](LICENSE).
