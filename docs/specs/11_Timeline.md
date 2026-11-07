# Timeline — Area Specification

| Field | Value |
| --- | --- |
| Version | 0.1 |
| Status | Draft for UX and engineering review |
| Date | November 6, 2026 |
| View IDs | V34–V36; three view types |
| Governing documents | [Master UI specification](00_UI_Master_Spec.md); [Product requirements](Music_Video_Studio_PRD.md) |

## Purpose and scope

Timeline assembles exact take versions into an intentional edit while preserving their relationship to the master song. It supports non-destructive placement, trims, basic transitions, alternate cuts, and contextual preview. It must make a performance-sync violation visible before an apparently simple edit creates drift.

The area is a focused music-video editor. P0 includes a usable timeline, exact media-version references, clip inspection, alternate cuts, and links to review/export. It does not include full compositing, advanced audio mixing, or general film dialogue editing. Tested external-editor interchange and independently reviewed alternate-aspect-ratio cuts remain P1.

## Entry, dependencies, and completion

Enter from the project dashboard, storyboard, selected take, lip-sync review, or an export issue. Open a selected `Cut` and preserve its sequence revision, playback position, zoom, and selected clips. Empty or historically stale cuts remain accessible.

New AI rough-cut assembly requires current whole-song map approval. New production assembly follows the same project gate, while users can inspect existing cuts and source media at any time. Draft edits may contain unapproved candidates, clearly marked for review; they cannot become an approved final delivery until required reviews and dependencies are current.

Completion means a cut has the intended visual coverage, explicit source-song bindings, technically valid placements, and resolved required editorial issues. The actual encoded file is reviewed in [Review & Export](12_Review_and_Export.md), so a successful timeline preview does not itself approve delivery.

## View inventory

Routes are proposed deep links. The clip inspector is a counted view even when displayed within V34.

| ID | View | Presentation | Proposed route |
| --- | --- | --- | --- |
| V34 | Editing timeline | Main multi-track editor and preview | `/projects/:projectId/cuts/:cutId/timeline` |
| V35 | Clip and transition inspector | Contextual inspector with focused mode | `/projects/:projectId/cuts/:cutId/timeline?clip=:clipId` |
| V36 | Alternate cuts and comparison | Cut inventory with A/B or side-by-side review | `/projects/:projectId/cuts/compare` |

## V34 — Editing timeline

The top preview shows the selected cut against the original song. Below it, a time ruler and visual tracks align with the locked master-music track. Optional overlays show lyric events, song sections, intended shot coverage, and unresolved review spans. A source rail provides available take versions and storyboard placeholders, with review and dependency badges. The transport displays sequence position and allows source-song inspection when an excerpt uses a nonzero origin.

The primary interaction places or trims a selected media version. Secondary actions split, move eligible clips, add a supported transition, undo, compare another cut, open the selected source, and request a proposed rough assembly. Selecting a lyric or scene can highlight related clips without rewriting the edit. Placeholders are permitted in drafts and count as unresolved final coverage.

Read the `Cut`, `TimelineClip` records, master revision, maps, media versions, mappings, and approvals. Save non-destructive sequence revisions; media files remain unchanged. A clip references the exact `TakeVersion` or `AssetRevision`, never whichever take becomes preferred later. Generated audio is excluded or muted by default; the original mix supplies the authoritative music.

Moving a performance clip must preserve or deliberately re-establish its valid song relationship. If a drag would place sung words over different song time, show the proposed mismatch before committing and block an unresolved operation. For ordinary narrative footage, placement can change without inventing a vocal binding. Ripple operations must list or preview affected performance clips and stop if their timing constraints cannot be preserved. No automatic looping or speed change is allowed to make a clip fit.

The source-song origin of an excerpt is explicit. A preview beginning at sequence zero can represent song time 00:40; the editor must display and retain that mapping. Selecting **Export excerpt** carries the mapping into the existing export view rather than creating another timeline type.

## V35 — Clip and transition inspector

The inspector shows clip ID, source take/version, source in/out, sequence in/out, source-song range, usable bounds, native frame rate, cut frame rate, and required review status. Present friendly duration/timecode controls first, with precise frame/sample details available for inspection. Group visual adjustments and transitions separately from timing relationships so a style change cannot obscure a sync change.

Primary actions adjust a trim or supported transition and apply a validated edit. Secondary actions revert, select another exact source version, open take review, inspect the governing lyric interval, and open dependency impact. Numeric controls provide an alternative to dragging. All changes produce an edit preview before an expensive render; a local trim does not submit a new generation job.

Trims must stay within available usable media or explicitly reviewed handles. A source-media trim changes the song interval represented by a performance clip; its sequence placement must preserve the corresponding mapping. The UI should make linked adjustments visible, including which boundary moved and why. It must not silently freeze the last frame, duplicate frames to fill a gap, or alter playback speed.

Transition controls identify both participating clips, overlap range, and available handles. Validate that both media sources cover the overlap and that any visible singing remains synchronized with the same underlying song interval. A dissolve may be technically possible while making two performers visible; required review should include that overlap. Unsupported transitions stay unavailable with a clear explanation.

An explicit replacement action lists affected clip instances and chooses a candidate version. It validates coverage, timing, and review differences before changing the sequence. An inadequate replacement can be used as a clearly marked draft only where the app can establish a valid placement; unknown sync is never presented as resolved. This inspector writes `TimelineClip` transformations and sequence revisions, not source footage.

## V36 — Alternate cuts and comparison

List named cuts with source-master revision, output profile, duration, last edit, coverage, and approval/dependency indicators. Users can duplicate a cut, rename it, select the active cut, or compare two revisions/cuts without copying immutable media. Duplicating a cut preserves references and provenance while giving subsequent editorial changes a separate sequence identity.

Comparison uses linked source-song playback where possible. For cuts covering different excerpts, show the common interval and unavailable regions rather than pretending sequence zero denotes the same song moment. Side-by-side preview is optional; a single-player A/B switch offers the same comparison with less screen space. A differences inspector summarizes placement, trims, source versions, transitions, and uncovered or newly visible performance intervals.

The primary action selects the cut to continue editing or review for delivery. Secondary actions open V34 at a changed interval, inspect a source difference in V35, or create an alternate arrangement. Choosing the active cut does not approve it and must not overwrite a previously delivered version. Exact cut and sequence revision remain available in delivery history.

Read `Cut` and sequence revisions plus shared media. Write cut metadata, duplicates, active-selection state, and explicit editorial changes. Advanced alternate-aspect-ratio workflows remain P1: a new output profile can require reframing and renewed visual review, not just a changed label. Cut comparisons never submit generation or export jobs automatically.

## Time mapping and editorial validation

Keep three clocks explicit: master song samples, media-local time/frames, and sequence time. Store frame rates as rational values where required, such as 30000/1001; a rounded display of 29.97 must not become the calculation basis. Define rounding at rendering/presentation boundaries and avoid accumulating per-clip rounded durations.

For example, footage may include half a second of context before a shot's intended song start. Placing the usable portion requires the corresponding source in-point, not media frame zero. Trimming another quarter second must advance its represented song position by the same amount unless the footage is intentionally non-performance material. These mappings must survive export, reload, and alternate-cut duplication.

Frame-rate conversion is an explicit rendering transform. It must preserve playback duration and the source-song relationship, and disclose when a conversion affects available frame boundaries. This does not authorize silent performance retiming. Actual encoded timing is verified in the delivery workflow.

Coverage checks distinguish planned coverage, placed footage, technical coverage, and reviewed performance coverage. A gap in a draft is visible and recoverable. A screenshot placeholder or stale approval must not count as finished footage for final delivery.

## Skills and bounded actions

The rough-cut skill consumes an approved map, shot plan, available exact take versions, desired coverage, and cut constraints. It produces a proposed sequence with reasons for source selection and unresolved gaps. Applying it creates a new editable sequence revision. It cannot purchase missing footage or approve candidate media.

The user may ask the assistant to tighten pacing or compare alternatives. Show changed placements and any timing conflicts before applying the proposal. Deterministic validators enforce song relationships, usable bounds, and required approvals. Shared proposal and execution detail use V45 in [Shared Tools](14_Shared_Tools.md).

## States and recovery

| State | Behavior |
| --- | --- |
| Empty cut | Show the master song and eligible sources; offer manual placement or a gated rough-cut proposal. |
| Missing source media | Keep clip identity and timing visible; show relink/import recovery without replacing it arbitrarily. |
| Loading proxies | Preserve the edit and transport position; distinguish unavailable preview from missing original media. |
| Unapproved candidate | Allow marked draft review; include the missing approval in final-delivery blockers. |
| Sync-breaking edit | Explain affected clips and intervals; offer a valid adjustment or cancel the operation. |
| Insufficient handles | Restrict trim/transition extent and link to source review or new production work. |
| Stale dependency | Preserve the old cut; show exact affected clips and source changes. |
| Interrupted session | Restore persisted edits; separate unsaved local state from confirmed saves and avoid duplicate actions. |
| Invalid output profile | Keep the cut editable and explain the unsupported rendering configuration. |

## Data and cross-area handoffs

`Cut` holds output settings and its master-audio binding. `TimelineClip` holds exact source-version references, source/sequence ranges, track, and explicit transformations. Reviews identify whether the visible interval is covered; a review of unused handles is not assumed. Transition definitions and sequence revision records are proposed implementation refinements of EDT-01 and EDT-04.

Shot Studio supplies preferred candidates but cannot silently change existing placements. Lip-Sync Lab supplies reviewed versions and mappings. Storyboard supplies intended coverage and scene context. Review & Export receives a pinned sequence revision, output settings, dependency state, and a deterministic media plan. Edit history and impact assessment use V46/V47 rather than another timeline-specific history system.

## Acceptance criteria

| ID | Behavior | PRD traceability |
| --- | --- | --- |
| TIME-AC01 | A timeline clip references an exact media version and retains independent source and sequence ranges. | EDT-01; §9.1 |
| TIME-AC02 | The original mix remains authoritative and generated audio does not replace it implicitly. | EDT-02; AC-01 |
| TIME-AC03 | Context handles and subsequent trims retain correct song placement through reload and export. | EDT-03; AC-12 |
| TIME-AC04 | A move or ripple that breaks visible-performance sync is explained and blocked until a valid relationship is established. | EDT-03; AC-13 |
| TIME-AC05 | Insufficient media cannot silently cause looping, freezing, or speed adjustment. | EDT-05; AC-14 |
| TIME-AC06 | Mixed/rational frame rates do not accumulate timing drift through rounded clip-duration arithmetic. | §10; AC-18 |
| TIME-AC07 | A preferred-take change leaves existing placements untouched until a scoped replacement is accepted. | EDT-01; §13 |
| TIME-AC08 | Rough-cut generation produces a reviewable sequence and submits no missing-shot jobs. | §11.2–11.3; AC-07 |
| TIME-AC09 | Alternate cuts preserve shared immutable media and independent editorial history. | EDT-04; §9.1 |
| TIME-AC10 | Draft placeholders or stale approvals remain explicit final-delivery blockers. | QC-01, QC-02; AC-18 |
| TIME-AC11 | A single completed shot can form an excerpt cut with an explicit song origin and proceed to export. | AC-20; EXP-01 |

## Accessibility and responsive behavior

Provide keyboard and numeric alternatives for trim, move, split, and selection. Announce selected clip, range, source, and timing conflicts. Keep track labels, review badges, and markers distinguishable beyond color. Shortcuts must respect focused text fields, and undo must describe the operation it reverses.

Desktop supports the full multitrack editor. On narrower screens, provide focused clip inspection, a horizontally navigable timeline, and single-player cut comparison. Preserve source/sequence distinctions and warnings across layouts. Essential timing controls must not disappear merely because a pointer-based interaction is unavailable.

## Open design decisions

- Define the initial transition set and supported output frame-rate profiles through media validation.
- Choose the default snapping behavior; lyric/beat snapping must remain optional and must not change authoritative timing.
- Determine proxy generation limits and large-project interaction targets during implementation benchmarking.

