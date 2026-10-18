# Flameshot legacy builds

![Sync upstream release](https://github.com/alkaid-ops/flameshot-legacy-builds/actions/workflows/sync-release.yml/badge.svg)

Unofficial rebuilds of [Flameshot](https://github.com/flameshot-org/flameshot) for legacy operating systems.

Current target: **macOS 11 (Big Sur) and later, arm64 / Apple Silicon**.

## Why this repository exists

Official Flameshot macOS arm64 packages currently require macOS 14-15 because they are built with current Homebrew Qt (6.11) and a newer deployment target.

These builds use:

- Qt 6.7.3, the last Qt release supporting macOS 11
- `CMAKE_OSX_ARCHITECTURES=arm64`
- `CMAKE_OSX_DEPLOYMENT_TARGET=11.0`

## Build sources

### 1. Upstream release sync

The workflow checks the latest stable release from `flameshot-org/flameshot` every 6 hours, rebuilds it, and publishes it here if a matching release does not already exist.

### 2. Fix sets

When an upstream release needs a small, important fix, a fix definition is added under [`fixes/`](fixes/):

```yaml
base: v15.0.rc1
cherry_picks:
  - <upstream-commit-sha>
```

Rules for fix sets:

- Base is always an official upstream tag, never a master snapshot.
- Only merged upstream commits are cherry-picked.
- Scope is minimal: legacy OS compatibility, security, or critical UX fixes.
- The release tag is the fix-set name, for example `v15.0.rc1-fix.1`.
- Once upstream includes the fixes in a stable release, the fix set is retired.

The current example is [`fixes/v15.0.rc1-fix.1.yml`](fixes/v15.0.rc1-fix.1.yml), which backports upstream `#4986` so the macOS app bundle contains the translation files.

Additional patches, if ever needed, live in [`patches/`](patches/). They are applied only when they still apply cleanly.

## Scope

| Item | Supported |
|---|---|
| macOS | 11 (Big Sur) and later |
| CPU | arm64 / Apple Silicon only |
| Intel Macs | Not supported yet |
| Other legacy platforms | Not yet; add a target when there is demand |

## Downloads

See the [Releases](https://github.com/alkaid-ops/flameshot-legacy-builds/releases) page.

Artifact names:

```text
Flameshot-<version>-macos11-arm64.dmg
Flameshot-<version>-<fix-set>-macos11-arm64.dmg
```

Each DMG is accompanied by a `.sha256` file.

The app is ad-hoc signed, not notarized. On first launch, right-click the app and choose **Open**, or clear the quarantine attribute:

```bash
sudo xattr -rd com.apple.quarantine /Applications/Flameshot.app
```

## macOS Screen Recording permission

These builds are ad-hoc signed, so the macOS Screen Recording permission is tied to the exact binary that was authorized. Every rebuilt or updated app has a different signature, and macOS can then show the old entry as enabled while the app only captures a black screen or the desktop wallpaper.

If screenshots only show the wallpaper or a black screen, use this manual recovery. Toggling the switch alone is often not enough:

1. Quit Flameshot completely (tray menu, or `pkill -x flameshot`).
2. Open **System Settings -> Privacy & Security -> Screen Recording**.
3. Select Flameshot and remove it with the minus button. Do not just toggle it off and on.
4. Launch Flameshot again.
5. When macOS asks for Screen Recording permission, open System Settings and enable Flameshot. An administrator password may be required.
6. Quit Flameshot completely and launch it again. The permission takes effect only after a restart.
7. Test again, for example with `flameshot full -p /tmp/test.png`.

`tccutil reset ScreenCapture org.flameshot.Flameshot` can clear the entry automatically, but it does not always replace the manual remove-and-re-add step. If the switch still looks enabled after a reset, remove the entry manually.

This is a limitation of ad-hoc signing, not a Flameshot bug. Official Flameshot builds have the same issue until they are signed with a stable Developer ID and notarized.

## How synchronization works

1. Scheduled run every 6 hours, or manual `workflow_dispatch`.
2. Resolve either the latest upstream stable release or a fix set from `fixes/`.
3. Skip the build if the release tag already exists here.
4. Check out the upstream base tag, apply cherry-picks and patches.
5. Build with Qt 6.7.3 and deployment target 11.0.
6. Verify arm64 and `minos 11.0`, create a DMG, and publish a release.

Manual examples:

```bash
gh workflow run sync-release.yml -f tag=v14.0.0
gh workflow run sync-release.yml -f fix=v15.0.rc1-fix.1
```

## Issues

Issues are welcome for problems specific to these rebuilt packages, such as the DMG not opening or the app not launching on macOS 11.

For bugs that affect Flameshot itself, use the [upstream issue tracker](https://github.com/flameshot-org/flameshot/issues).

## 中文简介

本仓库用于给老系统重新打包 Flameshot。目前只提供 macOS 11 及以上、Apple Silicon (arm64) 的版本。CI 每 6 小时检查一次上游 release，自动构建并发布；需要修复时，用官方 tag + 最小 cherry-pick 的 fix set，例如 `v15.0.rc1-fix.1`。

如果截图只有壁纸或黑屏，请到 `系统设置 -> 隐私与安全性 -> 屏幕录制`，先把 Flameshot 用减号删掉，再重新打开 Flameshot 授权；授权后必须完全退出并重新打开 Flameshot。只切换开关通常无效。

## License and disclaimer

- Flameshot is licensed under GPL-3.0. See [`LICENSE`](LICENSE).
- These builds are unofficial and are not affiliated with the Flameshot project.
- Corresponding source: the upstream base tag listed in each release, plus the listed cherry-picks and patches in this repository.
