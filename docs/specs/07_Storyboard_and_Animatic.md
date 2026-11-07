# Storyboard & Animatic — Area Specification

| Field | Value |
| --- | --- |
| Version | 0.1 |
| Status | Draft UX and functional specification |
| Date | November 6, 2026 |
| Views | V20–V22; 3 view types |
| Scope | P0 shot planning, reference frames, timed preview, and coverage |

[Master UI specification](00_UI_Master_Spec.md) · [Product requirements](Music_Video_Studio_PRD.md)

## Purpose and entry conditions

Make the intended video reviewable before moving footage is generated. This workspace organizes planned shots, connects them to exact song intervals, establishes visual references, and plays a timed still-frame preview against the master recording.

Entry context includes the whole-song performance map, creative brief, scene outline, and available cast/world references. All views remain accessible before approval, but AI shot planning, storyboard generation, and reference-frame generation require an approved current whole-song map. Existing boards remain inspectable when dependencies change. Text placeholders and imported source references allow incomplete imagery to remain explicit.

A storyboard is planning data. A `ShotRevision` is a continuous-camera intention. An imported or generated frame is an `AssetRevision`. Moving footage is a `Take`/`TakeVersion`, and a final placement is a `TimelineClip`. This area must preserve those distinctions.

Exit readiness means the selected shot plan has valid song intervals, intended performers and references, coverage gaps are understood, and the creator has reviewed the animatic. Approval of planning material does not approve a generated performance.

## View inventory

Routes are proposed. Overlays used for selection or confirmation remain states of these view types.

| ID | View | Presentation | Proposed deep link |
| --- | --- | --- | --- |
| V20 | Storyboard and shot coverage | Board/list with shared song strip and shot inspector | `/projects/:projectId/storyboard?scene=:sceneId` |
| V21 | Storyboard frame editor | Selected-frame workspace with reference and direction panes | `/projects/:projectId/storyboard/shots/:shotId/frame` |
| V22 | Timed animatic playback | Preview player with timed still sequence and coverage overlays | `/projects/:projectId/storyboard/animatic` |

## V20 — Storyboard and shot coverage

Display shot cards organized by intended song position, with scene grouping or filtering available. Each card shows a stable shot identifier, frame or text placeholder, shot type, intended interval, visible cast, vocal-performance requirement, reference status, and planning approval. Selecting a card reveals its framing, camera motion, blocking, performance intent, and dependency summary. The shared transport plays the master recording and highlights relevant lyric events.

Primary actions are **Add shot**, **Suggest coverage**, **Edit shot**, and **Open animatic**. Users can split a planned interval, duplicate a plan as an alternative, assign a scene, or select a frame. These actions create or revise plans; they never create video takes. **Produce this shot** navigates to [Shot Studio](09_Shot_Studio.md) with its exact revision, rather than immediately submitting a job.

The coverage strip distinguishes planned visual coverage from required visible-vocal coverage. Vocals playing during a narrative or B-roll shot do not automatically mean someone must lip sync. Where a shot intentionally shows a performer singing, its interval and vocal-lane binding must be explicit. Overlapping planned shots can represent alternatives. They are not automatically simultaneous placements in the final edit.

Intervals use the original recording's sample clock; displayed seconds and timecode are derived. Inputs outside the song range are rejected. Changing a card's interval must not stretch the song, duplicate a chorus occurrence, or move existing timeline clips. Dragging cards should clarify whether the action changes a presentation order or intended song coverage; numeric interval editing is always available.

Coverage suggestions show proposed shot additions and changes before application. A set of missing location or cast references remains a visible dependency list. Approving a shot plan freezes the reviewed creative inputs and exact reference versions for downstream production.

## V21 — Storyboard frame editor

The central canvas displays the selected storyboard image or a text placeholder at the intended output aspect ratio. A reference rail lists the selected cast identities, looks, location, and props. The direction pane contains composition, framing, pose/action, lighting, and the relationship of the still to the planned shot. A frame illustrates a camera intention; it does not demonstrate valid motion or lip sync.

Primary actions are **Import frame**, **Generate frame**, and **Select frame for shot**. Users may adjust composition instructions, attach approved references, inspect source versions, and compare candidates through existing asset tools. Advanced painting or compositing is outside this area. Generated candidates remain independent asset revisions; selecting one for the shot does not overwrite its predecessor.

Generation preflight is a state within this view, using the shared provider/cost component. It shows the exact selected reference versions, requested image count, connection, supported controls, and cost estimate or unknown cost. The whole-song approval gate is checked again at submission. A request for one frame cannot start video generation or generate frames for all shots. If a provider lacks a control, explain that limitation rather than offering a setting with no effect.

The editor reads `ShotRevision`, cast/world references, and project output settings. It writes candidate `AssetRevision` records, job provenance, and a selected-frame reference on a new shot revision. If the framing changes substantially, show which shot direction fields need review. Imported imagery records provenance and can still require visual approval before it becomes a production reference.

Returning to V20 preserves the selected shot and song cursor. **Open asset details** navigates to [Assets](08_Assets.md); **Revise cast/look/location** opens the owning [Cast & World](06_Cast_and_World.md) view and later returns to this shot.

## V22 — Timed animatic playback

Play the selected storyboard sequence against the untouched master recording. The player shows each chosen still or placeholder for its planned interval. The lower strip displays shot boundaries, scene labels, lyric/performance overlays, gaps, and alternative coverage. Users can loop a phrase or section, jump between cuts, and inspect the current shot while listening at normal speed.

The primary actions are **Play animatic**, **Review plan**, and **Continue to shot production**. Secondary actions change selected alternatives, adjust planned boundaries, replace a frame through V21, and open the current shot in V20. The animatic must retain master-song time when moving among these views. A projected shot count or duration estimate is informational and cannot authorize generation.

If several shots cover the same interval, the animatic needs an explicit selected planning sequence. Store that arrangement as a versioned planning selection linked to shot revisions; this is a proposed supporting structure, distinct from a final `Cut` using generated media. A coverage gap displays a deliberate placeholder or clearly marked empty interval. Playback must not silently shorten the song to hide missing imagery.

Review checks include visual pacing, scene recurrence, performance/story balance, cut boundaries, required cast appearances, and outstanding references. An automatic suggestion may identify an awkward boundary but cannot certify natural lip sync from stills. Beat-based suggestions are P1; basic manual timing and playback are P0.

Accepting the animatic records the reviewed plan and references. Editing a participating shot afterward marks only affected planning dependencies stale. The existing review remains in history. A full-song animatic does not commit the creator to generating every shot; the next production action can target one selected shot or a representative audition through the ordinary take workflow.

## AI and handoff contracts

| Action | Inputs | Proposed output and boundary |
| --- | --- | --- |
| Plan coverage | Approved map, chosen brief/scenes, cast/world state | Shot additions or edits with interval and performance bindings; no provider video requests |
| Generate frame | One selected shot and explicit references | Bounded candidate image assets and job records; no automatic approval |
| Build animatic | Chosen shot revisions, frame selections, master | Timed planning preview with missing coverage preserved |

Shared V45 shows inputs, versions, and validation results. Any AI action that changes an approved shot plan presents the affected fields. Existing takes remain linked to the earlier plan revision until users choose an explicit update workflow.

Shot Studio consumes the selected shot revision, frame, voice intent, and approved references. Lip-Sync Lab receives the actual visual take later and resolves target faces; a storyboard face label cannot replace face selection on generated footage. [Timeline](11_Timeline.md) owns the final edit. An eventual “assemble rough cut” action must make its proposed placements reviewable rather than overwriting the current cut.

## State and recovery behavior

| State | Expected behavior |
| --- | --- |
| Empty board | Offer manual shot creation or eligible coverage suggestion and display the song's uncovered intervals. |
| Loading media | Keep shot labels, timings, and placeholders available while frames load. |
| Missing map approval | Open all views with a precise blocker and a link to [Lyrics & Sync](04_Lyrics_and_Sync.md). |
| Missing reference/frame | Retain text intent and display the missing dependency; never substitute an unrelated image. |
| Image job failure/unknown | Preserve previous candidates; delegate job reconciliation to Connections & Jobs before a chargeable retry. |
| Stale plan | Highlight affected shots or references and link to shared impact review. |
| Playback failure | Preserve selected position and planning data; offer recovery without launching generation. |
| Competing coverage | Require an explicit animatic selection while retaining alternatives. |

## Acceptance criteria

| ID | Behavioral criterion | PRD trace |
| --- | --- | --- |
| BOARD-AC01 | The board opens before map approval, but all AI planning/frame-generation entry points remain gated. | MAP-09; AC-05 |
| BOARD-AC02 | Each shot retains exact song coverage, intended cast, framing, and explicit lip-sync requirements. | BRD-01 |
| BOARD-AC03 | Narrative coverage during vocals can remain non-lip-sync coverage without inventing a visible performer. | BRD-01; CAST-04 |
| BOARD-AC04 | Gaps and alternative coverage remain visible; the animatic preserves complete master duration. | BRD-03; BRD-04 |
| BOARD-AC05 | A one-frame request submits only its displayed image scope and preserves existing candidates. | BRD-02; AC-07 |
| BOARD-AC06 | Changing a planned interval does not move final timeline clips or alter the master recording. | BRD-01; EDT-01; AC-12 |
| BOARD-AC07 | Reusing a scene across choruses retains distinct song occurrences and one underlying scene identity. | SCN-02; MAP-04; AC-04 |
| BOARD-AC08 | Animatic approval identifies exact shot/frame revisions and does not approve generated performance or start a batch. | SYN-07; SHT-01; AC-15 |
| BOARD-AC09 | A changed reference lists affected shot plans while preserving earlier takes and approvals. | AST-02; AC-11 |
| BOARD-AC10 | Wizard and direct navigation preserve the selected shot, cursor, and shared project records. | AC-06 |

## Accessibility and responsive behavior

Provide a list view for the board, keyboard ordering and interval entry, and text equivalents for coverage colors. Frame references have meaningful labels. Playback controls expose time, duration, and current shot accessibly. On narrow screens, use focused board, inspector, and player panels; keep the selected shot and song position stable. Dense coverage remains available through a linear interval list instead of being hidden.

## Open design decisions

- Finalize the stored representation of alternative planning sequences before implementing V22.
- Choose default storyboard density and ordering behavior during wireframing; actions must clearly distinguish presentation order from song-time changes.
- Determine which frame-generation adapters and controls are available initially. Capability disclosure and bounded submissions are required regardless of provider.
