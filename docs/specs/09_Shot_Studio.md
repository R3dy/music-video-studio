# Shot Studio — Area Specification

| Field | Value |
| --- | --- |
| Version | 0.1 |
| Status | Draft for UX and engineering review |
| Date | November 6, 2026 |
| View IDs | V26–V29; four view types |
| Governing documents | [Master UI specification](00_UI_Master_Spec.md); [Product requirements](Music_Video_Studio_PRD.md) |

## Purpose and scope

Shot Studio turns one approved shot plan into inspectable candidate footage. The user directs the camera and performance, submits a bounded generation request, imports external footage when useful, and chooses a take without losing earlier work.

A `ShotRevision` describes the planned continuous view. A `Take` is one realization of that plan. A `TakeVersion` identifies exact media after generation or processing. A `GenerationJob` records an execution attempt and may produce no usable media. The interface must keep those meanings visible through its labels and history.

P0 includes single-take generation/import, configurable provider controls, preflight, retained outputs, comparison, and selection. Optional multi-shot batches are P1 and use V27; they do not introduce another view. The initial application does not promise a general compositor or automatic continuity correction.

## Entry, dependencies, and completion

Enter from a storyboard card, linked lyric interval, dashboard blocker, asset usage link, or timeline clip. Keep the selected production, shot revision, and song cursor in context.

All four views remain inspectable before prerequisites are satisfied. New AI production requires current whole-song map approval, an eligible shot revision, required references and performer bindings, and a compatible connection. Local source/reference import remains available; attaching footage as a new production take follows the production gate. Existing media remains viewable when its inputs become stale.

A shot is ready for editorial use when a candidate has valid media and current required reviews. Selecting a candidate is not approval. Performance shots continue through [Lip-Sync Lab](10_Lip_Sync_Lab.md); shots without visible vocal performance can proceed to [Timeline](11_Timeline.md) after their relevant reviews.

## View inventory

Routes below are proposed deep links, not implemented API contracts.

| ID | View | Presentation | Proposed route |
| --- | --- | --- | --- |
| V26 | Shot direction and generation settings | Main editor with reference and settings inspectors | `/projects/:projectId/shots/:shotId/direction` |
| V27 | Generation preflight and cost review | Focused review panel or page | `/projects/:projectId/shots/:shotId/preflight` |
| V28 | Take gallery and player | Gallery with selected-media player | `/projects/:projectId/shots/:shotId/takes` |
| V29 | Take comparison and selection | Synchronized comparison editor | `/projects/:projectId/shots/:shotId/compare` |

## V26 — Shot direction and generation settings

Place the shot name, scene, revision, intended song interval, and approval/dependency indicators above the editor. The main canvas shows the approved storyboard/reference frame with playable song context. A reference rail identifies cast, looks, location, props, and frame assets by revision. The inspector groups framing, camera movement, blocking, gestures, gaze, expression, and intensity before technical provider settings.

The primary action is **Review generation**. Secondary actions save a revised shot plan, inspect source references, audition the exact audio interval, import footage, and open prior takes. A “suggest direction” request proposes editable text; it does not submit a render. Reference selection opens the existing asset tools rather than an independent asset database.

Read the `ShotRevision`, scene/brief revisions, performance bindings, asset revisions, approved map, and adapter capabilities. Save creative or configuration changes as a new shot revision or a versioned pending request. Preserve provider-specific parameters without making them authoritative shot intent.

Only display supported controls as actionable. Explain disabled settings such as unavailable reference images, unsupported audio conditioning, or fixed duration increments. Show intended editorial duration separately from the requested provider duration and context handles. A 5–15 second default must not become a schema restriction. Changing provider revalidates settings and makes discarded/unsupported parameters visible before preflight.

## V27 — Generation preflight and cost review

Present the exact request as a compact review: shot and revision, chosen provider/model, media inputs, requested duration, usable song interval, handles, output profile, quantity, and planned stages. If video generation will be followed by lip sync, show each stage and whether it is included in the current bounded request. The user can authorize only the base visual stage and review it before any subsequent processing.

Separate blockers from advisory findings. Block missing whole-map approval, invalid ranges, unsupported capabilities, unavailable required references, or a request exceeding configured limits. Advisory findings may include difficult camera angles or uncertain input quality. Cost displays distinguish estimate, unknown estimate, and already incurred cost. Never show unknown as free.

The primary action names its scope, such as **Generate 1 take**. It captures an immutable input snapshot, request identifier, and applicable spending/runtime limits. Repeat clicks, reloads, or network reconnects must address the same submission intent. If inputs change while this view is open, mark the review outdated and rebuild its summary before submission.

On submission, move to V28 with the new job visible. Do not start another shot or an unstated retry. An uncertain provider outcome links to job reconciliation in [Connections & Jobs](13_Connections_and_Jobs.md). Cancellation reports “requested” until confirmed and makes no refund claim. P1 batch scope is opt-in, lists every shot/stage, and requires the relevant reviewed performance audition.

## V28 — Take gallery and player

Display take cards for the selected shot with thumbnails, provenance, generated/imported label, job state, review indicators, usable duration, and cost where known. A take card can expand its processed versions without duplicating the underlying shot. The selected take uses a player with master-song context and an explicit audio-source selector; generated audio is not silently substituted for the song.

Primary actions open the candidate for review or comparison. Secondary actions import a take, inspect its job, open Lip-Sync Lab, set a candidate as preferred, or place a reviewed version into a cut. Preserve a failed job in history even if it produced no take. A provider-successful job whose returned media fails validation appears as unusable output, with its cost retained.

Import records file metadata, provenance, the intended shot revision, and the source-song relationship. A file without a known timing relationship can remain an imported candidate but cannot be presented as sync-ready. Inspect actual duration, frame rate, resolution, and valid media before enabling approval or placement.

The current preferred take is distinct from the version used by an existing timeline clip. New preferred selection changes the default for future placement only. Replacing existing placements requires a separate scoped action that lists affected clips and reruns compatibility checks. Open historical approvals as records of the reviewed versions even when a candidate is stale for current inputs.

## V29 — Take comparison and selection

Compare two selected take versions side by side or through a single-player A/B switch. Synchronize playback by source-song time whenever both mappings are valid. If either mapping is unknown, show that limitation and permit manual visual comparison without implying synchronized playback. Keep exact version names and generation settings visible.

The comparison inspector groups direction, identity, continuity, technical validity, and visible-performance review. These dimensions may differ: a candidate can have excellent camera movement but need a new sync pass. Time-stamped notes attach to the exact take version and song/media position. Do not merge distinct quality dimensions into an unexplained winner score.

Primary actions select a preferred candidate or open a required review. Secondary actions retain both, reject a candidate with a reason, inspect differences in inputs, or send a selected visual base for sync repair. Selection does not overwrite or delete the other take. A processed version inherits provenance, not automatic approval of its changed pixels.

Read take versions, review records, shot inputs, and media mappings. Write explicit preference, review notes, and approved selection actions. Any “use in cut” action chooses a target cut and exact version; incompatible duration or timing returns the user to the relevant inspector rather than silently looping or stretching footage.

## Skills and bounded actions

The generation skill consumes an approved input snapshot, selected shot, connection, references, and authorized request scope. Its output is a job record and candidate media, not an approved shot. Creative suggestions remain proposals until applied. Deterministic validators check eligibility and returned media outside the LLM.

An integrated video-and-audio model is supported only through truthful adapter capabilities. If external phoneme timing is not a supported control, the UI describes the input as audio conditioning and retains the map for planning and review.

Skill execution details reuse V45 in [Shared Tools](14_Shared_Tools.md). Job transport retries may reconcile an existing request; creating another chargeable generation requires the declared retry policy and remaining authorized scope. Exhausted or uncertain scope stops submission.

## States and recovery

| State | Behavior |
| --- | --- |
| Empty shot | Show the selected plan and missing references; offer eligible setup/import actions. |
| Loading media | Preserve selection and readable metadata; show media-loading progress separately from generation status. |
| Blocked production | Name the exact missing approval/capability and link to its source workspace. |
| Queued/running | Keep editing other work available; show captured inputs rather than implying later edits affect the job. |
| Submission unknown | Prevent automatic resubmission; expose reconciliation and job identity. |
| Failed job | Retain failure, cost, and inputs; a retry is a new bounded attempt after outcome is known. |
| Invalid output | Explain metadata/decoding/timing failure and retain the artifact for inspection. |
| Stale candidate | Preserve playback and historical review; identify impacted dependencies and allowed repair paths. |
| Lost session | Restore selection and reconcile jobs from persisted IDs without restarting them. |

## Data and handoffs

The storyboard supplies shot intent and coverage. Cast/world and assets supply exact reference revisions. Lyrics supplies event occurrences and master-relative timing. Lip-Sync Lab consumes a chosen take version, never an ambiguous “latest” media URL. Timeline receives an explicit version plus its usable range and source-song mapping.

Proposed supporting fields include `preferred_take_version_id`, request quantity, and a validated media-mapping record. These refine the PRD relationships; they do not create additional product areas. Dependency updates open V47 for selective review instead of invalidating unrelated takes.

## Acceptance criteria

| ID | Behavior | PRD traceability |
| --- | --- | --- |
| SHOT-AC01 | Opening a shot before map approval succeeds; UI, API, and skill generation requests remain blocked. | SHT-01; AC-05 |
| SHOT-AC02 | A one-take request captures its exact scope and cannot start another shot or undisclosed stage. | SHT-01, SHT-05; AC-07, AC-20 |
| SHOT-AC03 | Unsupported settings and unknown cost are explained before submission. | SHT-04, SHT-05; §12.2–12.3 |
| SHOT-AC04 | A timeout or reload preserves the original job identity and does not buy another generation. | AC-10, AC-17 |
| SHOT-AC05 | An imported/generated file with invalid duration, rate, or timing cannot become an approved placement silently. | SHT-02, SHT-06; AC-14 |
| SHOT-AC06 | Failed jobs without takes and successful jobs with unusable outputs remain inspectable with costs. | §9, §12.3 |
| SHOT-AC07 | Repairing lip sync retains the visual base and all earlier take versions. | SHT-03, SHT-07; AC-08 |
| SHOT-AC08 | Choosing a new preferred take does not silently replace clips already in a cut. | EDT-01; §9.1, §13 |
| SHOT-AC09 | Timing handles retain their mapping to the intended usable interval. | SHT-08; AC-12 |
| SHOT-AC10 | P1 batches remain explicit and require the relevant reviewed audition. | SHT-09; §8.6 |

## Accessibility and responsive behavior

Provide keyboard access to shot selection, take selection, A/B playback, and review actions. Announce generation and validation state changes without stealing editing focus. Pair status colors with labels. Comparison players must expose which candidate is audible and which one is selected.

On narrow screens, use one player with an accessible candidate switch and sequential settings sections. Preserve all cost, approval, and timing information. Desktop offers the full side-by-side workflow; a smaller viewport must not silently remove generation prerequisites.

## Open design decisions

- Select initial provider adapters and their validated output profiles through the PRD benchmark work.
- Determine the default context-handle duration per adapter; record it explicitly and allow only validated ranges.
- Decide whether preferred-take selection appears on the gallery card or only within comparison; its semantics remain the same.

