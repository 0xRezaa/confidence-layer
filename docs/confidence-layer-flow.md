# Confidence layer: flow for review

Status: shared understanding for review, before grill-me and Wayfinder. This captures the agreed pipeline and the suggested failure paths; it does not select implementation tools or authorize implementation.

Source: [SAP challenge brief](<SAP - AI Hackathon with Nebius & NVIDIA.pdf>), especially pages 1–2, and the team's diagram discussed in this chat.

```mermaid
flowchart TD
    subgraph Pipeline[Confidence-layer pipeline]
        Issue["Bug report + repository + base commit SHA"]
        Pre["Pre-checks: qualify the bug<br/>Automatically generate and execute a reproducer<br/>Confirm failure matches the reported bug"]

        subgraph External[External dependency — outside implementation scope]
            Fixer["Black-box fixer<br/>Qualified bug + base revision + failing test + failure evidence<br/>Returns a candidate diff"]
        end

        Diff["Diff inspection<br/>Small and localized; no protected paths touched<br/>No added secrets; dependencies unchanged"]
        Oracle["Behavioral oracle<br/>Same reproducer: red to green<br/>Full regression suite remains green<br/>Tests and test configuration are not weakened"]
        Gate["Deployment gate<br/>Both post-check categories clearly pass<br/>Evidence identifies the exact candidate and deployable artifact"]
        Canary["Deploy behind a feature flag and canary<br/>Observe behavior and SLOs"]
        Promote["Promote automatically<br/>No routine human approval"]

        Reject["Reject candidate"]
        Escalate["Escalate bug to a human<br/>Record reason and available evidence"]
        Rollback["Automatic rollback<br/>Disable candidate traffic; restore previous release"]
        Restored{"Restoration verified?"}
        Pause["Pause the line<br/>Stop new fixes and releases<br/>Keep monitoring and recovery available"]

        Issue --> Pre
        Pre -->|Qualified; reproducer demonstrably fails| Fixer
        Pre -->|Fail or unknown; never invoke fixer| Escalate
        Fixer -->|Valid candidate bound to base revision| Diff
        Fixer -->|Error, timeout, missing or invalid output| Escalate
        Diff -->|Pass| Oracle
        Diff -->|Fail or unknown| Reject
        Oracle -->|Pass| Gate
        Oracle -->|Fail or unknown| Reject
        Reject --> Escalate
        Gate -->|All required evidence valid and complete| Canary
        Gate -->|Fail or unknown; do not deploy| Escalate
        Canary -->|Observation criteria clearly pass| Promote
        Canary -->|SLO breach, failed rollout, or missing observations| Rollback
        Rollback --> Restored
        Restored -->|Yes| Escalate
        Restored -->|No or unknown| Pause
        Pause -->|Report unresolved state| Escalate
    end

    Audit["Cross-cutting audit<br/>Every transition and outcome<br/>Bug, qualification, diff, verifying evidence, deployment result<br/>Immutable and signed; retrievable and replayable"]
    Human["Human governance<br/>On the loop; no real-time merge or deploy approval"]

    Pipeline -.->|Record inputs, decisions, evidence, and outcomes| Audit
    Human -.->|Inspect and replay decisions| Audit
    Human -.->|Pause at any time| Pause
    Human -.->|Request rollback at any time| Rollback

    classDef gate fill:#d9f2d9,stroke:#39733d,color:#172a18;
    classDef external fill:#f9d4d4,stroke:#9c4545,color:#351919;
    classDef action fill:#d7eaff,stroke:#35638c,color:#15283a;
    classDef stop fill:#fff0cc,stroke:#947020,color:#35290d;
    class Pre,Diff,Oracle,Gate gate;
    class Fixer external;
    class Issue,Canary,Promote,Audit action;
    class Reject,Escalate,Rollback,Restored,Pause stop;
```

## How to read this flow

- **Fail closed:** a failed, missing, inconclusive, or timed-out check never counts as a pass. Before deployment, reject or escalate without shipping. After possible traffic exposure, recover the previous release and verify the result.
- **Qualification is provisional:** pre-checks establish an eligible bug and a genuine reproducible failure. The actual patch's size and locality can only be enforced after the fixer returns it. An unrelated test failure or broken environment does not qualify a bug.
- **Both post-check categories are mandatory:** diff inspection and behavioral verification cannot compensate for each other. Their sequential arrangement here is illustrative, not a scheduling requirement.
- **Evidence stays bound to its subject:** distinguish the base commit, candidate diff/revision, reproducer, test execution, and deployed artifact. The deployed artifact must be the one verification authorized. The fixer cannot weaken the tests, test command, or gate policy to manufacture success.
- **The fixer stays external:** neither its internals nor its runtime hosting/containment belongs to this implementation. Executing verification tests is part of the confidence layer.
- **Audit covers failures too:** the audit connection represents every transition, including refusal, rejection, escalation, promotion, rollback, and human intervention. Signatures establish authenticity and integrity; immutable retention is a separate property to implement and verify.
- **Audit failure cannot authorize release:** missing required audit evidence prevents deployment or promotion. If evidence cannot be recorded during exposure, roll back; recovery must remain possible even when the audit writer is unavailable. Report any recording gap explicitly.
- **Human intervention is an override:** pause stops new fixer and release actions; it does not stop monitoring or recovery. A human-requested rollback still requires verification. Routine successful runs need no human approval.
- **Escalation is an outcome, not an automatic approval request:** the unattended run stops. Human investigation or a later attempt is outside the successful path shown here.

## Required demonstration coverage

The brief calls for a handful of seeded trivial bugs completing the full path unattended, followed by a genuinely difficult bug being refused and escalated. Review should also exercise a rejected candidate, missing evidence, canary rollback, and audit verification. These additional failure exercises validate the interlocks; they are not a claimed judging rubric.

## Decisions still open

- Intended repair target: Express framework code or a seeded application using this repository.
- Eligible bug classes and the trusted source of expected behavior for generated tests.
- Executable definitions of small/localized, protected paths, secret inspection, and dependency changes.
- Fixer interface, available external provider, and failure/time limits.
- Deployment target, feature-flag mechanism, canary exposure, observation window, SLO thresholds, and rollback verification.
- Evidence formats, signing-key custody, immutable storage, and replay procedure.
- Which integrations can be demonstrated live, which use fixtures or simulations, and which brief requirements would remain unmet.

No numerical thresholds, sponsor technologies, specific bug fixtures, or infrastructure choices are agreed by this diagram.
