# libfirelite

Prebuilt FireLite engine library, SDK gateways, CLI, sync hub, and benchmarks.

> **License:** closed-source [evaluation license](LICENSE) — read the terms before using.

FireLite is an embedded, Firestore-style document database written in Rust.
It stores typed JSON-like documents in binary form, runs fully in-process like
SQLite (no server process to operate for the engine itself), and exposes a flat
C ABI for C/C++, Go, JavaScript/TypeScript (Node.js + Bun), Pascal/Lazarus, and more.

This repo tracks engine **v0.8.18** and contains binaries + SDK sources only.
The engine source is not included.

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
- **Optional sync** — **Net Sync** (LAN mesh) and **Cloud Sync** (central WebSocket
  hub + offline-first clients). Release libraries are built with sync enabled
  (plain `cargo build --release`), so `fl_net_syncer_*` / `fl_cloud_sync_*` are present.

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
| `docs/` | `reads.md` (choosing a read path) + `benchmarking.md` (methodology, scoreboard) |

## Releases

Binaries are published as versioned **Release assets** and never committed to git:

| Asset | Contents |
|---|---|
| `libfirelite-0.8.18-windows.zip` | `firelite.dll`, `firelite-cli.exe`, `firelite-cloudserver.exe`, `benchmark.exe`, `sqlite_bench.exe` (no import lib — MinGW links the DLL directly, see `windows/README.md`) |
| `libfirelite-0.8.18-linux.tar.gz` | `libfirelite.so`, `firelite-cli`, `firelite-cloudserver`, `benchmark`, `sqlite_bench` |
| `libfirelite-0.8.18-android.tar.gz` | `jniLibs/arm64-v8a/libfirelite.so` |

Extract the archive for your platform into the matching directory (`windows/`, `linux/`,
or your app's `jniLibs/` for Android). Prior releases keep their own versioned assets.

## Usage

### C / C++ (Windows)

```c
#include "firelite.h"
// MinGW links the DLL directly (no import lib shipped). MSVC is unsupported
// upstream — generate your own import lib if required.
FL_Engine* db = fl_engine_open("./data.firelite");
FL_Doc* d = fl_doc_new();
fl_doc_insert_str(d, "name", "alice");
fl_engine_insert(db, "users", "alice", d);
fl_doc_free(d);
fl_engine_free(db);
```

```bash
# Windows release archive extracted into windows/
g++ -O2 -Iinclude app.cpp -Lwindows -lfirelite -o app.exe
```

### CLI (Windows)

Extract the Windows release archive into `windows/`, then run commands against a database:

```bash
windows\firelite-cli.exe --db .\demo.db seed users 100
windows\firelite-cli.exe --db .\demo.db set users/alice --data '{"name":"Alice","age":30}'
windows\firelite-cli.exe --db .\demo.db get users/alice
windows\firelite-cli.exe --db .\demo.db query users --where age:gte:21 --order name:asc --limit 10
```

One-shot commands wait (≤30s) for background index recovery after open.
Queries issued first would otherwise silently degrade (cursor bounds ignored, pages repeat).

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

## Lazy reads: raw rows, cursor walks, typed views (v0.8.2+)

Three borrowed (zero-alloc) read shapes for scan-many/touch-few workloads.
Rows are lent, never owned — valid for the call/slab only; resolve selected rows
with `to_doc`. Blob fields never inflate inside views.

| Shape | C ABI | Go | JS | Pascal |
|---|---|---|---|---|
| Raw page (pinned storage bytes) | `fl_query_execute_raw`, `fl_rawdoc_bytes`/`_id`, `fl_query_start_after_raw`, `fl_rawdoc_to_doc` | `ExecuteQueryRaw`, `RawDoc.Bytes`/`ID`, `StartAfterRaw`, `ToDoc` | `getRaw()`, `RawQuerySnapshot.resolve`, `startAfterRaw` | `TFLQuery.ExecuteRaw`, `TFLRawDoc`, `StartAfterRaw` |
| Cursor walk (one FFI call per scan, early-stop) | `fl_cursor_walk` + `FlWalkCallback` | `Engine.CursorWalk` + `WalkCallback` | — (use raw paging; a walk you can't touch rows in is a counting loop) | `TFLQuery.Walk` + `TFL_WalkCallback` |
| Typed view (lazy per-field pulls) | `fl_view_get`, `fl_view_get_int/float/bool/str/bytes`, `fl_view_to_doc`, `fl_cursor_walk_view` | `GetView`, `GetInt/Float/Bool/String/Bytes`, `HasField`, `ToDoc`, `CursorWalkView` | `viewDoc()`, `ViewDocSnapshot` | `TFireLite.GetView`, `TFLViewDoc`, `TFLQuery.WalkView` |

```c
// byte-level walk: count rows, touch nothing owned (false stops early)
int64_t n = fl_cursor_walk(db, q, cb, &ctx);
```

```go
n, _ := db.CursorWalk(q, func(id string, bytes []byte) bool { return true })
```

Read-path guide (which shape for which workload): `docs/reads.md`.

## Benchmark

`bench/benchmark.cpp` drives the engine only through the public C ABI and reports
throughput (ops/sec) per profile. `bench/sqlite_bench.cpp` is the matching SQLite
mirror: same documents, indexes, loop counts, math and table columns.

### Settling before measuring

A freshly written database is not settled: index recovery, async index updates,
blob persistence and periodic maintenance run in the background. Reads issued
mid-flight measure contention (observed 1000x swings), so settle first:

```c
fl_engine_await_quiescent(db, 30000);  // true when settled (two consecutive clear samples)
char* s = fl_engine_quiescence_status(db);  // JSON diagnostic, free with fl_string_free
```

`is_indexes_ready` covers stage one of four — enough for correct plans, not stable
numbers. To hold checkpoint/compaction/purge/snapshots for flat bench rounds:

```c
fl_config_set_background_maintenance(cfg, false);  // default on; files grow until re-enabled
```

SDKs: Go `Config.SetBackgroundMaintenance`, Pascal config setter, JS
`configSetBackgroundMaintenance` (both loaders).

Full methodology, equal-work table and scoreboard: `docs/benchmarking.md`.

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

After the matrix, a **FULL SCAN** section (×5 iters over all live docs, 1:1 on both
harnesses): decoded forward/reverse pages, byte/key-only scan (`fl_cursor_walk` vs
id-column select), and the lazy stage (2-pull view walk vs narrow id/tenant/age
select). `benchmark --gate` enforces the regression gate on median-of-3 Manual runs
(`Qry>=0.85Cmp`, Off/Cur within 2×, `Get>5xQry`, `Batch>=0.5Single` + smoke floors).

### Run (Windows, release binaries)

```bash
# Windows release archive extracted into windows/
cd windows
.\benchmark.exe --docs=1000      # 6 FireLite profiles
.\benchmark.exe --gate           # median-of-3 regression gate (Manual)
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

### Fairness rules

1. **Settle first.** Never measure against a freshly written database without
   settling (`fl_engine_await_quiescent`) — see above and `docs/benchmarking.md`.
2. **Durability parity.** Compare FireLite `Manual` against SQLite `Manual`
   (`synchronous=OFF` + `journal_mode=MEMORY`, both RAM-speed). Row-to-row:
   `Always`↔`Always` (fsync every write), `Interval`↔`Interval`, `OnCommit`↔`OnCommit`.
   One combo only: `sqlite_bench --sync=FULL --journal=DELETE`.
3. **Same `--docs`** (≥ 1000 for meaningful numbers) and same `--threads` on both sides.
4. **Full-row decode on both sides** — every SQLite `SELECT` reads all columns,
   mirroring FireLite's full-document decode.
5. **Same loop counts** — 300 iterations for queries, 15 000 stress gets, 50 transactions.
6. `QryLazy` in the SQLite output is an **extra diagnostic** (id-column-only read),
   not part of the comparison.
7. SQLite modes run sequentially in one invocation, so later rows benefit from a warm
   OS page cache. For strict isolation, run one custom mode at a time per rule 2.

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

This repo tracks engine **v0.8.18** (`VERSION`). Header, libraries, gateways and benchmarks
are all taken from the same engine revision.

## License

[Binary Evaluation License](LICENSE) — all rights reserved. In practice:

- OK: evaluating, benchmarking (mention version + settings when publishing numbers), prototyping.
- Not OK: redistributing the binaries, running them in production, or any commercial
  use beyond evaluation — that needs a separate agreement.

Third-party attributions for the statically linked open-source dependencies:
[THIRD-PARTY-NOTICES.md](THIRD-PARTY-NOTICES.md) (all permissive licenses; no GPL/LGPL/AGPL code linked).
