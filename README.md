# Arkiv Live Wire — Ticket Gate

A ticket board that updates **only from an Arkiv subscription** — no refresh loop, no `setInterval` fetch.
Built during an internal ETHROME dry-run for Arkiv's Mission 03 ("Live Wire").
Findings from the dry-run are in [docs/REPORT.md](docs/REPORT.md).

- Rows arrive from `client.watchEntityEvents()` over a `webSocket()` transport (viem uses `eth_subscribe`, so it is a true push).
- The countdown runs on `client.watchBlockNumber()` over the same socket.
- When `expiresAt` passes, the ticket is struck out and disappears. The chain emits **no expiry event** — expiry is derived from the block number.

Network: Tiramisu testnet (chain id `7738577`), `@arkiv-network/sdk` 0.8.0.

## Try it

1. Open the deployed page (or `http://127.0.0.1:4173/index.html` locally).
2. Go to https://stage.hub.arkiv.network/try-it-out and create an entity with the attribute `match = str('AS Roma vs Inter')` and a short expiry.
3. The row appears without a page refresh; when the expiry block passes it disappears.

Filter for a different match with `?match=Your%20Match%20Name` — the board queries `match = str('<value>')`.

## Run locally

```bash
npm install
npm run build
npm run serve      # http://127.0.0.1:4173/index.html
npm run watch -- 0xYourAddress   # optional terminal log, filtered by owner
```

## Docker

```bash
docker build -t arkiv-livewire .
docker run --rm -p 8080:80 arkiv-livewire   # http://localhost:8080
```

Static site: esbuild bundle served by nginx. The browser connects directly to the public Tiramisu WebSocket RPC; there is no backend.
