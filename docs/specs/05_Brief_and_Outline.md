# Brief & Outline — Area Specification

| Field | Value |
| --- | --- |
| Version | 0.1 |
| Status | Draft UX and functional specification |
| Date | November 6, 2026 |
| Views | V13–V14; 2 view types |
| Scope | P0 creative direction and scene planning |

[Master UI specification](00_UI_Master_Spec.md) · [Product requirements](Music_Video_Studio_PRD.md)

## Purpose and boundaries

Translate the approved recorded performance into a coherent creative direction and scene outline. This area establishes what the video means, how it should feel, and which situations or settings will communicate that intent. Shot framing and timed storyboard coverage continue in [Storyboard & Animatic](07_Storyboard_and_Animatic.md).

The area reads the current approved `PerformanceMapRevision`, its source recording, song sections, and the creator's references. It writes `CreativeBriefRevision` and `Scene` records plus their review and dependency records. It never edits lyric timing, rewrites the song, or starts video generation.

The workspace is always navigable. Before whole-song map approval, it displays prerequisites and permits inspection of existing material and capture of source notes. AI treatment or outline generation is blocked through every execution path. Existing briefs remain readable when a later map revision is unapproved. Their historical approval and current dependency validity are shown separately.

Exit readiness means a chosen brief revision and usable scene outline exist, with unresolved decisions identified. Approval of this material applies to those exact revisions; it does not certify cast references, a storyboard, or footage.

## View inventory

Routes below are proposed deep links, not implemented endpoints.

| ID | View | Presentation | Proposed deep link |
| --- | --- | --- | --- |
| V13 | Creative treatment and visual direction | Workspace editor with reference rail and contextual assistant | `/projects/:projectId/direction/brief` |
| V14 | Scene outline and scene details | Ordered outline with a selected-scene inspector | `/projects/:projectId/direction/scenes?scene=:sceneId` |

## V13 — Creative treatment and visual direction

The center pane contains structured fields for the concept, audience, narrative premise, mood, visual style, intended format, performance/story balance, and creative constraints. A short treatment text connects those fields into a readable direction. The left rail presents song sections and relevant lyric excerpts with playback links. The right rail shows reference assets and a compact revision/approval summary. The shared assistant opens in its standard panel rather than a separate area-specific chat.

The primary action is **Develop treatment** when no proposal exists, then **Review changes** or **Approve brief** as appropriate. Users can edit fields directly, attach existing assets, import source references, play a section, and request a revision limited to a selected field or passage. Each AI request displays its scope and any known inference cost. “Revise visual style” must not silently replace the narrative, scene outline, or other approved fields.

Saving a draft writes a new creative revision or draft state under the project's revision policy. Approving freezes the reviewed input version. Applying an AI proposal presents field-level changes; the user can accept a subset. An incomplete optional field remains visibly unspecified rather than being populated with an invented preference. Output format should reference the project's selected profile, with discrepancies surfaced before downstream use.

The editor reads the approved performance map but does not treat literal lyric depiction as mandatory. A narrative may use metaphor or unrelated visuals while still preserving the timing of performance coverage. A displayed ratio or balance is creative guidance, not an automatic permission to replace timing or generate a calculated number of clips.

From this view, **Plan scenes** opens V14 with the accepted brief revision. **Define cast** opens [Cast & World](06_Cast_and_World.md). Clicking a cited lyric returns to the exact occurrence in [Lyrics & Sync](04_Lyrics_and_Sync.md), preserving the song cursor.

## V14 — Scene outline and scene details

Display an outline list with scene names, narrative purpose, setting, cast summary, linked song sections, and readiness status. Selecting a row opens its detail inspector within this view. The inspector contains location, action, emotional progression, lighting intent, cast participation, wardrobe/prop references, and continuity notes. A compact song strip displays where each scene is intended to recur.

Primary actions are **Add scene**, **Suggest outline**, **Save scene**, and **Approve outline**. Secondary actions include duplicate as a new scene, reorder the planning list, link an existing scene to another song interval, detach an interval, and open the scene's storyboard coverage. The UI distinguishes an outline ordering from the final edit; moving an outline row does not move existing timeline clips.

A rooftop scene can be linked to three chorus intervals without creating three unrelated locations or character states. Those links preserve separate lyric occurrences. An interval that exceeds the recording is invalid. Overlapping scene coverage is allowed because alternative visual coverage may be intentional. Contradictory assignments, such as two looks intended simultaneously for the same performance, are flagged for review rather than silently resolved.

Changes write the selected scene revision and dependency links. Referenced cast, locations, and props are selected by ID and revision, with an explicit unresolved state for assets not yet defined. A textual mention is not sufficient evidence that a corresponding asset exists. Deleting a scene with downstream shots shows the impact in shared V47; it cannot silently remove media or timeline placements.

**Open storyboard** carries the scene and its intended song intervals into V20. It does not generate frames. **Edit location** and **Edit cast** navigate to their owning views and return with the selected scene context intact.

## Skills and approval behavior

| Skill action | Required context | Bounded output |
| --- | --- | --- |
| Develop treatment | Approved whole-song map, recording/section references, user preferences | A proposed brief revision and explicit assumptions |
| Revise direction | Selected brief revision and requested fields | A field-scoped patch, with unchanged fields preserved |
| Suggest scene outline | Chosen brief, approved map, available cast/world references | Proposed scenes and interval links; unresolved assets stay unresolved |

Skill runs are inspectable in shared V45. The server validates map approval again when the request executes. A proposal produced against older inputs cannot be silently applied to a newer revision; show the conflict and allow review against its original snapshot or regeneration with current inputs. Finishing a skill run never grants creative approval automatically.

## States and recovery

| State | Expected behavior |
| --- | --- |
| Empty | Show a concise purpose, required inputs, and the eligible next action. |
| Loading | Retain saved fields and selected scene; distinguish loading references from generation. |
| Blocked | Name the missing map approval and link directly to the responsible unresolved items. |
| Generation failure | Preserve the current brief and user edits; show a retry action with the same explicit scope. |
| Unsaved or conflicting edit | Retain the local draft, display save status, and resolve conflicts before applying a proposal. |
| Stale dependency | Identify changed song spans or source references and provide affected scene links. |
| Missing asset | Keep the scene's intent visible; show the missing reference and its consumers. |

Retrying a text-generation request must not begin image or video generation. A user can leave and return without losing the current selection or saved draft.

## Handoffs and traceability

The brief supplies creative constraints to cast definition and storyboard planning. The scene outline supplies setting, participation, and intended coverage. Downstream consumers record the exact brief/scene revisions used. A spelling-only correction may update text-dependent outputs without requiring footage regeneration; a changed setting or performance instruction is assessed through shared dependency review.

Reference attachments point to `AssetRevision`; this area does not own duplicate file storage. Reusable assets may already exist before the map is approved, but their use in a new AI-generated treatment or production remains gated. Cross-project asset reuse is P1 under AST-03.

## Acceptance criteria

| ID | Behavioral criterion | PRD trace |
| --- | --- | --- |
| BRIEF-AC01 | V13 and V14 open before map approval, while treatment/outline generation stays blocked in UI, API, and skills. | MAP-09; AC-05 |
| BRIEF-AC02 | A proposal limited to mood preserves unrelated brief fields and existing scenes. | DIR-01; PRD §11.3 |
| BRIEF-AC03 | The approved brief identifies the exact map and reference revisions used. | DIR-01; PRD §13 |
| BRIEF-AC04 | Reusing one scene in three choruses retains three distinct interval links and one scene identity. | DIR-02; SCN-02; AC-04 |
| BRIEF-AC05 | Reordering an outline does not alter existing timeline clips. | SCN-02; EDT-01 |
| BRIEF-AC06 | Changing a scene shows affected shots without deleting takes or submitting paid jobs. | SCN-01; AC-11 |
| BRIEF-AC07 | Wizard and direct edits operate on the same brief and scene records. | AC-06 |

## Accessibility and responsive behavior

All fields have persistent labels and validation text. Scene ordering has keyboard move controls in addition to drag and drop. Interval selection supports numeric entry and playback, and no status relies only on color. On narrow layouts, the outline, inspector, and reference rail become focused panels with preserved selection and a return action. Text and linked timings remain accessible even when visual reference thumbnails are collapsed.

## Open design decisions

- Decide the minimum required brief fields for approval during wireframing; optional creative choices must remain explicitly unset.
- Choose whether planning intervals are displayed primarily by song section, timecode, or both. The stored source relationship stays unchanged.
- Select a visual presentation for creative references; no fixed palette or branded template system is specified.
