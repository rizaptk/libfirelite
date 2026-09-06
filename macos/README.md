# macos/

macOS is a library-only distribution: `libfirelite.dylib` plus
`include/firelite.h`, published as a **Release asset** (not yet published for
v0.7.7 — no macOS build access). No `firelite-cli` / `benchmark` binaries.

Expected layout after extraction:

```text
macos/
  libfirelite.dylib
```

Link against the release library:

```bash
g++ -O2 -std=c++17 -I../include app.cpp -L. -lfirelite -o app
DYLD_LIBRARY_PATH=. ./app
```

```ts
// JS/TS SDK
const db = await FireLiteClient.open("./data.firelite", {
  libraryPath: "./macos/libfirelite.dylib",
});
```
