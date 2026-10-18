# Repository boundary

The confidence layer and its repair target are separate Git repositories. The existing GitHub repository, [kAWEOME/nebius-nvidia-code](https://github.com/kAWEOME/nebius-nvidia-code), remains the Express fork and owner of [issue #1](https://github.com/kAWEOME/nebius-nvidia-code/issues/1). The new confidence-layer repository owns the project documents and future orchestration code.

## Why a submodule

`fixtures/issue-repo` is a submodule pinned to a specific fork commit. This keeps the target available within the confidence-layer checkout without copying Express into its history. A run can bind its evidence to the recorded base revision, and changing the fixture revision produces a reviewable parent-repository change.

Git issues are hosted by GitHub, not stored in a clone or submodule. Read and update issues against the fork explicitly, for example `gh issue view 1 --repo kAWEOME/nebius-nvidia-code`; GitHub CLI can otherwise select the upstream Express repository.

A clone-on-demand fixture would also preserve this boundary, but would require bootstrap logic and an explicit revision manifest. A subtree or vendored copy would duplicate the target code in the confidence-layer history. The submodule is sufficient for the current structure.

## Documents and history

Both `confidence-layer-flow.md` and the SAP challenge PDF live in this repository's `docs/` directory. Their contents were copied without modification. They were removed from the fork's current tree, while older fork commits retain them. No existing Git history was rewritten.

The pre-split checkpoint in the fork is `dce54e54f50eeb410866adfcaba3d3d1592c4020` (`docs: capture confidence-layer flow before repository split`).

## Updating the fixture baseline

From the confidence-layer root, first ensure there are no uncommitted fixture changes, then select an explicitly reviewed commit:

```sh
git -C fixtures/issue-repo fetch origin
git -C fixtures/issue-repo checkout --detach <reviewed-commit-sha>
git add fixtures/issue-repo
git commit -m "chore: update issue fixture baseline"
```

Commit and push target-repository changes to the fork before publishing a parent commit that references them. Avoid automatically following the fork's branch tip: each run needs a fixed base SHA.

## GitHub repositories

- Confidence layer: [0xRezaa/confidence-layer](https://github.com/0xRezaa/confidence-layer), private, default branch `main`.
- Issue fixture: [kAWEOME/nebius-nvidia-code](https://github.com/kAWEOME/nebius-nvidia-code), public, default branch `master`.

The fork's checkpoint and documentation-removal commits have been published, so the pinned fixture revision is available from GitHub. The documents remain accessible in older public fork commits; moving them into the private confidence-layer repository does not remove that history.

For future fixture updates, push the target commit to the issue repository before publishing the parent commit that references it. Verify published changes with a fresh recursive clone using the instructions in the root README.
