# Issue repository fixture

`issue-repo/` is a Git submodule of [kAWEOME/nebius-nvidia-code](https://github.com/kAWEOME/nebius-nvidia-code), an Express fork. It remains a separate repository with its own issues and commit history.

- Repair issue: [#1 — res.send(ArrayBuffer) silently sends {} as JSON](https://github.com/kAWEOME/nebius-nvidia-code/issues/1).
- Original report: [expressjs/express#7362](https://github.com/expressjs/express/issues/7362).
- Base revision: the gitlink committed at `fixtures/issue-repo`; inspect it with `git rev-parse HEAD:fixtures/issue-repo` from the confidence-layer root.

The issue links identify the intended scenario, not proof of reproduction. Qualification still needs to generate and execute a reproducer as described in the flow document. No bug fix is included in the repository split.

## Run the existing Express suite

Use Node.js 18 or newer, as required by the fixture's package manifest:

```sh
git submodule update --init --recursive
cd fixtures/issue-repo
npm install
npm test
```

The upstream fixture has no committed dependency lockfile. Its Git revision pins source code, but `npm install` alone does not guarantee identical dependency resolution across runs. Dependency and execution-environment reproducibility remain implementation decisions.

## Candidate changes

Treat the pinned submodule as the baseline. Run candidate edits in a separate disposable clone, and record the base SHA and candidate diff with verification evidence. Keep confidence-layer policy, orchestration, and evidence outside the repair target.

Updating the baseline is an explicit change to the parent repository's submodule pointer; see [repository guidance](../docs/repository-layout.md).
