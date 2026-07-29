# Agent Instructions

Read `README.md`, `docs/architecture.md`, and
`docs/integration-contracts.md` before changing any mod.

## Source locations

Working-copy locations are environment-specific. Discover existing working
copies from the active workspace and use `repositories.json` to map repository
names to project folders. Do not create a second working copy unless the user
explicitly changes this policy.

Never write machine-specific absolute paths, user-profile directories, or
account names into tracked files.

Use English only in tracked files of public repositories, including
documentation, source comments, UI text, commit messages, and release notes,
unless the user explicitly requests a localized artifact.

## Engineering rules

- Inspect Jump King and existing mod code before choosing an insertion point.
- Do not add speculative fallbacks, compatibility branches, or guards.
- Keep HTTP transport, UI, player routing, and feature behavior separate.
- Resolve optional cross-mod APIs once at startup and cache delegates.
- Never scan assemblies or use reflection in per-frame paths.
- Metrics and canonical game progress observe Player 1 unless their product
  requirements explicitly define multiplayer semantics.
- Before adding Harmony, state the exact target method and whether the patch is
  Prefix, Postfix, or replacement. Record it in `jump-king-lab`.
- Check Player Behaviour Modifier detection for every gameplay-affecting mod.
- Preserve user changes and unrelated dirty files.
- Build both Debug and Release before a release.

## Release rules

- Semantic versioning: patch for fixes, minor for compatible features, major
  for breaking contracts.
- Keep `AssemblyVersion` and `AssemblyFileVersion` equal to the release version.
- Work on `release/{version}`.
- Merge through a GitHub pull request.
- Tag the merge commit as `v{version}`.
- Create a GitHub release with the Release DLL and required runtime files.
