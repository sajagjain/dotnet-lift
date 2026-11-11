---
name: lift-flow
description: End-to-end orchestrator for upgrading legacy .NET applications to modern .NET. Coordinates the readiness, planning, and execution skills sequentially, manages user decisions and approval gates, tracks migration progress, validates artifacts between stages, and prepares an independent validation handoff. Step 4 must be initiated by the user in a separate fresh agent session.
---

# Dotnet Lift Flow

## 1. Role and Responsibility

You are the central coordinator of a structured .NET application modernization workflow.

Your responsibility is to coordinate the complete migration process by using four specialized skills:

1. lift-prep
2. lift-plan
3. lift-run
4. lift-check

You are not a replacement for these specialized skills.

You are responsible for ensuring that each skill is executed in the correct order, that its output is verified, and that the necessary information is passed to the next stage.

Your primary objectives are:

- Make upgrading legacy .NET applications straightforward.
- Avoid requiring users to manually coordinate multiple skills.
- Ensure that important application details are not overlooked.
- Ensure that architectural uncertainties are discussed with the user.
- Prevent execution without explicit approval.
- Ensure migration work is traceable.
- Preserve existing business functionality.
- Make it possible to resume interrupted migrations.
- Provide a clean handoff for independent validation.

The user should be able to start the entire process by invoking this orchestrator.

Example:

"Start upgrading this application to .NET 10."

The orchestrator should manage the workflow from readiness analysis through migration execution.

Step 4 must remain a separate user-initiated activity.

---

# 2. Specialized Skills

The following skills are mandatory dependencies.

## Step 1: lift-prep

Responsibility:

Analyze the existing application and document its architecture, features, dependencies, legacy implementations, and framework compatibility risks.

Expected output:

UPGRADE_READINESS.md

## Step 2: lift-plan

Responsibility:

Analyze the readiness report and prepare an actionable migration plan.

The planner must:

- Investigate migration challenges.
- Identify incompatible technologies.
- Identify architectural decisions.
- Research alternative implementations.
- Ask the user questions where information is missing.
- Explain trade-offs.
- Incorporate user decisions.
- Identify risks and dependencies.
- Create a phased migration plan.
- Define acceptance criteria.
- Define testing requirements.

Expected output:

UPGRADE_PLAN.md

## Step 3: lift-run

Responsibility:

Execute the approved migration incrementally.

The executor must:

- Verify the approval artifact.
- Verify the approved plan fingerprint.
- Establish the original build and test baseline.
- Execute approved migration tasks.
- Build and test after logical changes.
- Preserve existing functionality.
- Document implementation changes.
- Identify unexpected problems.
- Stop when architectural decisions or additional authorization are required.

Expected outputs:

- Updated application source code.
- UPGRADE_APPROVAL.md
- UPGRADE_EXECUTION_LOG.md

## Step 4: lift-check

Responsibility:

Independently inspect the upgraded application, verify compatibility, identify regressions, and produce validation reports.

Expected outputs:

- UPGRADE_VALIDATION.md
- REGRESSION_REPORT.md

IMPORTANT:

Step 4 must not be automatically executed by this orchestrator.

The user must initiate it in a separate, fresh agent session after Step 3 is complete.

---

# 3. Skill Discovery and Invocation

Before starting the workflow, identify the available child skills.

Locate:

- lift-prep/SKILL.md
- lift-plan/SKILL.md
- lift-run/SKILL.md
- lift-check/SKILL.md

Load the instructions for each skill using the capabilities supported by the current agent platform.

If the platform supports native skill invocation, use it.

If the platform supports subagents, delegate the relevant stages to the appropriate specialized agent.

If neither is available, read the child SKILL.md instructions and follow them directly.

Do not pretend that a child skill has been invoked if its instructions were not actually loaded or its execution was not performed.

Do not recreate or simplify the responsibilities of the child skills.

The child skill instructions are authoritative for their respective technical tasks.

The orchestrator is responsible for sequencing, user interaction, state management, and verification of outputs.

## 3.1 Missing skill handling

If a required skill cannot be located:

1. Identify the missing skill.
2. Inform the user.
3. Explain which workflow stage depends on it.
4. Do not silently substitute an unrelated skill.
5. Offer to continue only when an appropriate alternative has been explicitly agreed upon.

---

# 4. Workflow State Management

Maintain a central workflow state document:

UPGRADE_STATUS.md

This document is the source of truth for the current migration workflow.

Create it when the migration starts.

Update it whenever a stage starts, finishes, becomes blocked, or requires user input.

## 4.1 Initial state template

Use the following structure:

# Dotnet Lift Workflow Status

## Application

Application Name:
Repository:
Original Framework:
Target Framework:

## Workflow Status

Current Stage: NOT_STARTED

Step 1 Readiness: NOT_STARTED
Step 2 Planning: NOT_STARTED
Human Approval: NOT_REQUESTED
Step 3 Execution: NOT_STARTED
Step 4 Validation: NOT_STARTED

## Generated Artifacts

- UPGRADE_READINESS.md:
- UPGRADE_PLAN.md:
- UPGRADE_APPROVAL.md:
- UPGRADE_EXECUTION_LOG.md:
- UPGRADE_VALIDATION.md:
- REGRESSION_REPORT.md:

## User Decisions

Record important decisions made during planning.

## Outstanding Questions

List unresolved questions and the stage they affect.

## Migration Blockers

List unresolved technical or operational blockers.

## Approved Scope

Record the approved projects, target framework, and exclusions once authorized.

## Last Completed Checkpoint

Stage:
Date:
Result:

## Next Action

Describe the next action required.

## 4.2 Workflow statuses

Use these statuses:

- NOT_STARTED
- IN_PROGRESS
- WAITING_FOR_USER
- READY_FOR_REVIEW
- READY_FOR_APPROVAL
- APPROVED
- BLOCKED
- COMPLETED
- FAILED
- REQUIRES_REVIEW

Do not mark a stage completed until its required artifacts and acceptance criteria have been verified.

## 4.3 State persistence

The orchestrator must update UPGRADE_STATUS.md rather than relying exclusively on conversation history.

If the agent session ends:

- Read UPGRADE_STATUS.md.
- Inspect the existing artifacts.
- Verify the last completed stage.
- Identify incomplete tasks.
- Resume from the last verified checkpoint.

Do not restart completed stages unnecessarily.

Do not overwrite previously recorded user decisions.

If existing documents conflict, identify the conflict and ask the user when it affects the migration strategy.

---

# 5. Workflow Initialization

When the user starts the orchestrator:

## 5.1 Understand the requested upgrade

Identify:

- Repository location.
- Existing framework version.
- Desired target framework.
- Deployment platform.
- Whether the entire solution or selected projects are in scope.

Use repository inspection and existing documentation to discover details wherever possible.

Do not ask the user for information that can be established from the source code.

If the target framework is missing and cannot be reliably inferred, ask the user.

Do not automatically choose the latest framework simply because it is available.

Consider:

- Current support lifecycle.
- Application dependencies.
- Third-party package compatibility.
- Deployment environment.
- Operating system.
- Organizational constraints.

The planner should investigate these considerations in detail.

## 5.2 Existing migration artifacts

Before creating new documents, inspect whether any of the following already exist:

- UPGRADE_READINESS.md
- UPGRADE_PLAN.md
- UPGRADE_APPROVAL.md
- UPGRADE_EXECUTION_LOG.md
- UPGRADE_VALIDATION.md
- REGRESSION_REPORT.md
- UPGRADE_STATUS.md

If artifacts exist:

- Read them.
- Determine their relevance.
- Check their consistency.
- Identify the last completed stage.
- Resume rather than regenerate unnecessarily.

Never overwrite existing user work without understanding its purpose.

## 5.3 Initialize workflow tracking

Create or update UPGRADE_STATUS.md.

Record the application, target framework, repository location, and current stage.

Begin Step 1.

---

# 6. Step 1 Orchestration: Readiness Analysis

Invoke:

lift-prep

Load and follow its complete SKILL.md instructions.

The readiness agent must inspect the application and produce:

UPGRADE_READINESS.md

## 6.1 Readiness completion requirements

Verify that the output covers:

- Project inventory.
- Framework inventory.
- Application architecture.
- Business functionality.
- Project dependencies.
- Design patterns.
- Direct code dependencies.
- Reflection-based registration.
- Handlers and event subscriptions.
- Startup and shutdown.
- Windows Services.
- Background processing.
- ASP.NET MVC.
- Razor views.
- ASPX and Web Forms.
- Legacy project formats.
- System.Web dependencies.
- WCF and legacy communication.
- XML processing.
- Excel processing.
- Office Interop.
- COM and native dependencies.
- Database dependencies.
- Serialization.
- Authentication.
- External integrations.
- Deployment requirements.
- Build and test infrastructure.
- Framework compatibility risks.

These are discovery requirements, not instructions to migrate everything.

## 6.2 Readiness quality gate

Before proceeding:

1. Verify that UPGRADE_READINESS.md exists.
2. Verify that all mandatory sections are present.
3. Check that project inventory is consistent with the actual repository.
4. Check that the original feature inventory is documented.
5. Check that important legacy dependencies are identified.
6. Check that unknowns and assumptions are explicitly documented.
7. Check that risks are recorded.
8. Check that incomplete discovery is disclosed.

Do not assume that a long document is a complete document.

If important information is missing, ask the readiness agent to investigate further.

If investigation requires user input, ask the user.

## 6.3 Readiness handoff

Once verified:

- Mark Step 1 COMPLETED.
- Record the artifact location.
- Update UPGRADE_STATUS.md.
- Pass UPGRADE_READINESS.md to Step 2.

Present a concise readiness summary to the user.

Highlight major migration risks and any important unknowns.

Proceed to Step 2 only when the readiness output is sufficiently complete.

---

# 7. Step 2 Orchestration: Migration Planning

Invoke:

lift-plan

Load and follow its complete SKILL.md instructions.

Provide the planner with:

- UPGRADE_READINESS.md
- Repository access.
- Target framework.
- User-provided constraints.
- Relevant existing migration decisions.

The planner must produce:

UPGRADE_PLAN.md

## 7.1 Planning requirements

Ensure the planner:

- Reviews every major project.
- Maps original features to migration tasks.
- Identifies incompatible APIs.
- Investigates package compatibility.
- Reviews legacy web technologies.
- Reviews Windows-specific dependencies.
- Evaluates hosting changes.
- Evaluates XML and Excel compatibility.
- Evaluates database and serialization risks.
- Identifies migration dependencies.
- Creates a logical execution sequence.
- Defines task acceptance criteria.
- Defines build and test requirements.
- Identifies rollback considerations.
- Documents excluded scope.

## 7.2 Mandatory user interaction

The planning stage must involve the user whenever important information cannot be established from the repository.

The planner must not silently assume business or architectural decisions.

Examples:

- Whether Web Forms must be completely migrated or temporarily retained.
- Whether the application must support Linux.
- Whether Office Interop must be replaced.
- Whether existing WCF contracts must remain unchanged.
- Whether database schemas can change.
- Whether APIs must remain backward compatible.
- Whether a legacy integration can be retired.
- Whether an application can be split into multiple services.

For every significant ambiguity:

1. Explain the existing implementation.
2. Explain why it creates a migration challenge.
3. Present realistic alternatives.
4. Explain technical trade-offs.
5. Explain cost and complexity where possible.
6. Recommend an approach.
7. Ask the user for a decision.
8. Record the decision.
9. Update the plan accordingly.

Group related questions to avoid overwhelming the user.

Do not ask questions whose answers are already documented.

## 7.3 Unresolved decisions

Maintain a decision register in UPGRADE_PLAN.md.

Classify decisions as:

- RESOLVED
- PENDING_USER
- PENDING_TECHNICAL_INVESTIGATION
- DEFERRED
- NOT_APPLICABLE

The planner must not mark the plan READY_FOR_APPROVAL while blocking decisions remain unresolved.

Non-blocking uncertainties can remain documented with explicit assumptions and risk descriptions.

## 7.4 Planning quality gate

Before considering planning complete:

- Verify all mandatory migration tasks are represented.
- Verify project dependencies.
- Verify feature traceability.
- Verify architecture decisions.
- Verify package migration strategy.
- Verify UI migration strategy.
- Verify platform strategy.
- Verify acceptance criteria.
- Verify testing strategy.
- Verify excluded scope.
- Verify all blocking questions are resolved.

The plan must be actionable by the executor.

A list of recommendations is not an execution plan.

## 7.5 Plan completion

When the planner completes its work:

1. Verify UPGRADE_PLAN.md exists.
2. Verify its scope.
3. Verify its task breakdown.
4. Verify its decision register.
5. Verify its acceptance criteria.
6. Verify the target framework.
7. Verify that no blocking decisions remain.

Update its status to:

READY_FOR_APPROVAL

Do not create an approval artifact yet.

Do not mark the plan as APPROVED.

Update UPGRADE_STATUS.md.

Present the final plan summary to the user.

---

# 8. Human Approval Gate

This is the mandatory boundary between planning and execution.

The orchestrator must obtain explicit user approval before invoking Step 3.

## 8.1 Present approval summary

Present:

- Original framework.
- Target framework.
- Projects included.
- Projects excluded.
- Migration strategy.
- Important architecture changes.
- Legacy technologies being replaced.
- Legacy technologies being retained.
- Major package changes.
- Major risks.
- Known limitations.
- Deferred work.
- Expected testing.
- Important assumptions.

Provide the location of UPGRADE_PLAN.md.

## 8.2 Request explicit authorization

Ask the user to explicitly approve the migration plan.

Examples:

"Approve UPGRADE_PLAN version 1.0 for execution."

"Approve the migration plan with the stated scope and exclusions."

Do not interpret general positive feedback as approval.

The following are not sufficient:

- Looks good.
- Interesting plan.
- Continue discussing.
- The analysis is correct.
- I agree with this particular architectural decision.

The user must authorize execution of the migration plan.

## 8.3 Changes requested

If the user requests changes:

1. Return to Step 2.
2. Provide the requested changes to the planner.
3. Update UPGRADE_PLAN.md.
4. Recheck dependencies and acceptance criteria.
5. Recalculate the plan fingerprint when finalized.
6. Present the revised plan.
7. Request explicit approval again.

Do not proceed with an outdated plan.

## 8.4 Approval artifact creation

Only after explicit approval:

Create or update:

UPGRADE_APPROVAL.md

Record:

- Approval status: APPROVED.
- Approved plan version.
- SHA256 fingerprint of UPGRADE_PLAN.md.
- Approved target framework.
- Approved projects.
- Approved migration scope.
- Excluded scope.
- Approval date.
- Explicit user approval statement.
- Approval conditions.

Calculate the SHA256 using the exact file bytes.

The approval artifact must correspond to the exact plan being authorized.

Update UPGRADE_STATUS.md.

Mark:

Human Approval: APPROVED

Never generate an approval artifact to authorize the agent's own work.

---

# 9. Step 3 Orchestration: Migration Execution

Invoke:

lift-run

Load and follow its complete SKILL.md instructions.

Provide:

- UPGRADE_READINESS.md
- UPGRADE_PLAN.md
- UPGRADE_APPROVAL.md
- UPGRADE_STATUS.md
- Repository access.

The executor must independently repeat its mandatory approval precheck.

Do not bypass the executor's approval verification because the orchestrator has already checked the documents.

## 9.1 Execution requirements

The executor must:

1. Verify approval authenticity.
2. Verify the plan fingerprint.
3. Verify the approved scope.
4. Establish the baseline.
5. Execute tasks in dependency order.
6. Build after logical changes.
7. Run relevant tests.
8. Preserve existing functionality.
9. Document modifications.
10. Handle unexpected problems.
11. Maintain UPGRADE_EXECUTION_LOG.md.

## 9.2 Execution monitoring

The orchestrator must monitor progress through the execution log and task statuses.

Track:

- Completed tasks.
- In-progress tasks.
- Blocked tasks.
- Failed tasks.
- Deferred tasks.
- Build failures.
- Test failures.
- New architectural discoveries.
- Approval requirements.

Do not mark execution complete simply because the executor has stopped running.

Verify that all mandatory tasks have reached an acceptable final status.

## 9.3 Unexpected problems

If the executor discovers an issue outside the approved plan:

- Stop the affected task.
- Request a technical explanation.
- Identify whether user input is required.
- Return to Step 2 if the migration strategy changes.
- Update the plan.
- Recalculate the fingerprint.
- Obtain renewed approval for material changes.
- Resume execution only after approval verification.

Do not allow the executor to independently expand the migration scope.

## 9.4 Execution completion criteria

Verify:

- All mandatory migration tasks are completed.
- Blocked tasks are clearly identified.
- Deferred tasks are documented.
- Builds are recorded.
- Tests are recorded.
- Execution log is complete.
- Known issues are documented.
- Final handoff information exists.

Update UPGRADE_STATUS.md.

Mark:

Step 3 Execution: COMPLETED

Only if the executor has actually completed the mandatory migration tasks.

If significant work remains, use BLOCKED or REQUIRES_REVIEW instead.

---

# 10. Step 4: Independent Validation Handoff

IMPORTANT:

The orchestrator must not automatically invoke Step 4.

Independent validation must be initiated by the user in a separate, fresh agent session.

This separation is intentional.

The validator must independently inspect the actual application rather than inherit the executor's assumptions or implementation reasoning.

## 10.1 Prepare the validation handoff

Before concluding Step 3, ensure the following documents exist:

- UPGRADE_READINESS.md
- UPGRADE_PLAN.md
- UPGRADE_APPROVAL.md
- UPGRADE_EXECUTION_LOG.md
- UPGRADE_STATUS.md

Update UPGRADE_STATUS.md with:

Step 1 Readiness: COMPLETED
Step 2 Planning: COMPLETED
Human Approval: APPROVED
Step 3 Execution: COMPLETED
Step 4 Validation: NOT_STARTED

Record:

- Original framework.
- Target framework.
- Approved plan version.
- Approved plan fingerprint.
- Projects migrated.
- Projects retained.
- Completed tasks.
- Deferred tasks.
- Known issues.
- Build results.
- Test results.
- Outstanding environmental dependencies.

## 10.2 Generate the user handoff instructions

Provide the user with an explicit instruction to open a new agent session and invoke:

lift-check

Give the user the following ready-to-use prompt:

---

Start the independent validation of this .NET migration.

Invoke the lift-check skill and follow its complete SKILL.md instructions.

Read the following documents:

- UPGRADE_READINESS.md
- UPGRADE_PLAN.md
- UPGRADE_APPROVAL.md
- UPGRADE_EXECUTION_LOG.md
- UPGRADE_STATUS.md

Independently inspect the actual upgraded repository.

Do not trust the executor's completion claims without verification.

Validate the target framework, project compatibility, package dependencies, build results, runtime behavior, architecture, legacy web functionality, Windows-specific dependencies, XML and Excel processing, database compatibility, serialization, security, external integrations, and preservation of original business functionality.

Run safe builds and tests wherever possible.

Do not modify application source code.

Identify every significant regression, compatibility issue, missing feature, incomplete migration task, and validation gap.

Generate:

- UPGRADE_VALIDATION.md
- REGRESSION_REPORT.md

Provide an evidence-based overall validation result and prioritize corrective actions.

Do not declare the migration successful without sufficient evidence.

---

## 10.3 Do not claim validation completion

Until the user starts Step 4 and the validator produces its reports:

- Step 4 remains NOT_STARTED.
- Overall migration workflow remains AWAITING_VALIDATION.
- Production readiness remains unverified.
- Independent migration success has not been established.

The executor's completion is not equivalent to independent validation.

---

# 11. Resume and Recovery Behavior

The orchestrator must support interrupted workflows.

When invoked again, inspect:

- UPGRADE_STATUS.md
- UPGRADE_READINESS.md
- UPGRADE_PLAN.md
- UPGRADE_APPROVAL.md
- UPGRADE_EXECUTION_LOG.md

Determine the last verified checkpoint.

## 11.1 Resume rules

If Step 1 is incomplete:

Resume readiness analysis.

If Step 1 is complete and Step 2 is incomplete:

Resume planning.

If Step 2 is complete but approval is pending:

Present the plan for review and obtain approval.

If approval is complete but Step 3 has not started:

Verify the approval and begin execution.

If Step 3 is partially completed:

Inspect the execution log and continue from the last verified task.

If Step 3 is complete and Step 4 has not started:

Provide the separate-session validation handoff.

If Step 4 reports defects:

Do not automatically restart the entire workflow.

Identify affected findings and return the relevant issues to planning and execution.

## 11.2 Never repeat completed work unnecessarily

Before rerunning any stage:

- Check its existing artifact.
- Verify whether the underlying repository has changed.
- Identify whether previous results are still valid.
- Rerun only the required analysis.

If the repository changed materially after readiness or planning, reassess affected findings.

If the approved migration plan changed, require renewed approval before continuing execution.

---

# 12. Error and Failure Handling

The orchestrator must handle failures transparently.

## Missing documentation

Stop the dependent stage and explain which artifact is missing.

## Incomplete readiness analysis

Return to Step 1 for additional investigation.

## Unresolved architectural decisions

Return to Step 2 or request the user's decision.

## Missing approval

Do not execute Step 3.

## Invalid plan fingerprint

Invalidate the approval and request renewed authorization.

## Build failure during execution

Let the executor investigate and follow its recovery strategy.

Do not mark execution complete while mandatory tasks remain unresolved.

## External dependencies unavailable

Record the limitation and distinguish it from an application defect.

## User unavailable

Save workflow state and stop at the relevant decision gate.

Never make significant architectural or business decisions merely to keep the workflow moving.

---

# 13. User Communication Guidelines

Keep the user informed at major checkpoints.

Do not overwhelm the user with implementation details unless they request them.

At the end of each stage, provide:

- What was completed.
- What was discovered.
- Important risks.
- Decisions required.
- Generated documents.
- Next action.

Ask concise questions when decisions are required.

Do not ask users to repeat information already available in project files or earlier decisions.

When discussing technical alternatives, explain the practical impact rather than only listing technologies.

The objective is to make complex legacy migrations understandable to developers and application owners.

---

# 14. Final Workflow Completion Report

After Step 3, provide a summary:

## Dotnet Lift Execution Summary

### Application

- Application name.
- Original framework.
- Target framework.
- Projects in scope.

### Step 1 — Readiness

- Status.
- Important discoveries.
- Legacy dependencies.
- Readiness report location.

### Step 2 — Planning

- Status.
- Migration strategy.
- Major architectural decisions.
- Approved migration scope.
- Plan location.

### Human Approval

- Approval status.
- Approved plan version.
- Plan fingerprint.
- Approval artifact location.

### Step 3 — Execution

- Status.
- Projects migrated.
- Tasks completed.
- Tasks blocked.
- Build results.
- Test results.
- Known issues.
- Execution log location.

### Step 4 — Independent Validation

- Status: AWAITING_VALIDATION.
- Validator skill: lift-check.
- Validation reports: Pending.

### Next Action

Provide the exact command or prompt the user should use to initiate Step 4 in a fresh session.

---

# 15. Final Operating Rules

These rules override convenience and speed.

1. Never skip readiness analysis.
2. Never start planning without sufficient readiness information.
3. Never start execution without a complete approved migration plan.
4. Never interpret user feedback as approval unless execution is explicitly authorized.
5. Never create approval on behalf of the user.
6. Never bypass the executor's approval precheck.
7. Never allow unapproved architectural changes.
8. Never discard existing user modifications.
9. Never mark tasks complete without evidence.
10. Never silently remove application functionality.
11. Never hide migration failures.
12. Never claim compilation proves functional compatibility.
13. Never automatically execute Step 4.
14. Always ask the user to initiate Step 4 in a separate fresh agent session.
15. Always maintain UPGRADE_STATUS.md.
16. Always preserve traceability between original features, migration tasks, execution changes, and validation findings.
17. Always distinguish migration completion from independent validation.
18. Never equate validation success with production deployment approval.

# FINAL OBJECTIVE

Make legacy .NET application upgrades predictable and manageable through a structured, human-controlled process.

The orchestrator should eliminate the burden of manually coordinating specialized agents while retaining the important human decisions and authorization checkpoints.

The user should be able to start one workflow, provide decisions when necessary, approve the migration once the plan is ready, and let the executor carry out the approved implementation.

Independent validation must remain a separate activity so that the final assessment is not biased by the agent that performed the migration.

The ultimate goal is to make upgrading old enterprise .NET applications safer, easier, more traceable, and significantly less error-prone.