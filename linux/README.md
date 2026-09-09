# linux/

Linux binaries are distributed as the `libfirelite-<version>-linux.tar.gz`
**Release asset**, not stored in git. Contents (v0.7.13):

```text
libfirelite.so
firelite-cli
benchmark
sqlite_bench
```

> The `sqlite_bench` binary is carried over from the v0.7.9 build:
> `bench/sqlite_bench.cpp` is unchanged since the multi-mode update, so the
> binary remains current. (The upstream v0.7.13 bundle shipped a stale
> single-mode build and is not used here.)

Extract into this directory:

```bash
tar xzf libfirelite-0.7.13-linux.tar.gz -C /path/to/libfirelite/linux
chmod +x firelite-cli benchmark sqlite_bench  # required if the tarball lost exec bits
```

Verify:

```bash
LD_LIBRARY_PATH=. ./benchmark --docs=1000
LD_LIBRARY_PATH=. ./sqlite_bench --docs=1000
```

Build the harness from source against the release library:

```bash
g++ -O2 -std=c++17 -I../include ../bench/benchmark.cpp -L. -lfirelite -o benchmark
```
