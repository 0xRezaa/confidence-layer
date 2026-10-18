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

## Publishing the initial split

The initial split is prepared locally. Publish the existing fork's checkpoint and documentation-removal commits first:

```sh
git -C /Users/rezabanazadeh/Projects/nebius-nvidia-code push origin master
```

Then create or select a separate GitHub repository for the confidence layer, add it as this repository's `origin`, and push `main`. Do not reuse the Express fork's remote as the confidence-layer origin.

Until the fork's cleanup commit has been pushed, a fresh submodule checkout from GitHub cannot fetch the locally pinned revision. The prepared local fixture already contains that commit. After publishing, verify a fresh recursive clone of the confidence-layer repository.
