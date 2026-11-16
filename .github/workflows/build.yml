name: Build APK
on:
  push:
  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: 17

      - name: Rust + cargo-ndk
        run: |
          rustup target add aarch64-linux-android armv7-linux-androideabi
          cargo install cargo-ndk --locked

      - name: Build tun2proxy native library
        run: |
          git clone https://github.com/tun2proxy/tun2proxy.git /tmp/tun2proxy
          cd /tmp/tun2proxy
          git checkout v0.8.0 || git checkout 0.8.0
          cargo ndk -t arm64-v8a -t armeabi-v7a -o "$GITHUB_WORKSPACE/app/src/main/jniLibs" build --release --lib
          ls -R "$GITHUB_WORKSPACE/app/src/main/jniLibs"

      - uses: gradle/actions/setup-gradle@v4
        with:
          gradle-version: 8.9

      - name: Build APK
        run: gradle assembleDebug --no-daemon

      - uses: actions/upload-artifact@v4
        with:
          name: SuperProxy-apk
          path: app/build/outputs/apk/debug/app-debug.apk
