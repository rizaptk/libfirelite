# linux/

Linux binaries are distributed as the `libfirelite-<version>-linux.tar.gz`
**Release asset**, not stored in git. Contents:

```text
libfirelite.so
firelite-cli
benchmark
sqlite_bench
```

Extract into this directory:

```bash
tar xzf libfirelite-0.7.7-linux.tar.gz -C /path/to/libfirelite/linux
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
