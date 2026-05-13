# Agent Operating Guide

This repository is in Harness v0. There is no product implementation yet.

The current job of agents is to preserve and grow the collaboration harness
before writing application code. Do not scaffold application source folders,
platform shells, package scripts, CI, or tests unless a later story explicitly
moves the project into implementation.

## Source Of Truth

Read in this order:

1. `README.md` for project status.
2. `docs/HARNESS.md` for the human-agent operating model.
3. `docs/FEATURE_INTAKE.md` before turning any prompt into work.
4. The user-provided spec or prompt, when one exists.
5. `docs/product/` for current product contracts.
6. `docs/ARCHITECTURE.md` before proposing implementation shape.
7. `docs/stories/` for story packets and backlog.
8. `docs/TEST_MATRIX.md` for proof status.
9. `docs/decisions/` for why important choices were made.

This harness does not ship with a project-specific `SPEC.md`. When the human
provides a spec for a new project, treat that spec as input material for the
first buildout. Derive product docs, story packets, architecture decisions, and
validation expectations from it. Product docs, stories, tests, and decisions
then become the living contract that agents should update as the system evolves.

## Task Loop

For every task:

1. Classify the request with `docs/FEATURE_INTAKE.md`.
2. Identify whether the input is a new spec, spec slice, change request, new
   initiative, maintenance request, or harness improvement.
3. Locate the affected product docs and story files.
4. Check `docs/TEST_MATRIX.md` for existing proof and gaps.
5. Work only inside the selected lane: tiny, normal, or high-risk.
6. Before finishing, ask:
   - Did product truth change?
   - Did validation expectations change?
   - Did architecture rules change?
   - Did we discover a repeated failure pattern?
   - Did the next agent need a clearer instruction?
7. Update routine harness files directly, or add a proposal to
   `docs/HARNESS_BACKLOG.md` when the change is structural.

## Acceptance Rhythm

Every acceptance gate — every row in `docs/TEST_MATRIX.md`, every story
acceptance criterion, every validation command — completes as a SINGLE motion.
The motion has three parts and they happen together, not as a final pass:

1. **Run the gate.** Execute the literal command (build, test, screenshot,
   grep, archive — whatever the row asks for).
2. **Save the artefact.** Whatever proof the gate produces (log file,
   screenshot, recording, archive) lands under `evidence/` (or wherever the
   project's contract says) before the next step.
3. **Update the contract and commit.** Flip the matrix row from `not run` to
   `green`, replace the row's Proof cell with a link to the artefact path,
   and commit. The commit message names the row (e.g.
   `test(US-003): volume ramp curve — green`).

These three are one motion. If any of them is missing, **the gate is not
done** — even if the underlying command succeeded. A gate that passed but
isn't reflected in `docs/TEST_MATRIX.md` is the same as a gate that didn't
run.

### Commit granularity

One acceptance criterion = one commit. Mega-commits across many gates are
forbidden because they break the link between contract row and proof, and
they make `git bisect` useless when something later regresses.

### No fake-green

If a gate cannot be honestly proven in the current environment (physical
hardware missing, paid account missing, signing certificate missing, network
to a gated system unavailable, etc.), leave the matrix row unchanged and add
a one-line entry to `RESUME.md` naming the specific resource that's missing.
`RESUME.md` is for documented blockers — never a substitute for matrix
updates that were skipped.

## Harness Change Policy

Agents may update directly:

- Story status and evidence.
- `docs/TEST_MATRIX.md` rows.
- Links from story packets to product docs.
- Validation notes and reports.
- Small clarifications tied to the current task.

Agents should ask for human confirmation before:

- Changing architecture direction.
- Removing validation requirements.
- Changing the source-of-truth hierarchy.
- Changing risk classification rules.
- Replacing the feature workflow.

## Done Definition

A task is done only when:

- The requested change is completed or the blocker is documented.
- Relevant docs, stories, and test matrix entries remain current.
- Every validation command for the requested change was run AND its row in
  `docs/TEST_MATRIX.md` is `green` with a linked evidence path. Per the
  Acceptance Rhythm section above: running the command without updating the
  matrix and committing does not count as done.
- Missing harness capabilities were added to `docs/HARNESS_BACKLOG.md`.
- The final response says what changed and what was not attempted.
