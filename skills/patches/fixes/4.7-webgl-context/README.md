# Godot 4.7 WebGL Context Correction

This incremental patch targets the adapted `citizenll/godot` 4.7 engine at
`a71c910992`, used by template `minigame4.7.0.7.tpz`. It is included in
`minigame4.7.0.8.tpz`. Do not add it to the version-locked 4.6 bundle or
apply it to unmodified upstream Godot.

Engine fix: [df3cdd9bc9](https://github.com/citizenll/godot/commit/df3cdd9bc9).

GLX builds disable `OFFSCREEN_FRAMEBUFFER`. Requesting explicit swaps when
such a build starts in standard WebGL mode makes Emscripten reject context
creation, even when WebGL 2 is supported. Keep implicit swaps for both runtime
modes in GLX builds; preserve explicit swaps for standard-only builds.
The existing WeChat frame wrapper must still flush and commit after
Emscripten returns `INVALID_TARGET` on the implicit standard path.

From the adapted engine root, replace `<patch>` with this directory's
`001-context-swap-control.patch` path:

```sh
git apply --check <patch>
git apply <patch>
node platform/web/js/tests/test_wechat_webgl_context_attributes.js
node platform/web/js/tests/test_godot_process_commit_frame.js
node platform/web/js/tests/test_wechat_glx_runtime.js
```

Rebuild and post-process using the [GLX adaptation guide](../../../references/wxglx-adaptation.md).
The context test covers GLX builds in both runtime modes and standard-only
builds. The frame test covers presentation even when Emscripten returns an
error code. Validate startup and scene rendering on target devices; the
4.7.0.8 template was also verified on macOS by the reporter.
