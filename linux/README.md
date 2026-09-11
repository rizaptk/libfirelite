# linux/

Linux binaries are distributed as the `libfirelite-<version>-linux.tar.gz`
**Release asset**, not stored in git. Contents (v0.7.13):

```text
libfirelite.so
firelite-cli
firelite-cloudserver  # managed sync hub + admin console (v0.8.0+)
benchmark
sqlite_bench
```

> The `sqlite_bench` binary is carried over from the v0.7.9 build:
> `bench/sqlite_bench.cpp` is unchanged since the multi-mode update, so the
> binary remains current. (The upstream v0.7.13 bundle shipped a stale
> single-mode build and is not used here.)

Extract into this directory:

```bash
tar xzf libfirelite-0.8.0-linux.tar.gz -C /path/to/libfirelite/linux
chmod +x firelite-cli firelite-cloudserver benchmark sqlite_bench  # required if the tarball lost exec bits
```

Verify (the v0.8.13rev1 `benchmark` carries an `$ORIGIN` rpath and finds
`libfirelite.so` beside itself — no `LD_LIBRARY_PATH` needed for it):

```bash
./benchmark --docs=1000
LD_LIBRARY_PATH=. ./sqlite_bench --docs=1000
./firelite-cloudserver --db-path ./cloud.db --admin-bind 127.0.0.1:8081 --sync-bind 0.0.0.0:8080
```

Build the harness from source against the release library:

```bash
g++ -O2 -std=c++17 -I../include ../bench/benchmark.cpp -L. -lfirelite -o benchmark
```
