# Review & Export — Area Specification

| Field | Value |
| --- | --- |
| Version / status | 0.1 / Draft |
| Date | November 6, 2026 |
| Views | V37–V39; three reusable view types |
| Primary requirements | QC-01–02, EXP-01–04, EDT-02; PRD AC-18–20 |
| Related specifications | [Master UI specification](00_UI_Master_Spec.md), [Product requirements](Music_Video_Studio_PRD.md) |

## Purpose and scope

Turn a selected cut into an inspectable delivery. This workspace verifies the edit's dependencies and reviews the actual encoded output before labeling it an approved final. It also produces downloadable excerpts and portable project packages. Completing one shot must remain useful: an excerpt is reviewed against its declared interval, rather than being blocked because the rest of the song has no video.

The workspace opens whenever the project exists. It may show missing inputs and earlier deliveries before current work is ready. New final delivery requires the applicable current approvals, including the whole-song map. Historical downloads remain accessible when later edits make them stale for the current project. Draft output is visibly distinguished and cannot acquire final status through filename choice alone.

## View inventory

Routes below are proposed UI addresses, not implemented endpoints.

| ID | View | Presentation | Proposed deep link |
| --- | --- | --- | --- |
| V37 | Quality checklist and issues | Workspace with issue list, preview, and contextual details | `/projects/:projectId/review?cut=:cutId` |
| V38 | Export configuration | Focused configuration view with scope summary | `/projects/:projectId/export?cut=:cutId` |
| V39 | Rendered delivery review and downloads | Encoded-media player and delivery detail | `/projects/:projectId/deliveries/:deliveryId` |

## V37 — Quality checklist and issues

The header identifies the selected `Cut` revision, master recording, map revision, and review scope: full song or a named excerpt with explicit source interval. A left column groups findings into coverage, approvals/dependencies, continuity, vocal performance, and technical validity. The center previews the selected cut; selecting an issue seeks to its interval and highlights the relevant clip. The details pane states the finding, evidence, severity, and resolution destination.

**Primary actions:** run or refresh checks, review a selected issue, and continue to export when eligible. **Secondary actions:** filter by category/severity, add a review note, inspect the referenced approval, and open the exact clip or take in its owning workspace. Continuity concerns can be recorded for human judgment; deterministic failures such as missing media or an invalid time mapping cannot be dismissed as aesthetic choices.

This view reads the cut, clips, selected take versions, master/map revisions, approvals, and dependencies. It writes review findings and human review outcomes, never replacement media. Rechecking creates a result for a specific snapshot. If inputs change during the check, display the result as superseded and require a fresh check for current final delivery.

A visible vocal performance requires its own applicable review. An approved lyric map or high automated score cannot substitute. A rejected performance links to [Lip-Sync Lab](10_Lip_Sync_Lab.md) with the take and interval intact. Structural gaps link to [Timeline](11_Timeline.md); missing annotations link to [Lyrics & Sync](04_Lyrics_and_Sync.md). Navigation retains the selected issue so the user can return after repair.

## V38 — Export configuration

Show the output scope and source snapshot before technical options. Required fields include the selected cut revision, full-song/excerpt interval, draft/final intent, filename, aspect ratio, resolution, frame-rate profile, and audio-source summary. Supported codec and quality controls depend on the chosen renderer. The source-song interval and expected output duration remain visible while settings change.

The default soundtrack is the original master mix mapped to the selected interval. Generated clip audio is excluded by default. Any deviation must be explicit and reflected in the review record. The view must not silently crop faces, stretch footage, loop a short take, or shift audio to satisfy an output profile. A changed aspect ratio that requires reframing routes back to the timeline; automatic alternative cuts are P1, not implied by an export dropdown.

**Primary action:** render the declared output. The final button names scope and intent, for example “Render final excerpt · 00:42–00:51.” **Secondary actions:** save settings, return to review, or request a portable project package. The request writes a bounded job with immutable input references. Cost/runtime is shown when available; an unavailable estimate remains explicit. Changing settings after submission does not alter the running job.

Final rendering is blocked by unresolved technical failures, incompatible settings, or missing applicable approvals. Draft renders expose unresolved issues and remain drafts. A render job's success means bytes were produced, not that the result is approved. Progress uses the existing jobs interface and opens V39 when a result is available. Repeated button presses must resolve to the same submission rather than create duplicate exports.

## V39 — Rendered delivery review and downloads

The main player loads the actual encoded asset, with its filename, checksum or stable asset identity, duration, output profile, and source snapshot shown nearby. Do not substitute the timeline preview for this player. A compact review strip links to the beginning, middle, end, and edited joins; users can add further marked intervals and step through frames where supported.

Checks include audio identity/offset, expected duration, retained clip timing, missing frames or media, unintended silence, and drift. The displayed audio source must be clear. Normal playback uses the encoded soundtrack; comparison with the intended master must not play both audios simultaneously or silently realign the candidate. Automated sync diagnostics remain P1 and cannot issue human approval.

**Primary actions:** approve this exact rendered delivery, reject with a finding, and download available files. **Secondary actions:** return to V37, inspect the export job, open source clips, and rerender after a correction. Approval writes a `Review`/`Approval` against the output `AssetRevision` and its source snapshot. Rerendering creates another output; it does not overwrite an approved file.

Offer the video, timing/subtitle exports, canonical performance map, and portable project/media package as applicable. Package contents have an explicit inventory of selected media and relationships. Credentials, secret endpoint parameters, and authentication material are excluded. The MVP package is a documented app format; professional-editor compatibility is P1 and must not be advertised before testing. Download failure retries transfer only and does not rerender media.

## Validation and AI behavior

The `Validate delivery` skill receives the cut snapshot, applicable reviews, media metadata, output profile, and declared scope. Deterministic checks establish missing media, duration, mapping, and approval validity. AI may explain findings and suggest repair destinations. It may not approve its own output, waive a technical blocker, or start a repair generation without a bounded request.

Final review should assess exact delivered timing. The proposed one-frame landmark goal in the PRD remains a benchmark target, not a “perfect sync” badge. Sustained sounds and natural anticipatory mouth movement require contextual judgment.

## State and recovery behavior

| State | Required presentation and recovery |
| --- | --- |
| Empty | Explain whether a cut, selected media, or render is missing; link directly to the relevant workspace. |
| Checking/rendering | Display the snapshot, progress if known, and a job link. Navigating away does not cancel or restart work. |
| Blocked | Identify concrete issues and disable final submission. Keep review, settings, and existing downloads accessible. |
| Stale | Show which input changed. Preserve historical findings and output; require checks against the new snapshot for current final approval. |
| Failed render | Retain settings, cost/history, and failure evidence. A retry is a new explicit bounded attempt. |
| Unknown job outcome | Reconcile the existing job through [Connections & Jobs](13_Connections_and_Jobs.md) before another submission. |
| Incomplete package | List missing items and do not label it complete; preserve already prepared output without rerendering the cut. |
| Playback/download failure | Distinguish delivery-transfer problems from media validity and provide a transfer retry. |

## Data and cross-area handoffs

Read `Cut`, `TimelineClip`, `TakeVersion`, `AssetRevision`, `AudioMasterRevision`, `PerformanceMapRevision`, `Dependency`, and applicable approvals. Write review records, output asset revisions, and export jobs.

A proposed `delivery` metadata record groups an output asset, cut snapshot, export profile, declared scope, review status, and package manifest. It is an implementation refinement of the PRD's delivery record, not a new production entity hierarchy. Job execution and cost records remain in `GenerationJob` or its implementation's shared job abstraction.

The package must preserve stable IDs and source-to-sequence relationships, including rational frame rates and integer source samples. Subtitle formats are derived exports; they must not replace the richer performance map or collapse simultaneous vocal events without an explicit format limitation.

## Acceptance criteria

| ID | Required behavior | PRD trace |
| --- | --- | --- |
| EXPORT-AC01 | A one-shot excerpt is reviewed and exported against its selected interval without requiring footage for the rest of the song. | AC-20, EXP-01 |
| EXPORT-AC02 | Final submission identifies missing approvals and dependencies; draft output stays visibly draft. | QC-01–02, AC-18 |
| EXPORT-AC03 | Review results remain bound to a cut snapshot; changed inputs prevent reuse as current final clearance. | AC-11, AC-18 |
| EXPORT-AC04 | Context-handle trims and frame-rate conversions preserve source-song timing in the encoded result. | AC-12, EXP-03 |
| EXPORT-AC05 | Delivery review plays the actual encoded asset and checks beginning, middle, end, and edited joins. | AC-18, EXP-03 |
| EXPORT-AC06 | A successful render is not automatically approved; approval pins the exact rendered revision. | SYN-07, QC-02 |
| EXPORT-AC07 | The project package includes selected media and structured timing relationships and excludes secrets. | AC-19, EXP-02 |
| EXPORT-AC08 | A download retry does not start a new render or provider charge. | AC-07, AC-10 |

## Accessibility and responsive behavior

Provide keyboard seeking, issue navigation, labeled frame controls, readable timecodes, and text descriptions of statuses. Do not encode severity only by color. On narrow screens, separate the issue list and player into focused panes while preserving selected time and issue context. Long technical profiles may collapse into labeled details; source identity, output scope, approval state, and blockers remain visible.

## Open design decisions

- Initial codecs, resolutions, rational frame rates, and renderer profiles require validation.
- Establish practical full-cut review controls and supported browser playback formats without confusing proxy playback with final-file validation.
- Define the portable package schema and subtitle handling for overlapping vocal lanes; external-editor interchange remains P1.
