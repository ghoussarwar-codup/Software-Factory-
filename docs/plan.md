# Software Factory implementation plan

Status: Proposed for approval

## Entry conditions

- Approve the design and this plan before writing implementation code.
- Choose the pilot BRD and first user group before implementation. Keep the factory tool-agnostic. The BRD should yield about 10–20 stories and cover login and roles, a form with validation, a list with filters and a background job such as email. It should exercise unit, integration, browser and API contract tests, plus deployed alerting and recovery checks.
- Obtain access to the private implementation repository, the pinned Munder Difflin baseline and a Windows 11 pilot laptop with representative memory and network constraints. Confirm existing Claude and Codex accounts work.
- Identify the customer-owned GitHub repository and AWS staging and production environments. Run CI capability checks during setup. Production protection must be verified before promotion. A pending plan or missing production reviewers permits staging only.

## Build order options

| Option | Sequence | Trade-off |
| --- | --- | --- |
| Thin vertical slice, then deepen (recommended) | Build one story through every stage and into staging, then harden and expand across the pilot before the production release. | Tests the actual workflow and handoffs early. Requires care to keep the first slice small while exercising real enforcement. |
| Enforcement and recovery first | Prove startup validation, worker recovery, trust boundaries and GitHub protection up front, then connect the product workflow. | Exposes the highest technical risks early, with later feedback on the approval and evidence screens. |
| Requirements and acceptance first | Build scope, spec and traceability on the full pilot BRD, then add worker execution and release. | Surfaces ambiguity and coverage gaps early, with later proof that CI, worker recovery and production protections work end to end. |

Use the thin vertical slice. Prove the GitHub protections and Windows auth and restart behavior early within that sequence. Take the pilot to staging and then production as two steps of a single milestone.

## Milestone 1 — Windows 11 and GitHub Actions production pilot

### 1. Private fork and setup checks

- Base the private desktop repository on Munder Difflin main at `c1eac0d2`. Keep the canonical project kit there. Verify the chosen fork arrangement preserves upstream licensing and repository visibility.
- Run setup on Windows 11 with PowerShell 7. Probe free RAM, WSL2/Docker availability where needed, Git transport and hosting APIs independently, and TLS trust for Git and browser tooling. Exercise mixed-case paths and worktree cleanup with junctions and symlinks.
- Detect the GitHub plan where available and effective rules. Set up a human-only merge queue where supported, or run the protected-branch fallback proof. Show the path and why. Test that worker identities cannot push or force-push main, merge, enqueue, spoof evidence, change trusted CI gate definitions or approve a deployment. Verify the fallback rejects stale and failing checks including a base update. Stop eligibility if controls are ineffective. Verify required production reviewers and show the explicit blocker if missing.
- Configure an authoritative Codex login with single-session access. Kill a worker during and after credential refresh to prove the next worker starts successfully or reports genuine reauthentication needs. Verify job isolation and credential redaction with synthetic sensitive values.

### 2. Thin slice through staging

- Implement the local workflow and approval records, BRD intake and questions, spec and BRD-to-test mapping, and dependency-aware queue. Complete the first story through requirements and spec approvals, test-first build, independent review, GitHub CI and human merge.
- Install the versioned kit in the pilot project. Exercise all stage types, both pre-edit and completion checks, report headings, provider evidence, Git rules, CI proof and the PR template. Demonstrate a missing or malformed stage fails before spawn with a visible error.
- Persist job intents, commits and handoffs. Kill workers for restart, inactivity and a test token cap. Restart the app during work and around checkpoint and PR publication. Verify new workers resume automatically, keep accepted changes, use the proper brief, and leave exactly one PR. Prove the queue holds heavy work when memory is low and runs at most one heavy job.
- Stage an artifact built by customer CI. Record its digest, run security and dependency checks, deploy it to AWS staging and verify the release evidence path. Exercise both allowed and blocked production approval states without deploying an unapproved artifact.

### 3. Complete the pilot BRD and verify the gates under load

- Process all agreed stories through the same app workflow with the pilot users. Use Claude-to-Codex for most stories and Codex-to-Claude for one or two. Verify Codex earns credit by running checks itself. Verify fresh builder-model workers make fixes and the other model checks each updated commit.
- Prove the blocked-story flow with failure evidence for both fix rounds, one human-authorized fresh attempt, blocked dependants and progress on independent stories. Verify unanswered or deferred approved requirements block release. Verify revised scope invalidates prior evidence.
- Demonstrate that a passing suite with an omitted BRD case or a weakened assertion still fails review. Verify test-first failure evidence, separate UI and API validation-rule tests, and human-only deployed-environment labels with staged or production-drill evidence. Check candidate integration after main advances and after multiple story merges.
- Produce the architecture document, runbook and BRD-to-story-to-code-to-test map. Confirm full unit, integration, end-to-end and API contract CI, clean security and dependency results, and zero open must-fix findings. Export sanitized evidence summaries and use them to select the first post-pilot hardening priorities.

### 4. Promote the approved pilot artifact and exercise rollback

- Verify staging, artifact digest, required protection, logs, alerts, backup restore readiness, schema compatibility and a recorded previous-version baseline before promotion. Verify that a stale or changed artifact cannot use an existing approval.
- Obtain the human approval in the protected GitHub production environment. Customer CI deploys, verifies health and monitoring, triggers and confirms an alert, rolls back to the baseline and proves the baseline works with the current schema. CI then verifies the digest and restores the same approved artifact within the run. If the drill fails, follow the runbook and keep the release incomplete.
- Record backup and rollback evidence, production-only requirement evidence, CI approval and final health in the app. The pilot completes only when the real staging-to-production release and rollback drill pass, all approved BRD lines have passing evidence and users have completed the workflow without hidden operator intervention.

## Later milestones

1. Harden the stages that caused retries, stalls, review escapes or manual intervention during the pilot. Product release policy requires drills on first production release, on releases with schema changes and on a monthly schedule by default. Verify those triggers and the separate destructive-migration approval and restore path before customer use.
2. Add and qualify Bitbucket Pipelines on Premium. Prove that stale and failing CI blocks human merge, that only one PR is eligible at a time, and that deployment requires an authorized human. Keep Bitbucket client use blocked until it passes.
3. Gate client onboarding on the visible per-project data policy and verify provider eligibility and data exclusions before dispatch. Qualify macOS and then Linux with the same recovery, hooks and resource checks before support expands.
