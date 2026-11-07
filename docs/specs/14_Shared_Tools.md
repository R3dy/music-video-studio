# Shared Tools — Area Specification

| Field | Value |
| --- | --- |
| Version / status | 0.1 / Draft |
| Date | November 6, 2026 |
| Views | V44–V47; four reusable view types |
| Primary requirements | PRD sections 6, 11, 13, and 17; AC-05–08, AC-11, AC-15, AC-17 |
| Related specifications | [Master UI specification](00_UI_Master_Spec.md), [Product requirements](Music_Video_Studio_PRD.md) |

## Purpose and scope

Provide a consistent AI director, actionable proposals, execution history, and dependency-aware revision controls across all production workspaces. These tools operate on the same project records as direct editing and the wizard. Conversation is a way to request work; it is not the project database.

The shared tools appear as contextual panels or focused views. Their context follows the selected project, object, and interval unless the user pins a different scope. They do not add a second navigation hierarchy, a skill marketplace, a visual workflow builder, or another set of wizard screens. A skill's identity and execution details are inspectable here; editing skill source is outside this scope.

## View inventory

These are proposed routes and query parameters. The application shell retains the originating workspace and selection.

| ID | View | Presentation | Proposed deep link |
| --- | --- | --- | --- |
| V44 | AI director conversation | Contextual assistant panel or focused conversation | `/projects/:projectId?panel=director&object=:objectId` |
| V45 | Skill proposals and execution details | Proposal/review panel with run details | `/projects/:projectId?panel=skill&run=:runId` |
| V46 | Revision history and comparison | History drawer with structured comparison | `/projects/:projectId?panel=history&object=:objectId` |
| V47 | Change-impact review | Dependency summary and resolution panel | `/projects/:projectId?panel=impact&change=:changeId` |

## V44 — AI director conversation

The panel header states the current scope: project, workspace, selected object(s), and song interval where relevant. Show compact reference chips for the source/master revision, selected take, or map span actually being used. The conversation contains user requests, assistant explanations, and linked action receipts. Proposed edits and jobs open V45 rather than being represented only as unstructured promises in chat.

**Primary action:** submit a request with the visible context. **Secondary actions:** attach or select existing project references, pin/unpin scope, open linked artifacts, and stop an in-progress assistant response. Stopping text generation is distinct from canceling a provider job; running jobs remain visible and use the cancellation controls in [Connections & Jobs](13_Connections_and_Jobs.md).

The assistant reads authorized structured state and referenced media metadata. It writes conversation messages and bounded skill requests; domain mutations follow their normal validation path. A statement in chat cannot set map approval, approve a rendered take, change a budget, or replace the master recording without the corresponding domain action. Explicit commands can perform eligible reversible edits through that action path and return an inspectable receipt, avoiding a confirmation dialog for every small change.

When context changes, the next request uses the newly visible scope unless pinned. Existing messages retain their historical references. For example, selecting take B after discussing take A must not make a later “repair this” silently operate on A. Ambiguous target references require clarification or a proposal identifying the target before execution. The assistant must not manufacture provider support, timing certainty, prices, or completed work.

## V45 — Skill proposals and execution details

Display the skill name/version, input snapshot, intended scope, prerequisites, permitted outputs, and current execution state. A proposal shows a structured before/after diff for editable objects and a list of planned jobs for cost-incurring work. Include provider/model, selected media, intervals, affected objects, estimated or unknown cost, runtime limits, and retry policy where applicable. Present readable action summaries; do not expose secrets or internal reasoning traces.

**Primary actions:** apply an eligible proposal, submit the displayed bounded workflow, or inspect completed outputs. **Secondary actions:** edit the proposed creative fields, reject the proposal, stop a resumable workflow at a safe boundary, and open an individual job. Human review boundaries remain visible—for example, a transcription proposal leads to alignment review; successful lip-sync processing leads to take review.

The application revalidates inputs and permissions at execution time. If a referenced object changed after the proposal was created, the proposal becomes stale and needs an updated diff; it cannot overwrite a newer revision. The whole-song approval gate applies to creative planning/production here exactly as in their direct controls. A skill cannot promote a model confidence score into human approval.

One request to generate one shot identifies exactly what is included, such as visual generation followed by one selected sync pass. It does not authorize endless retries, another shot, or an unlisted enhancement. Optional batches remain P1. Existing authorization for the displayed bounded workflow should not trigger duplicate approval prompts at each internal non-review step.

A completed run shows proposed versus actually applied changes, output IDs, validation results, job links, and any remaining review tasks. Partial completion preserves completed outputs and the checkpoint. Resume evaluates only remaining work and does not resubmit completed or uncertain provider jobs.

## V46 — Revision history and comparison

The history list identifies an object's revisions with time, origin—manual edit, skill, import, or processing job—and review/validity state. Select two compatible revisions to show a structured comparison. Text displays changed passages; timing shows shifted event boundaries and affected intervals; media references show the exact assets and thumbnails; complex media comparison links to the relevant take or asset comparison view.

Historical approval and current validity are separate badges. A take can be “approved for map revision 3” while stale for the currently selected revision 4. This must not be presented as if the user never reviewed it. Compare parent/child processing stages without confusing a base visual take with its lip-sync derivative.

**Primary actions:** inspect a revision and compare it with another. **Secondary actions:** navigate to its sources or consumers, view its associated skill/job, and restore prior content as a new current revision where that action is supported. Restoration runs dependency analysis and creates a new revision; it does not erase subsequent history or mutate immutable media.

Undo is a reversible edit operation with a recorded outcome, not an eraser for provider execution. Undoing a selection may restore an earlier selected take; it cannot cancel an already submitted job or refund a charge. Restoring an older reference does not silently retarget every timeline clip to it. The current selection and any affected consumers are evaluated explicitly through V47.

This view reads version chains, artifact metadata, approvals, dependencies, and execution provenance. It writes only supported restore/undo actions through the object's owning service. A missing old media file must be shown as unavailable; the UI must not fabricate a preview or treat metadata as proof that the bytes are still retrievable.

## V47 — Change-impact review

Show the proposed source change at the top, followed by affected consumers grouped into unaffected, requires review, and requires regeneration. Each row identifies the object, relevant interval, dependency type, and reason. Provide counts plus concrete examples, such as “Three performance shots use the changed vocal assignment; four narrative shots remain valid.” Users can inspect the underlying diff and open the referenced artifact without losing this review.

Impact analysis is semantic, not merely a comparison of version numbers. A spelling correction may affect captions without changing generated video. A pronunciation or vocalist change can affect timing and sync review. A different audio crop can invalidate an existing sync mapping. Replacing the master recording has broad timing consequences and requires a corresponding approved map for new creative production.

**Primary action:** apply the source change and record the resulting validity updates. **Secondary actions:** return to editing, narrow the proposed change, inspect dependencies, and open specific repair tasks. Applying the source revision does not buy replacement footage, discard old takes, or change a historical approval. Any regeneration is a separate bounded action using the normal preflight.

The panel reads the proposed revision, current consumers, exact event/media dependencies, and approval records. It writes a new source revision and dependency-validity results as one consistent operation. If the project changes while the panel is open, recalculate impact before applying; do not apply a stale count to a newer cut. Where dependency evidence is incomplete, show an explicit “review required: impact uncertain” reason rather than declaring the consumer unaffected.

## Shared state and recovery behavior

| State | Required behavior |
| --- | --- |
| Empty | Offer context-appropriate actions or explain that no history exists. Do not imply a required chat interaction. |
| Loading | Preserve the selected object and last committed data; distinguish retrieving context from executing work. |
| Blocked | Name the missing prerequisite and link to its owning view. Opening the panel remains allowed. |
| Failed assistant response | Preserve the user request and existing project state. Retrying text generation must not repeat already executed actions. |
| Partial skill execution | Show completed outputs, remaining steps, and their job states. Resume only eligible unfinished work. |
| Unknown provider outcome | Link to reconciliation; do not submit another request as a generic “try again.” |
| Stale proposal or diff | Display changed inputs and require a refreshed comparison before applying. |
| Session interrupted | Restore conversations, references, proposals, and run/checkpoint IDs without restarting work. |

## Data contracts and cross-area handoffs

`SkillDefinition` supplies the contract and version. `SkillRun` records the input snapshot, bounded request, validations, outputs, and execution state. `GenerationJob` remains the authoritative provider-operation record. `Review`/`Approval` belongs to the exact artifact version and reviewer; an assistant message is never an equivalent approval record.

`Dependency` relates a source revision or interval to its consumers. Proposed supporting fields include a change-set identifier, reason code, and unresolved-impact flag; these refine the existing revision/impact workflow rather than introducing a new feature. Applied changes should carry an expected input revision so concurrent edits cannot be overwritten silently.

Deep links preserve project, object, interval, and return destination. The wizard can highlight a shared proposal or a review task using the same IDs. It must not store a separate hidden completion flag that disagrees with the artifact's actual state. For bounded changes, show a receipt and undo when feasible; reserve explicit approval controls for meaningful review or execution boundaries defined by the PRD.

## Acceptance criteria

| ID | Required behavior | PRD trace |
| --- | --- | --- |
| SHARED-AC01 | A request visibly identifies its context; switching selected takes cannot silently target the previous take. | Section 6.1, section 11 |
| SHARED-AC02 | UI, API, and skill execution share the whole-song map gate and exact-revision validation. | AC-05–06 |
| SHARED-AC03 | A proposed one-shot workflow cannot execute unrelated shots, hidden enhancements, or unbounded retries. | AC-07, section 11.3 |
| SHARED-AC04 | An outdated proposal cannot overwrite a newer object revision; the user receives an updated diff. | Section 13, AC-11 |
| SHARED-AC05 | Model confidence and assistant prose cannot issue human map or rendered-performance approval. | AC-15, MAP-09, SYN-07 |
| SHARED-AC06 | History preserves old take versions and approvals when a source change marks them stale. | AC-08, AC-11 |
| SHARED-AC07 | A spelling-only edit does not automatically regenerate video; audio/crop changes identify dependent sync work. | Section 13.1, AC-11 |
| SHARED-AC08 | Applying a change-impact result does not start paid repairs or delete approved assets. | AC-07–08, AC-11 |
| SHARED-AC09 | Resuming a run after interruption retains finished outputs and reconciles uncertain provider jobs before retry. | AC-10, AC-17 |

## Accessibility and responsive behavior

All proposals and comparisons need keyboard-accessible alternatives to visual diff highlighting. Describe added/removed fields, shifted time intervals, and review state in text. Restore focus to the initiating control when a panel closes. Announce completed actions without reading every streaming token or progress update aloud.

On narrow screens, shared tools may occupy the full workspace while retaining a clear return link and scope header. Do not hide the target object, proposed job count, unknown cost, or blocked reason to fit the viewport. A pinned context must remain visibly pinned.

## Open design decisions

- Choose how much provenance appears by default versus expanded detail while keeping input identity and action scope immediately visible.
- Define which reversible edits apply directly with a receipt and which significant proposals use explicit diff review; domain approval boundaries remain fixed.
- Specify revision retention and checkpoint granularity without implying undo can reverse external execution.
