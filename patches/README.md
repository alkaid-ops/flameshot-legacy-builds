# Patches

The build workflow applies every `*.patch` file in this directory to the upstream source before building.

Current patches:

- `0001-bundle-macos-translations.patch` backports upstream `#4986`, which bundles `Internationalization_*.qm` files inside `Flameshot.app` and makes `PathInfo::translationsPaths()` look in `Contents/Resources/translations`. This is needed for tags created before that fix, such as `v14.0.0`.

macOS 11 support itself is achieved only by pinning Qt 6.7.3 and setting the deployment target and architecture.

If a patch becomes necessary:

1. Create it against the upstream tag, for example with `git format-patch`.
2. Name it with a numeric prefix, for example `0001-fix-macos11.patch`.
3. Document it in the release notes or README.
