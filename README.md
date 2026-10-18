# Flameshot macOS 11+ (Big Sur) builds

![Sync upstream release](https://github.com/alkaid-ops/flameshot-macos11/actions/workflows/sync-release.yml/badge.svg)

Unofficial Linux-style community rebuilds of [Flameshot](https://github.com/flameshot-org/flameshot) for **macOS 11 (Big Sur) and later, arm64 / Apple Silicon**.

## Why this repository exists

Official Flameshot macOS arm64 packages currently require macOS 14-15 because they are built with current Homebrew Qt (6.11) and a newer deployment target.

These builds use:

- Qt 6.7.3, the last Qt release supporting macOS 11
- `CMAKE_OSX_ARCHITECTURES=arm64`
- `CMAKE_OSX_DEPLOYMENT_TARGET=11.0`

No Flameshot source changes are currently required. Patches, if ever needed, live in [`patches/`](patches/).

## Scope

| Item | Supported |
|---|---|
| macOS | 11 (Big Sur) and later |
| CPU | arm64 / Apple Silicon only |
| Intel Macs | Not supported yet |
| Flameshot versions | Upstream release tags, rebuilt automatically |

## Downloads

See the [Releases](https://github.com/alkaid-ops/flameshot-macos11/releases) page.

Artifacts are named like:

```text
Flameshot-<version>-macos11-arm64.dmg
Flameshot-<version>-macos11-arm64.dmg.sha256
```

The app is ad-hoc signed, not notarized. On first launch, right-click the app and choose **Open**, or clear the quarantine attribute:

```bash
sudo xattr -rd com.apple.quarantine /Applications/Flameshot.app
```

## How synchronization works

The GitHub Actions workflow:

1. Runs on a schedule every 6 hours and can also be triggered manually.
2. Reads the latest stable release tag from `flameshot-org/flameshot`.
3. Skips the build if a `macos11-<upstream-tag>` release already exists here.
4. Checks out the upstream tag, applies any patches in `patches/`.
5. Builds with Qt 6.7.3 and deployment target 11.0.
6. Verifies the binary architecture and `minos`, creates a DMG, and publishes it to this repository's Releases.

Manual run with a specific upstream tag:

```bash
gh workflow run sync-release.yml -f tag=v14.0.0
```

## Issues

Issues are welcome in this repository for problems specific to these rebuilt packages, such as:

- The DMG does not open or install
- The app does not launch on macOS 11
- Crash or behavior difference compared with upstream packages

Please report bugs that affect Flameshot itself to the [upstream issue tracker](https://github.com/flameshot-org/flameshot/issues).

## 中文简介

本仓库用 Qt 6.7.3 重新打包 Flameshot 官方 release，目标是最低支持 macOS 11 (Big Sur)，目前只提供 Apple Silicon (arm64) 版本。CI 每 6 小时检查一次上游新 release，自动构建并发布到本仓库的 Releases。Intel Mac 暂不支持，有需要再跟进。

## License and disclaimer

- Flameshot is licensed under GPL-3.0. See [`LICENSE`](LICENSE).
- These builds are unofficial and are not affiliated with the Flameshot project.
- Corresponding source: the upstream tag listed in each release, plus any patches in this repository.
