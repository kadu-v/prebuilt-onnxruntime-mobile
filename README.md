# prebuilt-onnxruntime-mobile

Build scripts for ONNX Runtime binaries for mobile and desktop (edge-vision-toolkit `sdk/third_party/onnxruntime`).

- ONNX Runtime: **v1.28.0** (submodule `kadu-v/onnxruntime` branch `v1.28.0-fix`)
  - upstream v1.28.0 + LoggingManager mutex fix + eigen `URL_HASH` disabled
- ORT API 28 (for the Rust `ort` crate)

## Build

```sh
uv run cargo make all             # runs ios → ios-sim → ios-sim-x86_64 → macosx → android → package in order

# or one at a time
uv run cargo make android         # android/arm64-v8a/lib/libonnxruntime.so
uv run cargo make ios             # apple/ios64/lib/libonnxruntime.a
uv run cargo make ios-sim         # apple/simulator-arm64/lib/libonnxruntime.a
uv run cargo make ios-sim-x86_64  # apple/simulator-x86_64/lib/libonnxruntime.a
uv run cargo make macosx          # apple/macosx/lib/libonnxruntime.a
uv run cargo make package         # checks all 5 files (existence + arch) → build/${ORT_VERSION}/onnxruntime-${ORT_VERSION}.zip
```

The output goes to `build/${ORT_VERSION}/onnxruntime/`, which you can copy straight to `sdk/third_party/onnxruntime`.
Every task deletes `onnxruntime/build`, so run the tasks one at a time.

| Target | Main flags |
| --- | --- |
| Android | `--android_abi arm64-v8a --android_api 27 --use_xnnpack --build_shared_lib` (NDK: `android.env`) |
| iOS / Simulator | `--ios --apple_deploy_target 16.4 --build_apple_framework --use_coreml --no_kleidiai` |
| macOS | `--macos MacOSX --apple_deploy_target 15.0 --build_apple_framework --use_coreml --no_kleidiai --use_vcpkg` |

Apple targets are built without XNNPACK: the static libraries go into the same binary as TFLite, whose own XNNPACK would clash with it.
