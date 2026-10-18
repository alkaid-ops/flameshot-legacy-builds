# Patches

The build workflow applies every `*.patch` file in this directory to the upstream source before building.

Currently no patches are required: macOS 11 support is achieved only by pinning Qt 6.7.3 and setting the deployment target and architecture.

If a patch becomes necessary:

1. Create it against the upstream tag, for example with `git format-patch`.
2. Name it with a numeric prefix, for example `0001-fix-macos11.patch`.
3. Document it in the release notes or README.
