# Third-party notices

The prebuilt libfirelite binaries statically link the following open-source
Rust crates (engine v0.8.0 + CLI + cloudserver dependency closure, runtime-linked set).
All are under permissive licenses (MIT / Apache-2.0 / BSD / ISC / Unlicense /
CC0 / Unicode-3.0 / BSL-1.0). No GPL / LGPL / AGPL / MPL-linked code is included.
Full license texts: https://spdx.org/licenses/ and https://crates.io/crates/<name>.
Build-only tools (e.g. cbindgen) and first-party crates are excluded.

| Crate | Version | License | Copyright holders |
|---|---|---|---|
| aead | 0.5.2 | MIT OR Apache-2.0 | RustCrypto Developers |
| ahash | 0.8.12 | MIT OR Apache-2.0 | Tom Kaitchuck <Tom.Kaitchuck@gmail.com> |
| allocator-api2 | 0.2.21 | MIT OR Apache-2.0 | Zakarum <zaq.dev@icloud.com> |
| anstream | 1.0.0 | MIT OR Apache-2.0 |  |
| anstyle | 1.0.14 | MIT OR Apache-2.0 |  |
| anstyle-parse | 1.0.0 | MIT OR Apache-2.0 |  |
| anstyle-query | 1.1.5 | MIT OR Apache-2.0 |  |
| anstyle-wincon | 3.0.11 | MIT OR Apache-2.0 |  |
| anyhow | 1.0.102 | MIT OR Apache-2.0 | David Tolnay <dtolnay@gmail.com> |
| arc-swap | 1.9.2 | MIT OR Apache-2.0 | Michal 'vorner' Vaner <vorner@vorner.cz> |
| argon2 | 0.5.3 | MIT OR Apache-2.0 | RustCrypto Developers |
| async-trait | 0.1.92 | MIT OR Apache-2.0 | David Tolnay <dtolnay@gmail.com> |
| atomic-waker | 1.1.2 | Apache-2.0 OR MIT | Stjepan Glavina <stjepang@gmail.com>, Contributors to futures-rs |
| autocfg | 1.5.0 | Apache-2.0 OR MIT | Josh Stone <cuviper@gmail.com> |
| aws-lc-rs | 1.17.3 | ISC AND (Apache-2.0 OR ISC) | AWS-LibCrypto |
| aws-lc-sys | 0.43.0 | ISC AND (Apache-2.0 OR ISC) AND Apache-2.0 AND MIT AND BSD-3-Clause AND (Apache-2.0 OR ISC OR MIT) AND (Apache-2.0 OR ISC OR MIT-0) | AWS-LC |
| axum | 0.7.9 | MIT |  |
| axum-core | 0.4.5 | MIT |  |
| axum-server | 0.7.3 | MIT | Programatik <programatik29@gmail.com>, Adi Salimgereev <adisalimgereev@gmail.com> |
| base64ct | 1.8.3 | Apache-2.0 OR MIT | RustCrypto Developers |
| bincode | 1.3.3 | MIT | Ty Overby <ty@pre-alpha.com>, Francesco Mazzoli <f@mazzo.li>, David Tolnay <dtolnay@gmail.com> |
| bitflags | 1.3.2 | MIT/Apache-2.0 | The Rust Project Developers |
| blake2 | 0.10.6 | MIT OR Apache-2.0 | RustCrypto Developers |
| block-buffer | 0.12.1 | MIT OR Apache-2.0 | RustCrypto Developers |
| byteorder | 1.5.0 | Unlicense OR MIT | Andrew Gallant <jamslam@gmail.com> |
| bytes | 1.11.1 | MIT | Carl Lerche <me@carllerche.com>, Sean McArthur <sean@seanmonstar.com> |
| cc | 1.2.57 | MIT OR Apache-2.0 | Alex Crichton <alex@alexcrichton.com> |
| cfg-if | 1.0.4 | MIT OR Apache-2.0 | Alex Crichton <alex@alexcrichton.com> |
| chacha20 | 0.9.1 | Apache-2.0 OR MIT | RustCrypto Developers |
| chacha20poly1305 | 0.10.1 | Apache-2.0 OR MIT | RustCrypto Developers |
| cipher | 0.4.4 | MIT OR Apache-2.0 | RustCrypto Developers |
| clap | 4.6.0 | MIT OR Apache-2.0 |  |
| clap_builder | 4.6.0 | MIT OR Apache-2.0 |  |
| clap_derive | 4.6.0 | MIT OR Apache-2.0 |  |
| clap_lex | 1.1.0 | MIT OR Apache-2.0 |  |
| clipboard-win | 5.4.1 | BSL-1.0 | Douman <douman@gmx.se> |
| cmake | 0.1.58 | MIT OR Apache-2.0 | Alex Crichton <alex@alexcrichton.com> |
| colorchoice | 1.0.5 | MIT OR Apache-2.0 |  |
| concurrent-queue | 2.5.0 | Apache-2.0 OR MIT | Stjepan Glavina <stjepang@gmail.com>, Taiki Endo <te316e89@gmail.com>, John Nunley <dev@notgull.net> |
| const-oid | 0.10.2 | Apache-2.0 OR MIT | RustCrypto Developers |
| cpufeatures | 0.3.1 | MIT OR Apache-2.0 | RustCrypto Developers |
| crc32fast | 1.5.0 | MIT OR Apache-2.0 | Sam Rijs <srijs@airpost.net>, Alex Crichton <alex@alexcrichton.com> |
| crossbeam-channel | 0.5.15 | MIT OR Apache-2.0 |  |
| crossbeam-deque | 0.8.6 | MIT OR Apache-2.0 |  |
| crossbeam-epoch | 0.9.18 | MIT OR Apache-2.0 |  |
| crossbeam-utils | 0.8.21 | MIT OR Apache-2.0 |  |
| crypto-common | 0.2.2 | MIT OR Apache-2.0 | RustCrypto Developers |
| data-encoding | 2.11.0 | MIT | Julien Cretin <git@ia0.eu> |
| digest | 0.11.3 | MIT OR Apache-2.0 | RustCrypto Developers |
| dunce | 1.0.5 | CC0-1.0 OR MIT-0 OR Apache-2.0 | Kornel <kornel@geekhood.net> |
| either | 1.15.0 | MIT OR Apache-2.0 | bluss |
| endian-type | 0.1.2 | MIT | Lolirofle <lolipopple@hotmail.com> |
| equivalent | 1.0.2 | Apache-2.0 OR MIT |  |
| error-code | 3.3.2 | BSL-1.0 | Douman <douman@gmx.se> |
| fastrand | 2.3.0 | Apache-2.0 OR MIT | Stjepan Glavina <stjepang@gmail.com> |
| fd-lock | 4.0.4 | MIT OR Apache-2.0 | Yoshua Wuyts <yoshuawuyts@gmail.com> |
| find-msvc-tools | 0.1.9 | MIT OR Apache-2.0 |  |
| flume | 0.11.1 | Apache-2.0/MIT | Joshua Barretto <joshua.s.barretto@gmail.com> |
| fnv | 1.0.7 | Apache-2.0 / MIT | Alex Crichton <alex@alexcrichton.com> |
| form_urlencoded | 1.2.2 | MIT OR Apache-2.0 | The rust-url developers |
| fs-err | 3.3.1 | MIT OR Apache-2.0 | Andrew Hickman <andrew.hickman1@sky.com> |
| fs_extra | 1.3.0 | MIT | Denis Kurilenko <webdesus@gmail.com> |
| futures-channel | 0.3.32 | MIT OR Apache-2.0 |  |
| futures-core | 0.3.32 | MIT OR Apache-2.0 |  |
| futures-macro | 0.3.32 | MIT OR Apache-2.0 |  |
| futures-sink | 0.3.32 | MIT OR Apache-2.0 |  |
| futures-task | 0.3.32 | MIT OR Apache-2.0 |  |
| futures-util | 0.3.32 | MIT OR Apache-2.0 |  |
| fxhash | 0.2.1 | Apache-2.0/MIT | cbreeden <github@u.breeden.cc> |
| generic-array | 0.14.7 | MIT | Bartłomiej Kamiński <fizyk20@gmail.com>, Aaron Trent <novacrazy@gmail.com> |
| gethostname | 0.5.0 | Apache-2.0 | Sebastian Wiesner <sebastian@swsnr.de> |
| getrandom | 0.4.2 | MIT OR Apache-2.0 | The Rand Project Developers |
| h2 | 0.4.19 | MIT | Carl Lerche <me@carllerche.com>, Sean McArthur <sean@seanmonstar.com> |
| hashbrown | 0.16.1 | MIT OR Apache-2.0 | Amanieu d'Antras <amanieu@gmail.com> |
| heck | 0.5.0 | MIT OR Apache-2.0 |  |
| home | 0.5.12 | MIT OR Apache-2.0 | Brian Anderson <andersrb@gmail.com> |
| http | 1.4.0 | MIT OR Apache-2.0 | Alex Crichton <alex@alexcrichton.com>, Carl Lerche <me@carllerche.com>, Sean McArthur <sean@seanmonstar.com> |
| http-body | 1.0.1 | MIT | Carl Lerche <me@carllerche.com>, Lucio Franco <luciofranco14@gmail.com>, Sean McArthur <sean@seanmonstar.com> |
| http-body-util | 0.1.3 | MIT | Carl Lerche <me@carllerche.com>, Lucio Franco <luciofranco14@gmail.com>, Sean McArthur <sean@seanmonstar.com> |
| httparse | 1.10.1 | MIT OR Apache-2.0 | Sean McArthur <sean@seanmonstar.com> |
| httpdate | 1.0.3 | MIT OR Apache-2.0 | Pyfisch <pyfisch@posteo.org> |
| hybrid-array | 0.4.10 | MIT OR Apache-2.0 | RustCrypto Developers |
| hyper | 1.8.1 | MIT | Sean McArthur <sean@seanmonstar.com> |
| hyper-util | 0.1.20 | MIT | Sean McArthur <sean@seanmonstar.com> |
| if-addrs | 0.13.4 | MIT OR BSD-3-Clause | MaidSafe Developers <dev@maidsafe.net>, Messense Lv <messense@icloud.com> |
| indexmap | 2.13.0 | Apache-2.0 OR MIT |  |
| inout | 0.1.4 | MIT OR Apache-2.0 | RustCrypto Developers |
| is_terminal_polyfill | 1.70.2 | MIT OR Apache-2.0 |  |
| itoa | 1.0.17 | MIT OR Apache-2.0 | David Tolnay <dtolnay@gmail.com> |
| jobserver | 0.1.34 | MIT OR Apache-2.0 | Alex Crichton <alex@alexcrichton.com> |
| lazy_static | 1.5.0 | MIT OR Apache-2.0 | Marvin Löbel <loebel.marvin@gmail.com> |
| libc | 0.2.183 | MIT OR Apache-2.0 | The Rust Project Developers |
| local-ip-address | 0.5.7 | MIT OR Apache-2.0 | Esteban Borai <estebanborai@gmail.com> |
| lock_api | 0.4.14 | MIT OR Apache-2.0 | Amanieu d'Antras <amanieu@gmail.com> |
| log | 0.4.29 | MIT OR Apache-2.0 | The Rust Project Developers |
| matchers | 0.2.0 | MIT | Eliza Weisman <eliza@buoyant.io> |
| matchit | 0.7.3 | MIT AND BSD-3-Clause | Ibraheem Ahmed <ibraheem@ibraheem.ca> |
| mdns-sd | 0.11.5 | Apache-2.0 OR MIT | keepsimple <keepsimple@gmail.com> |
| memchr | 2.8.0 | Unlicense OR MIT | Andrew Gallant <jamslam@gmail.com>, bluss |
| memmap2 | 0.9.10 | MIT OR Apache-2.0 | Dan Burkert <dan@danburkert.com>, Yevhenii Reizner <razrfalcon@gmail.com>, The Contributors |
| mime | 0.3.17 | MIT OR Apache-2.0 | Sean McArthur <sean@seanmonstar.com> |
| mime_guess | 2.0.5 | MIT | Austin Bonander <austin.bonander@gmail.com> |
| mio | 1.1.1 | MIT | Carl Lerche <me@carllerche.com>, Thomas de Zeeuw <thomasdezeeuw@gmail.com>, Tokio Contributors <team@tokio.rs> |
| nibble_vec | 0.1.0 | MIT | Michael Sproul <micsproul@gmail.com> |
| nu-ansi-term | 0.50.3 | MIT | ogham@bsago.me, Ryan Scheel (Havvy) <ryan.havvy@gmail.com>, Josh Triplett <josh@joshtriplett.org> |
| num-traits | 0.2.19 | MIT OR Apache-2.0 | The Rust Project Developers |
| once_cell | 1.21.4 | MIT OR Apache-2.0 | Aleksey Kladov <aleksey.kladov@gmail.com> |
| once_cell_polyfill | 1.70.2 | MIT OR Apache-2.0 |  |
| opaque-debug | 0.3.1 | MIT OR Apache-2.0 | RustCrypto Developers |
| parking_lot | 0.12.5 | MIT OR Apache-2.0 | Amanieu d'Antras <amanieu@gmail.com> |
| parking_lot_core | 0.9.12 | MIT OR Apache-2.0 | Amanieu d'Antras <amanieu@gmail.com> |
| password-hash | 0.5.0 | MIT OR Apache-2.0 | RustCrypto Developers |
| percent-encoding | 2.3.2 | MIT OR Apache-2.0 | The rust-url developers |
| pin-project-lite | 0.2.17 | Apache-2.0 OR MIT |  |
| pin-utils | 0.1.0 | MIT OR Apache-2.0 | Josef Brandl <mail@josefbrandl.de> |
| pkg-config | 0.3.32 | MIT OR Apache-2.0 | Alex Crichton <alex@alexcrichton.com> |
| polling | 2.8.0 | Apache-2.0 OR MIT | Stjepan Glavina <stjepang@gmail.com> |
| poly1305 | 0.8.0 | Apache-2.0 OR MIT | RustCrypto Developers |
| ppv-lite86 | 0.2.21 | MIT OR Apache-2.0 | The CryptoCorrosion Contributors |
| proc-macro2 | 1.0.106 | MIT OR Apache-2.0 | David Tolnay <dtolnay@gmail.com>, Alex Crichton <alex@alexcrichton.com> |
| quote | 1.0.45 | MIT OR Apache-2.0 | David Tolnay <dtolnay@gmail.com> |
| radix_trie | 0.2.1 | MIT | Michael Sproul <micsproul@gmail.com> |
| rand | 0.9.5 | MIT OR Apache-2.0 | The Rand Project Developers, The Rust Project Developers |
| rand_chacha | 0.9.0 | MIT OR Apache-2.0 | The Rand Project Developers, The Rust Project Developers, The CryptoCorrosion Contributors |
| rand_core | 0.9.5 | MIT OR Apache-2.0 | The Rand Project Developers, The Rust Project Developers |
| rayon | 1.11.0 | MIT OR Apache-2.0 |  |
| rayon-core | 1.13.0 | MIT OR Apache-2.0 |  |
| regex-automata | 0.4.14 | MIT OR Apache-2.0 | The Rust Project Developers, Andrew Gallant <jamslam@gmail.com> |
| regex-syntax | 0.8.10 | MIT OR Apache-2.0 | The Rust Project Developers, Andrew Gallant <jamslam@gmail.com> |
| ring | 0.17.14 | Apache-2.0 AND ISC |  |
| rmp | 0.8.15 | MIT | Evgeny Safronov <division494@gmail.com>, Kornel <kornel@geekhood.net> |
| rmp-serde | 1.3.1 | MIT | Evgeny Safronov <division494@gmail.com> |
| rust-embed | 8.12.0 | MIT | pyrossh |
| rust-embed-impl | 8.12.0 | MIT | pyrossh |
| rust-embed-utils | 8.12.0 | MIT | pyrossh |
| rustls | 0.23.43 | Apache-2.0 OR ISC OR MIT |  |
| rustls-native-certs | 0.8.4 | Apache-2.0 OR ISC OR MIT |  |
| rustls-pemfile | 2.2.0 | Apache-2.0 OR ISC OR MIT |  |
| rustls-pki-types | 1.15.1 | MIT OR Apache-2.0 |  |
| rustls-webpki | 0.103.13 | ISC |  |
| rustversion | 1.0.22 | MIT OR Apache-2.0 | David Tolnay <dtolnay@gmail.com> |
| rustyline | 14.0.0 | MIT | Katsu Kawakami <kkawa1570@gmail.com> |
| ryu | 1.0.23 | Apache-2.0 OR BSL-1.0 | David Tolnay <dtolnay@gmail.com> |
| same-file | 1.0.6 | Unlicense/MIT | Andrew Gallant <jamslam@gmail.com> |
| schannel | 0.1.29 | MIT | Steven Fackler <sfackler@gmail.com>, Steffen Butzer <steffen.butzer@outlook.com> |
| scopeguard | 1.2.0 | MIT OR Apache-2.0 | bluss |
| serde | 1.0.228 | MIT OR Apache-2.0 | Erick Tryzelaar <erick.tryzelaar@gmail.com>, David Tolnay <dtolnay@gmail.com> |
| serde_core | 1.0.228 | MIT OR Apache-2.0 | Erick Tryzelaar <erick.tryzelaar@gmail.com>, David Tolnay <dtolnay@gmail.com> |
| serde_derive | 1.0.228 | MIT OR Apache-2.0 | Erick Tryzelaar <erick.tryzelaar@gmail.com>, David Tolnay <dtolnay@gmail.com> |
| serde_json | 1.0.149 | MIT OR Apache-2.0 | Erick Tryzelaar <erick.tryzelaar@gmail.com>, David Tolnay <dtolnay@gmail.com> |
| serde_path_to_error | 0.1.20 | MIT OR Apache-2.0 | David Tolnay <dtolnay@gmail.com> |
| serde_spanned | 0.6.9 | MIT OR Apache-2.0 |  |
| serde_urlencoded | 0.7.1 | MIT/Apache-2.0 | Anthony Ramine <n.oxyde@gmail.com> |
| sha1 | 0.10.7 | MIT OR Apache-2.0 | RustCrypto Developers |
| sha2 | 0.11.0 | MIT OR Apache-2.0 | RustCrypto Developers |
| sharded-slab | 0.1.7 | MIT | Eliza Weisman <eliza@buoyant.io> |
| shlex | 1.3.0 | MIT OR Apache-2.0 | comex <comexk@gmail.com>, Fenhl <fenhl@fenhl.net>, Adrian Taylor <adetaylor@chromium.org> |
| slab | 0.4.12 | MIT | Carl Lerche <me@carllerche.com> |
| smallvec | 1.15.1 | MIT OR Apache-2.0 | The Servo Project Developers |
| socket2 | 0.6.3 | MIT OR Apache-2.0 | Alex Crichton <alex@alexcrichton.com>, Thomas de Zeeuw <thomasdezeeuw@gmail.com> |
| spin | 0.9.8 | MIT | Mathijs van de Nes <git@mathijs.vd-nes.nl>, John Ericson <git@JohnEricson.me>, Joshua Barretto <joshua.s.barretto@gmail.com> |
| strsim | 0.11.1 | MIT | Danny Guo <danny@dannyguo.com>, maxbachmann <oss@maxbachmann.de> |
| subtle | 2.6.1 | BSD-3-Clause | Isis Lovecruft <isis@patternsinthevoid.net>, Henry de Valence <hdevalence@hdevalence.ca> |
| syn | 3.0.5 | MIT OR Apache-2.0 | David Tolnay <dtolnay@gmail.com> |
| sync_wrapper | 1.0.2 | Apache-2.0 | Actyx AG <developer@actyx.io> |
| tempfile | 3.27.0 | MIT OR Apache-2.0 | Steven Allen <steven@stebalien.com>, The Rust Project Developers, Ashley Mannix <ashleymannix@live.com.au> |
| thiserror | 2.0.18 | MIT OR Apache-2.0 | David Tolnay <dtolnay@gmail.com> |
| thiserror-impl | 2.0.18 | MIT OR Apache-2.0 | David Tolnay <dtolnay@gmail.com> |
| thread_local | 1.1.10 | MIT OR Apache-2.0 | Amanieu d'Antras <amanieu@gmail.com> |
| tokio | 1.50.0 | MIT | Tokio Contributors <team@tokio.rs> |
| tokio-macros | 2.6.1 | MIT | Tokio Contributors <team@tokio.rs> |
| tokio-rustls | 0.26.4 | MIT OR Apache-2.0 |  |
| tokio-tungstenite | 0.26.2 | MIT | Daniel Abramov <dabramov@snapview.de>, Alexey Galakhov <agalakhov@snapview.de> |
| tokio-util | 0.7.18 | MIT | Tokio Contributors <team@tokio.rs> |
| toml | 0.8.23 | MIT OR Apache-2.0 |  |
| toml_datetime | 0.6.11 | MIT OR Apache-2.0 |  |
| toml_edit | 0.22.27 | MIT OR Apache-2.0 |  |
| toml_write | 0.1.2 | MIT OR Apache-2.0 |  |
| tower | 0.5.3 | MIT | Tower Maintainers <team@tower-rs.com> |
| tower-layer | 0.3.3 | MIT | Tower Maintainers <team@tower-rs.com> |
| tower-service | 0.3.3 | MIT | Tower Maintainers <team@tower-rs.com> |
| tracing | 0.1.44 | MIT | Eliza Weisman <eliza@buoyant.io>, Tokio Contributors <team@tokio.rs> |
| tracing-attributes | 0.1.31 | MIT | Tokio Contributors <team@tokio.rs>, Eliza Weisman <eliza@buoyant.io>, David Barsky <dbarsky@amazon.com> |
| tracing-core | 0.1.36 | MIT | Tokio Contributors <team@tokio.rs> |
| tracing-log | 0.2.0 | MIT | Tokio Contributors <team@tokio.rs> |
| tracing-subscriber | 0.3.23 | MIT | Eliza Weisman <eliza@buoyant.io>, David Barsky <me@davidbarsky.com>, Tokio Contributors <team@tokio.rs> |
| tungstenite | 0.26.2 | MIT OR Apache-2.0 | Alexey Galakhov, Daniel Abramov |
| typenum | 1.19.0 | MIT OR Apache-2.0 | Paho Lurie-Gregg <paho@paholg.com>, Andre Bogus <bogusandre@gmail.com> |
| unicase | 2.9.0 | MIT OR Apache-2.0 | Sean McArthur <sean@seanmonstar.com> |
| unicode-ident | 1.0.24 | (MIT OR Apache-2.0) AND Unicode-3.0 | David Tolnay <dtolnay@gmail.com> |
| unicode-segmentation | 1.12.0 | MIT OR Apache-2.0 | kwantam <kwantam@gmail.com>, Manish Goregaokar <manishsmail@gmail.com> |
| unicode-width | 0.1.14 | MIT OR Apache-2.0 | kwantam <kwantam@gmail.com>, Manish Goregaokar <manishsmail@gmail.com> |
| universal-hash | 0.5.1 | MIT OR Apache-2.0 | RustCrypto Developers |
| untrusted | 0.9.0 | ISC | Brian Smith <brian@briansmith.org> |
| utf-8 | 0.7.6 | MIT OR Apache-2.0 | Simon Sapin <simon.sapin@exyr.org> |
| utf8parse | 0.2.2 | Apache-2.0 OR MIT | Joe Wilm <joe@jwilm.com>, Christian Duerr <contact@christianduerr.com> |
| version_check | 0.9.5 | MIT/Apache-2.0 | Sergio Benitez <sb@sergio.bz> |
| walkdir | 2.5.0 | Unlicense/MIT | Andrew Gallant <jamslam@gmail.com> |
| widestring | 1.2.1 | MIT OR Apache-2.0 |  |
| winapi-util | 0.1.11 | Unlicense OR MIT | Andrew Gallant <jamslam@gmail.com> |
| windows-link | 0.2.1 | MIT OR Apache-2.0 |  |
| windows-service | 0.6.0 | MIT OR Apache-2.0 | Mullvad VPN |
| windows-sys | 0.61.2 | MIT OR Apache-2.0 |  |
| windows-targets | 0.52.6 | MIT OR Apache-2.0 | Microsoft |
| windows_x86_64_msvc | 0.52.6 | MIT OR Apache-2.0 | Microsoft |
| winnow | 0.7.15 | MIT |  |
| zerocopy | 0.8.42 | BSD-2-Clause OR Apache-2.0 OR MIT | Joshua Liebow-Feeser <joshlf@google.com>, Jack Wrenn <jswrenn@amazon.com> |
| zeroize | 1.8.2 | Apache-2.0 OR MIT | The RustCrypto Project Developers |
| zmij | 1.0.21 | MIT | David Tolnay <dtolnay@gmail.com> |
| zstd | 0.13.3 | MIT | Alexandre Bury <alexandre.bury@gmail.com> |
| zstd-safe | 7.2.4 | MIT OR Apache-2.0 | Alexandre Bury <alexandre.bury@gmail.com> |
| zstd-sys | 2.0.16+zstd.1.5.7 | MIT/Apache-2.0 | Alexandre Bury <alexandre.bury@gmail.com> |
