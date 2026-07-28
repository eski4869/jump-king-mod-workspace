# Development And Release Workflow

1. Read the target repository and relevant Jump King code.
2. Identify lifecycle hooks, component ownership, and Harmony patches.
3. Confirm filesystem, forced-exit, custom-map, and major-mod behavior.
4. Make the smallest responsibility-aligned change.
5. Run focused automated tests.
6. Build Debug and Release.
7. Deploy Debug DLLs to `Content/JKMods` for human verification.
8. Create or update `release/{version}`.
9. Push the release branch and open a pull request.
10. Merge the pull request into the repository's default branch.
11. Tag the merge commit as `v{version}`.
12. Publish the GitHub release and attach the Release artifact.

Assembly and file versions must match the release tag without the leading `v`.

Do not publish directly from an unmerged feature branch. Do not overwrite a
released tag with unrelated history.
