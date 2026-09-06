# libfirelite

**Prebuilt FireLite library + SDK gateways + CLI + fair benchmarks (preview).**

> **License:** closed-source **evaluation license** — see [`LICENSE`](LICENSE).
> Evaluation, benchmarking and prototyping allowed; redistribution and production
> use require permission. Open-sourcing the engine is under consideration.

FireLite is an embedded, Firestore-style document database written in Rust.
It stores typed JSON-like documents in binary form, runs **fully in-process** like SQLite
(no server, no daemon, no network config), and exposes a **flat C ABI** so it can be embedded in
apps written in C/C++, Go, JavaScript/TypeScript (Node.js + Bun), Pascal/Lazarus, and more.

> **Status of this repo:** binary/SDK **preview** of FireLite **v0.7.9**.
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
  (central WebSocket hub + offline-first clients). Release libraries are built with
  `net-sync` and `cloud-sync` enabled, so the `fl_net_syncer_*` / `fl_cloud_sync_*`
  C ABI symbols are always present (build contract: `cargo build --release --features net-sync,cloud-sync`).

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
| `pascal/` | Lazarus/FPC wrapper (`FireLiteRaw.pas`, `FireLite.pas`, `FireLiteComponent.pas`, package `firelite.lpk`) |
| `bench/` | `benchmark.cpp` (official FireLite harness) + `sqlite_bench.cpp` (fair SQLite mirror) |

## Releases

Binaries are published as versioned **Release assets** and never committed to git:

| Asset | Contents |
|---|---|
| `libfirelite-0.7.9-windows.zip` | `firelite.dll`, `firelite.lib`, `firelite-cli.exe`, `benchmark.exe`, `sqlite_bench.exe` |
| `libfirelite-0.7.9-linux.tar.gz` | `libfirelite.so`, `firelite-cli`, `benchmark`, `sqlite_bench` |
| `libfirelite-0.7.9-android.tar.gz` | `jniLibs/arm64-v8a/libfirelite.so` |

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

Add `pascal/` to the unit path (`-Fu`), or install `pascal/firelite.lpk`
(`Package > Open Package File (.lpk)` → Compile → Install), then:

```pascal
DB := TFireLite.Create('./data.firelite');
Col.Doc('u1').SetDoc(TFLDocument.Create.InsertStr('name', 'alice'));
```

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

- **Windows** — `libfirelite-<version>-windows.zip` in **Releases**; `windows/` in git holds only `README.md`.
- **Linux** — `libfirelite-<version>-linux.tar.gz` in **Releases**; `linux/` in git holds only `README.md`.
- **Android** — `libfirelite-<version>-android.tar.gz` in **Releases** (`jniLibs/arm64-v8a`); see `android/README.md`.
- **macOS** — library-only distribution (`libfirelite.dylib` + `include/firelite.h`, not yet published);
  no CLI/benchmark binaries (no macOS build access). JS/Go/Pascal gateways link against the dylib.

## Version

This preview tracks engine **v0.7.9** (`VERSION`). Header, libraries, gateways and benchmarks
are all taken from the same engine revision.

## License

This repository is distributed under the [libfirelite Binary Evaluation License](LICENSE) —
**all rights reserved**. You may use the prebuilt binaries, headers and SDK gateways to
evaluate, benchmark (disclose version and settings when publishing results) and prototype
with FireLite. Redistribution, production deployment and any commercial use beyond evaluation
require a separate agreement. Open-source licensing is under consideration.

Third-party attributions for the statically linked open-source dependencies:
[THIRD-PARTY-NOTICES.md](THIRD-PARTY-NOTICES.md) (all permissive licenses; no GPL/LGPL/AGPL code linked).
