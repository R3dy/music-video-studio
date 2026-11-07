# Lyrics & Sync — Area Specification

| Field | Value |
| --- | --- |
| Version | 0.1 |
| Status | Draft for product and engineering review |
| Date | November 6, 2026 |
| Area | Lyrics & Sync |
| Assigned views | V08–V12; 5 distinct view types |
| Parent documents | [Master UI specification](00_UI_Master_Spec.md), [Product requirements](Music_Video_Studio_PRD.md) |

## Purpose and scope

Build and approve the Vocal Performance Map: an editable representation of what the recording contains, which voice performs each event, and when it occurs. Preserve the supplied lyrics as evidence while maintaining a separate reconciled transcript of the actual performance.

All MAP-01 through MAP-10 requirements are P0. Comparing multiple alignment-engine proposals is P1 under MAP-11. V11 supports the P0 unresolved-items workflow even when the P1 comparison function is unavailable.

The approved map supports planning, audio cropping, performer assignment, and evaluation. It does not certify generated mouth motion or imply that a video provider accepts phoneme timestamps as controls. Rendered-performance review belongs in [Lip-Sync Lab](10_Lip_Sync_Lab.md).

## Entry, dependencies, and completion

The area remains accessible without source media, displaying missing inputs. Transcription and alignment execution require a validated selected `AudioMasterRevision`; individual operations also require their stated transcript, vocal-lane, language, or source prerequisites. Users may paste supplied lyrics before audio preparation completes.

Whole-song approval requires the current map to reference the current master, blocking discrepancies and assignments to be resolved, required review coverage to be complete, and the user to explicitly approve that exact revision. Section approvals help organize review but do not unlock creative production independently. All workspaces remain navigable while treatment, storyboard, and production execution remain gated.

## View inventory

Routes are proposed deep links. A selected event, region, or lane can be carried in query parameters without creating a new view type.

| ID | View | Presentation | Proposed route |
| --- | --- | --- | --- |
| V08 | Transcript reconciliation | Main tab with comparison editor | `/projects/:projectId/lyrics/transcript` |
| V09 | Vocal-lane assignment | Main tab with lane editor | `/projects/:projectId/lyrics/voices` |
| V10 | Word and phoneme timing | Focused waveform/spectrogram editor | `/projects/:projectId/lyrics/timing` |
| V11 | Unresolved passages and alignment comparison | Review queue with selected-region editor | `/projects/:projectId/lyrics/review` |
| V12 | Map approval and export | Review summary and output panel | `/projects/:projectId/lyrics/approval` |

## V08 — Transcript reconciliation

Show the supplied lyric revision alongside the performed transcript, grouped by song section and phrase. An audio strip and shared transport keep the selected passage audible. Differences include omitted words, extra words, repetitions, ad-libs, nonverbal events, and sung pronunciation. Machine suggestions carry their origin and unresolved state; supplied wording is never silently replaced.

Primary actions accept or edit a proposed performed phrase, add a missing event, remove an incorrectly inferred event, and mark a discrepancy resolved. Secondary actions audition context, search text, restore a prior proposal, and open the selected phrase in V09 or V10. Editing the performed transcript changes only that transcript/map draft. Editing the original supplied text requires a distinct input revision.

Repeated choruses receive separate occurrence IDs even if users copy their text. Copying words may accelerate entry, but timing and review are not inherited as approved. A sung “oh,” laugh, or breath can be represented as the appropriate event rather than forced into ordinary lyric spelling. Unintelligible material remains explicitly unresolved until a reviewer determines its treatment; the AI must not invent text to close the issue.

Read the supplied transcript, performed transcript revision, source master, section markers, and associated events. Save reconciled text and provenance. Structural edits such as splitting or merging phrases preserve traceable event identity where possible and flag affected timing for review. A spelling-only change that preserves pronunciation and timing must not automatically require video regeneration.

## V09 — Vocal-lane assignment

Display the recording against separate rows for independent vocal performances. A lane identifies a recorded voice label and role, such as lead singer, rapper, harmony, double, or ad-lib. A lane can reference an appropriate stem while retaining access to the full mix. Voice labels and role labels are distinct from the visible cast members that will portray them later.

The primary task is assigning phrases/events to lanes. Users create or rename lanes, move selected events, audition a lane, and correct model assignments. At least two independently timed lanes must be fully editable in the MVP. A held lead vowel and a rapper's overlapping response remain simultaneously visible and selectable; moving one event must not shift the other to make a flat sequence.

An unknown voice stays unassigned with a visible issue. Diarization output is a proposal, not an identity claim. A mixed “vocals” stem does not prove that its contents belong to one person, and multiple lanes need not imply that clean separate stems exist.

Read/write `VocalLane` and event-lane assignments. Selecting a stem records a dependency on its revision and timing mapping. Changes to an established voice assignment open impact review when production consumers exist. The later `PerformanceBinding` between a lane and a `CastMember` belongs to Cast & World/Lip-Sync Lab; V09 can display that link without conflating the two identities.

## V10 — Word and phoneme timing

The central editor combines an overview waveform, a detailed waveform/spectrogram, and synchronized lanes for phrases, words, syllables, phonemes, and nonverbal events. The user can select a parent event and expand its children in place. A compact inspector exposes text, pronunciation, start/end positions, source, confidence when supplied, uncertainty, and review status. Confidence and manual approval must remain visually distinct.

Primary actions adjust event boundaries, split/merge events when appropriate, edit pronunciation, and review the selected passage. Provide precise numeric entry, draggable boundaries, keyboard nudging, phrase looping, normal-speed playback, and slowed audition. The inspector identifies whether its units are seconds, samples, or derived frames. Display precision does not imply measured accuracy.

Save canonical positions as integer master samples using `[start, end)`. Validate positive event duration, recording bounds, and appropriate parent containment. Do not derive every phoneme boundary by evenly dividing a word; missing phone timing remains a proposal or unresolved annotation. A word may contain a held vowel across multiple notes without generating extra phonemes merely because pitch changes. Relevant gaps and pauses must remain representable.

Cross-lane overlap is expected. Within a lane, distinguish intentional acoustic transition/annotation uncertainty from accidental duplicated or misordered events; flag questionable structure for review rather than applying a universal rule that erases expressive performance. Uncertainty intervals may overlap even where the chosen nominal boundaries are ordered.

Edits are applied as bounded annotation transactions with undo and autosave. New durable revisions preserve previous approvals; they do not mutate the reviewed record. When downstream work exists, save the edit with a clear affected-region summary through V47. Approval never causes an automatic lip-sync render.

## V11 — Unresolved passages and alignment comparison

Present a queue grouped by issue type and song section: missing/extra text, unknown lane, questionable pronunciation, unreviewed timing, invalid structure, uncertain stem mapping, or stale input. Each item shows its song range, relevant lane, reason, severity, and the action that can resolve it. Selecting an item opens the region in a focused editor with surrounding audio context; the user can move through the queue without losing playback position.

Primary actions open the appropriate editor, resolve the issue with a recorded correction, or record a reviewer judgment about an ambiguous boundary. “Reviewed with uncertainty” preserves the uncertainty and rationale; it is not a blanket bypass for missing lyrics, invalid time ranges, or unknown vocalist assignments. Issue severity and the permitted resolution depend on the issue class. Empty queues must not be treated as equivalent to whole-map approval.

In P1, display alternative alignment proposals as distinguishable overlays or columns against the same source, transcript, and lane. Users can select one proposal for a bounded region or manually reconcile differences. Do not silently average proposals or overwrite unrelated intervals. When input revisions differ, explain that the comparison is not directly equivalent.

An optional re-alignment request states the selected region, input revisions, model/connection, estimated cost or unknown cost, and replacement scope. Applying its output is a separate annotation action. Job failure or uncertainty preserves current corrections and cannot initiate an unseen paid retry. Read/write issue and review records as supporting data associated with `PerformanceMapRevision`; exact storage schema remains a technical design refinement.

## V12 — Map approval and export

Show the master recording revision, map revision, transcript revision, voice lanes, reviewed sections, unresolved blockers, and known uncertainties. Coverage should account for vocal performance and explicitly identified instrumental/silent regions, without fabricating words during nonvocal passages. A full-song review transport makes the final check possible in this view.

The primary action is “Approve performance map.” Enable it only after server-side validation confirms current inputs, resolved blocking issues, and required review completion. The application, API, and skills must use the same approval predicate. Section-level approvals remain visible progress records; they cannot substitute for whole-song approval. The approval records reviewer, exact target revision, and time.

After approval, offer “Continue to brief” linking to [Brief & Outline](05_Brief_and_Outline.md). Reopening the map for changes creates a draft revision and shows the effect on dependent work; the previously approved revision remains auditable. A new source recording never inherits the old approval.

The export panel offers the canonical structured map and compatible line/word timing files. Draft exports are explicitly labeled as drafts. For formats unable to express overlapping voices, phonemes, or review metadata, explain the omitted information and offer lane-specific files or the structured format. Export must not silently flatten a duet or label a lossy LRC as a complete performance map. Exact dialect options and multi-lane text-export conventions remain implementation decisions.

## AI and skill behavior

“Reconcile lyrics” reads the source recording and supplied lyrics and produces a performed-transcript proposal plus discrepancies. “Align performance” reads the reconciled transcript, lanes, pronunciation context, chosen audio sources, and their exact revisions; it produces proposed events and uncertainty/issues. Neither skill may mark its output human-approved.

The application performs deterministic interval validation and enforces editing and execution gates. Skills can propose corrections and prepare bounded alignment requests. They cannot convert a model score into an approval, remove inconvenient overlapping material, assume identical timing for repeated text, or authorize unrelated production work. Input snapshots, proposed patches, and job outcomes are inspectable in shared V45.

## State and recovery behavior

| State or condition | Required behavior |
| --- | --- |
| Missing audio | Allow lyric entry and inspection; explain that alignment awaits a validated recording. |
| Missing supplied lyrics | Offer text entry/import; any transcription is a visible proposal rather than silently authoritative input. |
| Loading or analysis running | Preserve editor navigation and saved corrections; show proposal/job progress separately. |
| Alignment failure | Keep transcript, lane assignments, and existing timings; offer manual correction and bounded retry. |
| Unsupported language/phone model | Explain the unavailable capability; allow manual editing and connection changes without inventing timings. |
| Unknown provider outcome | Reconcile the submitted job; do not resubmit automatically. |
| Blocking issue | Link directly to the event and reason; keep whole-map approval disabled. |
| Ambiguous acoustic boundary | Retain uncertainty and reviewer rationale; do not display false precision as certainty. |
| Stale input | Identify the changed recording, stem, transcript, or lane dependency and affected intervals. |
| Unsaved/conflicting revision | Preserve local edit intent and compare against current state; do not silently overwrite another durable revision. |
| Approved | Show the exact approved revision and keep editing available through a new draft. |

## Data contract and cross-area handoffs

The canonical records are `AudioMasterRevision`, `DerivedAudio`, `VocalLane`, `PerformanceMapRevision`, and `VocalEvent`, with `Review`/`Approval` and dependencies. Supplied/performed transcript records, issue records, and pronunciation overrides are supporting schema refinements, not substitutes for the canonical map.

Each occurrence retains identity through navigation. Event selection passes its ID, lane, and song interval to storyboard coverage, shot planning, and the Lip-Sync Lab. Downstream processes pin exact input revisions and retain explicit audio mappings. Changing annotations produces selective impact analysis rather than invalidating every consumer solely because the map revision number changed.

The final map is a planning and evaluation reference. Mouth animation still requires an appropriate provider and independent rendered review. A correct map never automatically approves a `TakeVersion`.

## Acceptance criteria

| ID | Required behavior | PRD traceability |
| --- | --- | --- |
| LYR-AC01 | Supplied lyrics remain preserved when the performed transcript is corrected. | MAP-01; AC-02 |
| LYR-AC02 | Repeated choruses retain separate occurrence IDs and independently editable timings. | MAP-04; AC-04 |
| LYR-AC03 | A held vocal and overlapping response remain simultaneous events in independent lanes. | MAP-03; AC-03 |
| LYR-AC04 | Names, acronyms, and sung pronunciation can be corrected without silently replacing the source lyrics. | MAP-05; MAP-01 |
| LYR-AC05 | Numeric, keyboard, and drag edits produce validated master-sample boundaries and preserve undo history. | MAP-06; PRD §10 |
| LYR-AC06 | Missing phone timing is distinguishable from reviewed timing; displayed decimals do not create approval. | MAP-02, MAP-07 |
| LYR-AC07 | Unresolved omitted words, invalid intervals, or unknown voice assignments block whole-map approval. | MAP-08, MAP-09; AC-02 |
| LYR-AC08 | Section approval and model confidence cannot unlock creative production without current whole-song approval. | MAP-09; AC-05, AC-15 |
| LYR-AC09 | Opening another workspace remains possible before approval; UI/API/skill execution enforce the same gate. | AC-05, AC-06 |
| LYR-AC10 | A timing edit preserves previous revisions and identifies affected consumers without paid regeneration. | AC-11; PRD §13 |
| LYR-AC11 | Draft and lossy text exports are labeled; the structured export preserves lane overlap and event hierarchy. | MAP-10; EXP-02 |
| LYR-AC12 | The unresolved queue works in MVP without multiple-engine comparison. | MAP-08 P0; MAP-11 P1 |
| LYR-AC13 | An approved map cannot independently mark generated footage approved. | SYN-07; AC-15 |

## Accessibility and responsive behavior

Provide keyboard traversal among lanes, events, boundaries, and queue items with an announced selection and current time. Numeric boundary fields and textual issue descriptions accompany graphical interaction. Voice identity, confidence, review status, and uncertainty must not rely only on color. Pronunciation fields need plain-language labels and an explanation of the accepted notation.

Desktop presents the full multitrack editor. Narrow screens use a selected-lane focus with a persistent indication of overlapping events in other lanes; the user can switch or expand them without losing shared time. Spectrogram, transcript, and inspector may become selectable panes, while loop controls and exact numeric editing remain available.

## Open design decisions

- Initial alignment languages, pronunciation notation, and provider selection.
- Review-coverage policy, issue-severity taxonomy, and rules for accepting genuinely ambiguous boundaries with recorded uncertainty.
- Default nudge increments and visual density at word versus phoneme zoom levels.
- Supported LRC/word-timing dialects and lane-specific export packaging.
- Benchmark-derived alignment targets; this area must not promise arbitrary-song perfection before measurement.
