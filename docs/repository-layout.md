# Repository boundary

The confidence layer and its repair target are independent repositories connected through GitHub links.

| Purpose | Repository | Visibility | Default branch |
| --- | --- | --- | --- |
| Confidence-layer documents and future orchestration code | [0xRezaa/confidence-layer](https://github.com/0xRezaa/confidence-layer) | Private | `main` |
| Repair target, bug reports, and candidate fixes; currently an Express fork | [kAWEOME/nebius-nvidia-code](https://github.com/kAWEOME/nebius-nvidia-code) | Public | `master` |

The confidence-layer checkout contains no copy or submodule of the issue repository. Each repository can be cloned, changed, and published independently.

The target repository may be repurposed in any way needed for the demo. Express is not a product requirement. At least one GitHub issue must be available for the confidence layer to process; the brief separately requires a demonstration with a handful of seeded trivial bugs and a difficult refusal. See the [agreed scope and flow](confidence-layer-flow.md).

## GitHub integration

An existing repair fixture is [issue #1 — res.send(ArrayBuffer) silently sends {} as JSON](https://github.com/kAWEOME/nebius-nvidia-code/issues/1), based on [expressjs/express#7362](https://github.com/expressjs/express/issues/7362). It can be replaced when repurposing the target repository.

Use the fork's explicit repository identity when accessing the issue. GitHub CLI can otherwise select upstream Express:

```sh
gh issue view 1 --repo kAWEOME/nebius-nvidia-code
```

A future confidence-layer run should accept the GitHub issue URL, identify its repository, and record an exact base commit SHA before qualification begins. Bind the reproducer, candidate diff, verification evidence, and deployment artifact to that revision as described in the flow document. A mutable branch name or issue URL alone does not identify the source revision that was verified.

If execution requires source code locally, obtain that revision in a disposable run workspace. Keep confidence-layer policy, orchestration, and evidence outside the repair target. No target revision is pinned by this repository's Git tree.

The issue link identifies an available scenario, not proof of reproduction. Qualification still needs to generate and execute a reproducer. The integration and execution workflow remain to be implemented. Execution and evidence storage are local, with `gh` handling GitHub access and issue-label updates. The product interface is CLI and GitHub, with issue labels representing pipeline progress; no dashboard is planned. Label names and triggers remain open.

## Working on the issue repository

Use its independent checkout to change target code or run its existing test suite. Repository prerequisites should be minimal, primarily using existing test coverage. For the current Express fixture, its package manifest requires Node.js 18 or newer and provides `npm test` after installing dependencies with `npm install`; these commands may change if the repository is repurposed.

A run accepts an issue URL, optional install command, and existing regression-test command through the CLI. No new target-repository configuration file or automatic command discovery is required. The existing suite must pass at the base revision; after repair, the preserved generated reproducer and full suite must pass. Record the supplied commands and prevent the fixer from modifying them. The reproducer is a separate test-only patch with its fixed execution command and failure evidence.

The issue repository has no committed dependency lockfile. Pinning its source commit alone does not guarantee identical dependency resolution across runs. Dependency and execution-environment reproducibility remain implementation decisions.

## Documents and history

Both `confidence-layer-flow.md` and the SAP challenge PDF belong in this repository's `docs/` directory. They were moved without changing their contents, and removed from the issue repository's current tree.

The pre-split checkpoint in the issue repository is `dce54e54f50eeb410866adfcaba3d3d1592c4020` (`docs: capture confidence-layer flow before repository split`). Those documents remain accessible in older public issue-repository commits. No existing Git history was rewritten.
