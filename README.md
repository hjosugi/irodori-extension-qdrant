# Qdrant Connector

Adds Qdrant vector database connectivity as an installable connector extension.

This connector is listed in the public Irodori extension marketplace.

## Connector

- Extension ID: `irodori.qdrant`
- Engine ID: `qdrant`
- Wire: `qdrant`
- Default port: `6333`
- Native ABI: `irodori.connector.native.v1`
- Driver linked: `false`

No desktop adapter source exists yet; this package starts from the refactored ABI shim and connector metadata.

Connector metadata lives in `connector.config.json` and `irodori.extension.json`.
The Rust code keeps native ABI exports in `src/lib.rs`, shared buffer/JSON helpers in `src/abi.rs`, and metadata-only behavior in `src/stub.rs` until the engine driver is linked.

## Connection Metadata

- Endpoint modes: `hostPort`, `connectionString`
- Transport modes: `direct`, `sshTunnel`, `socks5Proxy`, `httpConnectProxy`, `proxyChain`
- TLS supported: `true`
- Custom driver options: `true`

| Auth method | Label | Secret purposes |
|---|---|---|
| `none` | No authentication | none |
| `connectionString` | Connection string / DSN | none |
| `apiKey` | API key | `token` |
| `bearerToken` | Bearer token | `token` |
| `clientCertificate` | Client certificate / mTLS | `privateKey`, `privateKeyPassphrase` |
| `customDriverOptions` | Custom driver options | `password`, `token`, `privateKey`, `privateKeyPassphrase` |

## Experience Metadata

- Domains: `vector`
- Result views: `vectorNeighbors`, `table`, `json`
- Inspired by: `Qdrant Collections`, `Qdrant filtering`, `Qdrant payload indexes`

| Workflow | Result view | Templates |
|---|---|---|
| Similarity search | vectorNeighbors | vector-similarity |
| Filtered ANN search | vectorNeighbors | vector-filtered |
| Collection or index health | table | vector-health |

| Template | Label | Language | Result view |
|---|---|---|---|
| `vector-similarity` | Qdrant similarity search | `json` | `vectorNeighbors` |
| `vector-filtered` | Qdrant filtered search | `json` | `vectorNeighbors` |
| `vector-health` | Qdrant collection info | `text` | `json` |

## ABI Calls

The scaffold handles these JSON requests today:

| Method | Response |
|---|---|
| `health` / `ping` | Connector health, engine id, ABI version, and driver link status. |
| `describe` / `capabilities` | Embedded manifest and connector config. |
| `manifest` | Raw `irodori.extension.json`. |
| `config` | Raw `connector.config.json`. |


Driver operations such as `connect`, `query`, and `metadata` intentionally return `connector.driverNotLinked` until the engine implementation is connected.

## Development


Generated extension repositories share `../target` across sibling repositories so Rust dependencies are compiled once per checkout. DuckDB and MotherDuck are driver-linked by default; set `IRODORI_CONNECTOR_LINK_DUCKDB=0` only when you need metadata-only DuckDB-compatible scaffolds.


```sh
make check
make build
```

Release packages place platform-specific native artifacts under `dist/native`.
