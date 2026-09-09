# libfirelite

**Prebuilt FireLite library + SDK gateways + CLI + fair benchmarks (preview).**

> **License:** closed-source **evaluation license** — see [`LICENSE`](LICENSE).
> Evaluation, benchmarking and prototyping allowed; redistribution and production
> use require permission. Open-sourcing the engine is under consideration.

FireLite is an embedded, Firestore-style document database written in Rust.
It stores typed JSON-like documents in binary form, runs **fully in-process** like SQLite
(no server, no daemon, no network config), and exposes a **flat C ABI** so it can be embedded in
apps written in C/C++, Go, JavaScript/TypeScript (Node.js + Bun), Pascal/Lazarus, and more.

> **Status of this repo:** binary/SDK **preview** of FireLite **v0.8.0**.
> It exists to share and try the library. Open-sourcing the engine itself is still under consideration —
> the Rust source is **not** included here. See [License](#license).

## What is FireLite?

- **Firestore-like ergonomics** — collections, documents, `set/get/delete`, fluent queries
  (`where` / `orderBy` / `limit` / `offset` / cursors), real-time `watch_collection` streams.
- **SQLite-style embedding** — link the library into your process, open a file, done.
- **Durable storage** — WAL + tiered-segment engine with configurable durability
  (`Always`, `Interval`, `Manual`, `OnCommit`) and crash recovery.
- **Encryption at rest** — ChaCha20-Poly1305 for WAL and segment payloads.
- **Indexing** — secondary B-Tree, composite, and full-text (FTS) indexes, built in the background.
- **Zero-copy projection** — queries cherry-pick fields from memory-mapped binary slices;
  deferred blob fetching (`defer_blobs`) keeps list views fast.
- **Optional sync** — **Net Sync** (LAN mesh, mDNS) and **Cloud Sync**
  (central WebSocket hub + offline-first clients). Since v0.7.13 sync is part of the
  default build, so the `fl_net_syncer_*` / `fl_cloud_sync_*` C ABI symbols are
  always present in release libraries (build contract: plain `cargo build --release`).

## Contents

| Path | What |
|---|---|
| `include/firelite.h` | C ABI header (all languages build against this) |
| `windows/` | Per-version zip from **Releases** (`firelite.dll`, `firelite.lib`, `firelite-cli.exe`, `benchmark.exe`, `sqlite_bench.exe`) — see `windows/README.md` |
| `linux/` | Per-version tarball from **Releases** (`libfirelite.so`, `firelite-cli`, `benchmark`, `sqlite_bench`) — see `linux/README.md` |
| `android/` | Per-version tarball from **Releases** (`jniLibs/arm64-v8a/libfirelite.so`) — see `android/README.md` |
| `macos/` | Library-only (`libfirelite.dylib`, not yet published) — no CLI/benchmark binaries (no macOS build access) — see `macos/README.md` |
| `go/` | Go cgo gateway (`firelite.go`, `firelite_c.h`, `go.mod`) |
| `js/` | JS/TS SDK over C-FFI (`client.ts`, `native.ts`, koffi for Node, `bun:ffi` for Bun). Tauri gateway excluded — it needs the engine source |
| `pascal/` | Lazarus/FPC wrapper (`FireLiteRaw.pas`, `FireLite.pas`, `FireLiteComponent.pas`; packages `FireLitePkg.lpk` runtime + `FireLiteDesign.lpk` designtime) |
| `bench/` | `benchmark.cpp` (official FireLite harness) + `sqlite_bench.cpp` (fair SQLite mirror) |

## Releases

Binaries are published as versioned **Release assets** and never committed to git:

| Asset | Contents |
|---|---|
| `libfirelite-0.8.0-windows.zip` | `firelite.dll`, `firelite.lib`, `firelite-cli.exe`, `firelite-cloudserver.exe`, `benchmark.exe`, `sqlite_bench.exe` |
| `libfirelite-0.8.0-linux.tar.gz` | `libfirelite.so`, `firelite-cli`, `firelite-cloudserver`, `benchmark`, `sqlite_bench` (binary carried over — source unchanged) |
| `libfirelite-0.8.0-android.tar.gz` | `jniLibs/arm64-v8a/libfirelite.so` |

Extract the archive for your platform into the matching directory (`windows/`, `linux/`,
or your app's `jniLibs/` for Android). Prior releases keep their own versioned assets.

## Quick use

### C / C++ (Windows)

```c
#include "firelite.h"
// link: firelite.lib  |  run: firelite.dll next to your .exe
FL_Engine* db = fl_engine_open("./data.firelite");
FL_Doc* d = fl_doc_new();
fl_doc_insert_str(d, "name", "alice");
fl_engine_insert(db, "users", "alice", d);
fl_doc_free(d);
fl_engine_free(db);
```

```bash
# prerequisite: Windows release archive extracted into windows/
g++ -O2 -Iinclude app.cpp -Lwindows -lfirelite -o app.exe
```

### CLI (Windows)

Prerequisite: extract the Windows release archive into `windows/`:

```bash
windows\firelite-cli.exe --db .\demo.db seed users 100
windows\firelite-cli.exe --db .\demo.db set users/alice --data '{"name":"Alice","age":30}'
windows\firelite-cli.exe --db .\demo.db get users/alice
windows\firelite-cli.exe --db .\demo.db query users --where age:gte:21 --order name:asc --limit 10
```

### Go

The gateway defaults to the engine's own `target/` tree; override the link path
to point at the extracted release library:

```bash
CGO_LDFLAGS="-L${PWD}/windows -lfirelite" go build ./...
```

```go
db, _ := firelite.Open("./data.firelite")
defer db.Close()
doc := db.NewDoc().SetString("name", "alice").SetInt("age", 30)
_ = db.Put("users", "alice", doc)
```

### JavaScript / TypeScript

```bash
cd js && npm install
```

```ts
import { FireLiteClient } from "@firelite/client";
const db = await FireLiteClient.open("./data.firelite", {
  libraryPath: "../windows/firelite.dll", // or linux/libfirelite.so, macos/libfirelite.dylib
});
await db.collection("users").doc("alice").set({ name: "Alice", age: 30 });
```

### Pascal / Lazarus

Add `pascal/` to the unit path (`-Fu`), or install the packages — runtime and
designtime are split (a single mixed package will not install):

- `pascal/FireLitePkg.lpk` — **runtime**: reference it from Project Inspector
  to use the SDK from code. Never install this one.
- `pascal/FireLiteDesign.lpk` — **designtime**: `Package > Open Package File (.lpk)`
  → Compile → **Install**. A **FireLite** tab with `TFireLiteComponent` (palette
  icon included) appears on the component palette.

```pascal
DB := TFireLite.Create('./data.firelite');
Col.Doc('u1').SetDoc(TFLDocument.Create.InsertStr('name', 'alice'));
```

Sync on the component is opt-in: `NetSyncEnabled` / `CloudSyncEnabled` default to
`False` and the remaining sync properties (`NetSyncName`, `NetSyncRoomKey`,
`NetSyncPort`, `NetSyncDiscovery`, `CloudSync*`) are inert until enabled —
`StartNetSync` / `StartCloudSync` raise otherwise.

## Net Sync discovery (v0.7.8+)

LAN peer discovery is developer-chosen per syncer, symmetric across platforms:

| Mode | Value | Transport | Default on |
|---|---|---|---|
| mDNS | `0` | multicast browse/register | desktop |
| Broadcast | `1` | UDP beacons on `255.255.255.255:5354`, no `MulticastLock` | mobile (Android) |
| Both | `2` | mDNS + broadcast | opt-in (mixed groups) |

Beacons carry `{id, room_hash, tcp_port, known_peers}`; receivers use the UDP
source IP (multi-interface safe) and merge gossiped peers, so finding one peer
bootstraps the group. Defaults preserve pre-0.7.8 behavior with zero config.
**Mixed-group recipe:** whoever hears, dials — but a default desktop never hears
broadcast-only mobile peers, so opt the desktop side into `Both` once.

```bash
# CLI: desktop joining mobile peers
firelite-cli --db ./demo.db serve --port 7070 --node-id node-1 --key my-room-key --discovery both
```

```c
fl_net_syncer_set_discovery(syncer, 2);   // takes effect at the next start()
```

```go
syncer.SetDiscoveryMode(firelite.DiscoveryBoth)  // DiscoveryMdns / DiscoveryBroadcast / DiscoveryBoth
```

```ts
await syncer.setDiscoveryMode(2);  // 0 | 1 | 2
```

```pascal
Syncer.SetDiscoveryMode(dmBoth);  // dmMdns / dmBroadcast / dmBoth (default dmMdns)
```

See `android/README.md` for Android specifics (permissions, Doze, expiry).

## Engine notes (v0.7.11–v0.7.13)

- **WAL history compaction (v0.7.11).** `compact()` now rewrites the WAL snapshot
  whenever stale history dominates (past the compaction threshold and over ~3x
  live inlined bytes), even with zero segments to merge — previously small-but-hot
  collections accumulated unreclaimable WAL history. The same bounded rewrite runs
  once at open (best-effort). Existing `compact` CLI/FFI paths reclaim automatically.
- **`wal_reserve_bytes` defaults to 0 (v0.7.12).** A/B measured no throughput delta
  on fsync-bound workloads, so no phantom size by default (matters on mobile
  storage). Opt back in via `fl_config_set_wal_reserve_bytes` if a long-soak test
  ever shows fragmentation-driven fsync decay.
- **Sync in default features (v0.7.13).** Release libraries are built with plain
  `cargo build --release` — `net-sync` and `cloud-sync` are now default features,
  so the `fl_net_syncer_*` / `fl_cloud_sync_*` symbols are always present.
  (Older cuts required `--features net-sync,cloud-sync`.)

## firelite-cloudserver (managed sync hub + admin console) — v0.8.0+

Standalone console binary for operators who outgrow `firelite-cli serve`: a
room-agnostic sync hub plus an admin web console (embedded HTML + SSE), one
process, two ports. Shipped in the Windows zip and Linux tarball as
`firelite-cloudserver[.exe]`; not built for Android/macOS.

```bash
firelite-cloudserver --db-path ./cloud.db --admin-bind 127.0.0.1:8081 --sync-bind 0.0.0.0:8080
```

Configuration layers (later wins): compiled defaults < `./firelite-cloud.toml`
(auto-loaded when present) < `FL_*` env (`FL_DB_PATH`, `FL_ADMIN_BIND`,
`FL_SYNC_BIND`, `FL_LOG_LEVEL`, `FL_SECURE_COOKIES`, `FL_SERVER_ID`,
`FL_SYNC_TOKEN`, `FL_TLS_CERT`, `FL_TLS_KEY`) < CLI flags.

```toml
db_path = "/var/lib/firelite-cloud/db"
admin_bind = "127.0.0.1:8081"
sync_bind = "0.0.0.0:8080"
log_level = "info"
```

- **First run:** open the console — with no admin account present only the setup
  wizard is reachable. Create the initial administrator; the wizard disables
  itself permanently. Roles: `viewer` (read), `operator` (read + write data),
  `admin` (everything incl. users, groups, maintenance).
- **Groups:** rooms accept anonymous peers unless you create a **group** for the
  room name: `registered` mode issues an API key (shown once, only its hash
  persists) presented at handshake; an optional member list pins allowed
  `client_id`s. Absent groups stay open, so existing deployments keep working.
- **Topology advice:** point one (two for redundancy) always-on peer per site at
  the hub; let the rest mesh peer-to-peer locally. Hubs converge through the
  server; LAN traffic never leaves the site.
- **TLS and services:** `--tls-cert` + `--tls-key` must come as a pair
  (fail-closed); session cookies flip `Secure` automatically. Linux: hardened
  systemd unit (upstream `contrib/`); Windows: `--install-service` (absolute
  `--db-path`, auto-start at boot) / `--uninstall-service`. Never bind the
  console to `0.0.0.0` without TLS — the server logs a loud warning.

Point embedded clients at the hub as with CLI cloud-server mode
(`serve --server ws://hub:8080 ...` with matching room, key and token).

### Encrypted sync posture (v0.7.14+, fail-closed)

Encryption at rest and sync-time plaintext are **independent**: tailers emit
decoded documents, so an encrypted collection replicates as **plaintext on the
wire** unless refused — there is deliberately no silent path. Handshakes carry
key fingerprints (mesh `SyncCaps`, cloud `enc_fp`/`enc_cols`); senders skip,
receivers drop and relays filter per recipient, all with throttled warnings.
Put the same `encryption_key` on every node sharing the room; expect
`[sync-guard]` warnings for keyless/wrong-key/old peers. No override flag by
design. Not protected: cloud operator visibility, passive LAN observers (no E2E
yet), impersonators replaying fingerprints (assertions, not proofs).

## Benchmark

`bench/benchmark.cpp` is the **official harness**: it drives the engine only through the public
C ABI and reports throughput (ops/sec) per profile. `bench/sqlite_bench.cpp` is a **fair SQLite
mirror**: same documents, same indexes, same loop counts, same math, same matrix columns.

### What is measured

| Column | Stages |
|---|---|
| `WPS (Sgl/Btc)` | 100 single writes, then batch writes (`--docs - 100`, 10 per batch) |
| `RPS (Seq/Par)` | 200 sequential point-gets, then threaded point-gets |
| `STRESS (Get/Qry/Cmp)` | 15 000 rotating point-gets, indexed filter query, composite (filter + sort) query |
| `QPS (Off/Cur)` | Offset pagination vs keyset-cursor pagination (300 iterations each) |
| `Agg QPS` | Full-scan aggregation (50×) |
| `Tx WPS` | Read-modify-write transactions (50×) |
| `Bulk Upd/Del` | 100-doc bulk update, 100-doc bulk delete |
| `Startup/Flush` | Engine open (ms) / clean shutdown (ms) |
| `Size` | On-disk size |

### Run (Windows, release binaries)

```bash
# prerequisite: Windows release archive extracted into windows/
cd windows
.\benchmark.exe --docs=1000      # 6 FireLite profiles
.\sqlite_bench.exe --docs=1000   # 4 SQLite durability modes (default matrix)
.\sqlite_bench.exe --docs=1000 --sync=FULL --journal=DELETE  # single custom mode
```

### Build from `bench/` yourself

```bash
# FireLite (needs the release lib + header)
g++ -O2 -std=c++17 -I../include benchmark.cpp -L../windows -lfirelite -o benchmark.exe
# SQLite (needs sqlite3 dev files)
g++ -O2 -std=c++17 -o sqlite_bench.exe sqlite_bench.cpp -lsqlite3
```

Linux/macOS equivalents use `-L../linux` / `-L../macos` and `-lfirelite`
(`LD_LIBRARY_PATH` / `DYLD_LIBRARY_PATH` at runtime).

### Fairness rules (read before comparing)

1. **Durability parity.** Default comparison is FireLite `Manual` profile vs
   SQLite `Manual` mode (`synchronous=OFF` + `journal_mode=MEMORY`, both RAM-speed).
   Row-to-row: `Always`↔`Always` (fsync every write), `Interval`↔`Interval`,
   `OnCommit`↔`OnCommit`. For one combo only: `sqlite_bench --sync=FULL --journal=DELETE`.
2. **Same `--docs`** (≥ 1000 for meaningful numbers) and same `--threads` on both sides.
3. **Full-row decode on both sides** — every SQLite `SELECT` reads all columns,
   mirroring FireLite's full-document decode.
4. **Same loop counts** — 300 iterations for queries, 15 000 stress gets, 50 transactions.
5. `QryLazy` in the SQLite output is an **extra diagnostic** (id-column-only read),
   not part of the fair comparison.
6. SQLite modes run sequentially in one invocation, so later rows benefit from a warm
   OS page cache. For strict isolation, run one custom mode at a time per bullet 1.

### Comparison targets

| Target | Goal |
|---|---|
| SQLite (`sqlite_bench`) | Reference baseline: identical workload shapes under matched durability settings |
| FireLite profiles | `Always` / `Interval` / `Manual` / `OnCommit` / `Enc_Comp` / `Gaming` against each other |

## Platform notes

- **Windows** — `libfirelite-<version>-windows.zip` in **Releases** (engine lib + CLI + cloudserver + benchmarks); `windows/` in git holds only `README.md`.
- **Linux** — `libfirelite-<version>-linux.tar.gz` in **Releases** (engine lib + CLI + cloudserver + benchmarks); `linux/` in git holds only `README.md`.
- **Android** — `libfirelite-<version>-android.tar.gz` in **Releases** (`jniLibs/arm64-v8a`); engine library only, no server/CLI binaries; see `android/README.md`.
- **macOS** — library-only distribution (`libfirelite.dylib` + `include/firelite.h`, not yet published);
  no CLI/benchmark binaries (no macOS build access). JS/Go/Pascal gateways link against the dylib.

## Version

This preview tracks engine **v0.8.0** (`VERSION`). Header, libraries, gateways and benchmarks
are all taken from the same engine revision.

## License

This repository is distributed under the [libfirelite Binary Evaluation License](LICENSE) —
**all rights reserved**. You may use the prebuilt binaries, headers and SDK gateways to
evaluate, benchmark (disclose version and settings when publishing results) and prototype
with FireLite. Redistribution, production deployment and any commercial use beyond evaluation
require a separate agreement. Open-source licensing is under consideration.

Third-party attributions for the statically linked open-source dependencies:
[THIRD-PARTY-NOTICES.md](THIRD-PARTY-NOTICES.md) (all permissive licenses; no GPL/LGPL/AGPL code linked).
