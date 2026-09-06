# android/

Android binaries are distributed as the `libfirelite-<version>-android.tar.gz`
**Release asset**, not stored in git. The archive follows the Android Studio
`jniLibs` layout — extract it into your app module:

```text
app/src/main/jniLibs/arm64-v8a/libfirelite.so
```

Current coverage: **arm64-v8a only** (`aarch64-linux-android`). armeabi-v7a and
x86_64 emulator builds are not published yet.

Notes:

- Pure C ABI, no Java glue required — bind `include/firelite.h` through your
  own JNI layer. No `JNI_OnLoad` is exported.
- Net Sync discovery defaults to **UDP subnet broadcast** on Android: the WiFi
  stack filters inbound multicast without a `MulticastLock`, so mDNS browsing
  hears nothing, while broadcast (`255.255.255.255:5354`) passes with only the
  `INTERNET` permission. Override per syncer if the app holds a lock:
  `fl_net_syncer_set_discovery(s, 2)` for both transports.
- App-side requirements: `INTERNET` permission, same WiFi as the group.
  Doze stalls the mesh with the screen off; use a foreground service for
  always-on sync. Beacons expire after 45s (broadcast has no leave event).
- Mixed groups (Android + desktop): the desktop side must opt into `Both`
  (`serve --discovery both` or the SDK setter) — a default desktop never hears
  broadcast-only mobile peers.
