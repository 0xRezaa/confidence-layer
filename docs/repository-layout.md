# Repository boundary

The confidence layer and its repair target are independent repositories connected through GitHub links.

| Purpose | Repository | Visibility | Default branch |
| --- | --- | --- | --- |
| Confidence-layer documents and future orchestration code | [0xRezaa/confidence-layer](https://github.com/0xRezaa/confidence-layer) | Private | `main` |
| Express source, bug reports, and candidate fixes | [kAWEOME/nebius-nvidia-code](https://github.com/kAWEOME/nebius-nvidia-code) | Public | `master` |

The confidence-layer checkout contains no copy or submodule of the issue repository. Each repository can be cloned, changed, and published independently.

## GitHub integration

The initial repair scenario is [issue #1 — res.send(ArrayBuffer) silently sends {} as JSON](https://github.com/kAWEOME/nebius-nvidia-code/issues/1), based on [expressjs/express#7362](https://github.com/expressjs/express/issues/7362).

Use the fork's explicit repository identity when accessing the issue. GitHub CLI can otherwise select upstream Express:

```sh
gh issue view 1 --repo kAWEOME/nebius-nvidia-code
```

A future confidence-layer run should accept the GitHub issue URL, identify its repository, and record an exact base commit SHA before qualification begins. Bind the reproducer, candidate diff, verification evidence, and deployment artifact to that revision as described in the flow document. A mutable branch name or issue URL alone does not identify the source revision that was verified.

If execution requires source code locally, obtain that revision in a disposable run workspace. Keep confidence-layer policy, orchestration, and evidence outside the repair target. No target revision is pinned by this repository's Git tree.

The issue link identifies the intended scenario, not proof of reproduction. Qualification still needs to generate and execute a reproducer. The integration and execution workflow remain to be implemented.

## Working on the issue repository

Use its independent checkout to change Express code or run its existing test suite. Its package manifest currently requires Node.js 18 or newer and provides `npm test` after installing dependencies with `npm install`.

The issue repository has no committed dependency lockfile. Pinning its source commit alone does not guarantee identical dependency resolution across runs. Dependency and execution-environment reproducibility remain implementation decisions.

## Documents and history

Both `confidence-layer-flow.md` and the SAP challenge PDF belong in this repository's `docs/` directory. They were moved without changing their contents, and removed from the issue repository's current tree.

The pre-split checkpoint in the issue repository is `dce54e54f50eeb410866adfcaba3d3d1592c4020` (`docs: capture confidence-layer flow before repository split`). Those documents remain accessible in older public issue-repository commits. No existing Git history was rewritten.
