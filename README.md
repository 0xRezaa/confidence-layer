# Confidence layer

Confidence-layer orchestration and verification for automated bug fixes. The agreed pipeline and open design decisions are in [the flow document](docs/confidence-layer-flow.md); the [SAP challenge brief](<docs/SAP - AI Hackathon with Nebius & NVIDIA.pdf>) provides the project context.

This repository currently contains planning documents and a pinned repair fixture. The confidence-layer implementation has not been built yet; its language and tooling remain open.

## Layout

```text
confidence-layer/
├── docs/                  # Flow, challenge brief, and repository guidance
└── fixtures/
    ├── README.md          # Fixture identity and usage
    └── issue-repo/        # Git submodule: the existing Express fork
```

The Express fork keeps its own Git history and [GitHub issue #1](https://github.com/kAWEOME/nebius-nvidia-code/issues/1). Confidence-layer code and documentation belong here. Express code, bug reports, and candidate fixes belong to the issue repository.

## Checkout

The repository is private; authenticate with a GitHub account that has access. To clone it with the fixture:

```sh
git clone --recurse-submodules git@github.com:0xRezaa/confidence-layer.git
```

For an existing clone, run:

```sh
git submodule update --init --recursive
```

The submodule uses the fork's HTTPS URL and checks out the exact commit recorded by this repository. See [fixture instructions](fixtures/README.md) for running its tests and [repository guidance](docs/repository-layout.md) for changing the pinned revision and publishing the split.

There is no root package manifest or confidence-layer test command yet. The Express package and test suite live inside the fixture.
