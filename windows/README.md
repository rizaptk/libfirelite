# windows/

Windows binaries are distributed as the `libfirelite-<version>-windows.zip`
**Release asset**, not stored in git. Contents:

```text
firelite.dll            # engine (keep next to your .exe at runtime)
firelite.lib            # import library for MSVC / MinGW linking
firelite-cli.exe        # CLI tool
firelite-cloudserver.exe  # managed sync hub + admin console (v0.8.0+)
benchmark.exe           # official FireLite harness (bench/benchmark.cpp)
sqlite_bench.exe        # fair SQLite mirror (bench/sqlite_bench.cpp)
```

Extract into this directory, then verify:

```bash
cd windows
.\benchmark.exe --docs=1000
.\sqlite_bench.exe --docs=1000
.\firelite-cloudserver.exe --help
```

Run a hub (console on localhost, sync plane on all interfaces):

```bash
.\firelite-cloudserver.exe --db-path .\cloud.db --admin-bind 127.0.0.1:8081 --sync-bind 0.0.0.0:8080
```
