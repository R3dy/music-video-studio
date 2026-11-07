# Cast & World — Area Specification

| Field | Value |
| --- | --- |
| Version | 0.1 |
| Status | Draft UX and functional specification |
| Date | November 6, 2026 |
| Views | V15–V19; 5 view types |
| Scope | P0 identity, appearance, performance, location, and continuity management |

[Master UI specification](00_UI_Master_Spec.md) · [Product requirements](Music_Video_Studio_PRD.md)

## Purpose and boundaries

Define who appears in the video, how each person should look and perform, and the places and objects that must remain consistent. The area connects creative intent to approved reference assets. It does not guarantee that a generation model will preserve those references; generated identity and continuity are reviewed later.

`Character` stores a reusable identity. `CastMember` describes participation in this production. `PerformanceBinding` connects a recorded vocal lane to a visible cast member over a song interval. These remain separate: a dancer may have no voice, and a visual character may portray a recorded vocalist.

Users can always inspect existing definitions and manage imported source references. New AI creative planning or reference generation requires current whole-song map approval. The cast/world skill uses the chosen brief and scene outline when available. Unspecified creative choices remain visible rather than being invented as approved facts.

Exit readiness is evaluated for a particular consumer: a shot needs the relevant cast/look/location/prop revisions, not every possible asset in the production. Review records identify exact versions. A new reference revision does not silently update approved shots.

## View inventory

Routes are proposed, and detail selections stay within the named view type.

| ID | View | Presentation | Proposed deep link |
| --- | --- | --- | --- |
| V15 | Cast roster | Cards or accessible list | `/projects/:projectId/world/cast` |
| V16 | Character identity and performance profile | Detail editor with reference gallery | `/projects/:projectId/world/cast/:castMemberId` |
| V17 | Wardrobe and looks | Look editor with cast/scene assignments | `/projects/:projectId/world/looks?look=:lookId` |
| V18 | Locations and sets | Location browser with selected detail | `/projects/:projectId/world/locations?location=:locationId` |
| V19 | Props and continuity | Prop inventory plus continuity assignment matrix | `/projects/:projectId/world/continuity?scene=:sceneId` |

## V15 — Cast roster

Show cast cards with name, role, reference thumbnail, identity-review status, assigned scenes, and whether a vocal binding exists. Filters separate principals, supporting characters, individual extras, and extra groups. The roster must not imply that every visible person sings. An extra group may have a descriptive visual specification without individually modeled identities.

The primary action is **Add cast member**. Users can create a character definition, attach existing project references, or define an extra group. **Open profile** enters V16. Secondary actions assign participation to scenes, inspect usage, and remove an unused participation record. Cross-project character browsing is a P1 extension of asset reuse, not a required MVP marketplace.

Creating participation writes `CastMember` and optionally a new `Character`; it does not create a recorded voice. Display voice assignments as links to `VocalLane` and `PerformanceBinding`. Initial voice-to-character intent can be captured here, while exact shot intervals and face targeting are refined in [Lip-Sync Lab](10_Lip_Sync_Lab.md).

Removing a cast member with bindings or downstream shots opens shared change-impact review. Existing media remains traceable to the historical identity. The roster can remain partially defined while users develop narrative coverage, but a production action cannot claim a missing required reference is approved.

## V16 — Character identity and performance profile

The main pane displays identity description, reference images, approved reference selection, and distinguishing appearance details. The supporting pane separates performance direction: posture, gaze, gesture style, expression, intensity, and movement. Users can describe a relaxed R&B delivery without changing the recorded vocal or creating a new body type.

Primary actions are **Save profile** and **Approve reference set**. Users can import images, select approved source versions, reorder reference priority, annotate a reference, and request a bounded reference-generation job when the gate and provider capabilities allow it. A generated reference remains a candidate until reviewed. Simple image annotation is scoped to marking relevant evidence; advanced image editing is not introduced by this specification.

Reference details should support precise textual constraints such as tattoo placement, left/right accessories, hairstyle, or glasses. Store the constraint with its source asset and intended scope. Conflicting reference images must be surfaced rather than averaged into an undisclosed design. A constraint such as “same face” is creative intent, not evidence that a model obeyed it.

The profile writes character and participation revisions plus referenced `AssetRevision` IDs. A performance direction specific to this production belongs to `CastMember`, not automatically to the reusable identity. **View affected shots** opens usage; **Assign look** opens V17. Reference changes preserve previous approvals and trigger dependency assessment.

## V17 — Wardrobe and looks

Present named looks with their reference images, included clothing/accessories, associated cast members, and scene or shot assignments. Selecting a look shows its parts, intentional variations, and continuity notes. A cast member can have several approved looks, but downstream shots must select which one applies.

Primary actions are **Create look**, **Assign look**, and **Approve look**. Users can attach or import references, duplicate a look as a new revision or variation, and inspect its uses. The distinction between editing a saved look and creating a variation is explicit. Changing one scene's outfit must not silently change the performer across every scene.

Assignments read the scene plan and write versioned appearance constraints referenced by scenes/shots. Supporting look/assignment fields are proposed refinements of the PRD's appearance and continuity data, not separate production stages. A scarf, watch, or jewelry item may also reference a prop record without duplicating its media.

Flag incompatible assignments when they apply to the same performer in the same intended continuous action. Users may record an intentional transition; the app must preserve that explanation. Approving a look confirms the chosen specification and references. Each generated take still needs its own visual continuity review.

## V18 — Locations and sets

Display locations with a name, setting description, reference frames, spatial notes, lighting intent, approved version, and linked scenes. The selected detail includes reusable set features and scene-specific variations. A studio's permanent desk layout can remain stable while lighting changes for a later scene.

Primary actions are **Create location**, **Attach reference**, and **Assign to scene**. Users may import a location image, generate a candidate reference with a bounded job, or use a text-only placeholder until imagery exists. Candidate selection and approval remain explicit. The UI distinguishes a reused location record from a copied location variant.

Location definitions are supporting structured world data linked through `Scene` and `AssetRevision`; stable references let multiple scenes share a set without sharing every lighting or prop state. A scene's shot plan reads exact approved reference revisions. Replacing an image shows consumers before applying downstream changes.

No map, geographic search, or real-world booking feature is implied. Spatial guidance is for visual production. **View scenes** returns to [Brief & Outline](05_Brief_and_Outline.md); **Inspect coverage** opens [Storyboard & Animatic](07_Storyboard_and_Animatic.md) filtered to the location.

## V19 — Props and continuity

Combine a prop inventory with a scene/shot assignment matrix. The inventory lists object descriptions, reference assets, variants, and current approval. The matrix shows which cast members interact with which props, where objects should appear, and any state that changes during the story. A detail panel belongs to this view and does not add a new view count.

Primary actions are **Add prop**, **Assign**, and **Record continuity rule**. Users can distinguish a persistent requirement from an intentional change, link evidence to a rule, and inspect affected shots. Examples include a microphone remaining in the same hand or a laptop screen changing between scenes. Rules can be descriptive; the MVP does not promise automatic visual recognition of every object state.

The view reads scenes, cast, looks, locations, and planned shots. It writes supporting continuity fields and reference dependencies. A warning identifies the conflicting assignments and allows correction or an explicit intentional variation. It does not block unrelated production work.

**Review generated result** navigates to the relevant take rather than declaring the requirement satisfied from the plan. An absent or obscured mouth caused by a prop should also be visible to shot preflight and lip-sync review; continuity approval cannot override a provider's face-visibility constraints.

## AI and bounded generation

The cast/world skill proposes identities, looks, location needs, and continuity requirements from approved timing and chosen creative direction. It must label inferred preferences. Applying a proposal creates structured drafts, not approvals.

Reference-generation actions show the selected character or location, attached source versions, requested output count, provider, and estimate or unknown cost. Generating a character reference must not generate wardrobe, every location, or video shots as an undisclosed batch. Shared V45 records execution, while [Connections & Jobs](13_Connections_and_Jobs.md) owns job recovery and costs.

## States, recovery, and handoffs

| State | Expected behavior |
| --- | --- |
| Empty roster/world | Provide relevant add/import actions and a brief explanation of required references. |
| Loading references | Preserve names, constraints, assignments, and known status while media loads. |
| Blocked AI action | Link to current map approval or missing connection; manual source management remains available. |
| Failed import/generation | Retain prior assets and draft descriptions; show the failed item and bounded retry. |
| Conflicting references | Show the contradiction and source versions; require a chosen interpretation before approving the affected set. |
| Stale reference | Show historical approval alongside the changed dependency and its consumers. |
| Missing linked asset | Mark the precise dependency unresolved without deleting its description or usage. |

The asset system owns media and immutable versions. Cast & World owns the meaning and assignments of those references. [Assets](08_Assets.md) supplies preview, import, usage, and version tools. Storyboard and Shot Studio consume approved versions; Lip-Sync Lab resolves face bindings against actual footage. Changing a reference never silently rewrites takes.

## Acceptance criteria

| ID | Behavioral criterion | PRD trace |
| --- | --- | --- |
| CAST-AC01 | Adding a dancer or extra does not require a recorded vocal assignment. | CAST-01; CAST-03; CAST-04 |
| CAST-AC02 | One character can participate in this production with project-specific performance direction. | CAST-01; CAST-05 |
| CAST-AC03 | An approved identity identifies its precise reference versions and distinguishes unresolved contradictory references. | CAST-02; AST-02 |
| CAST-AC04 | A wardrobe change scoped to one scene leaves unrelated assignments intact. | SCN-01; AC-11 |
| CAST-AC05 | Several scenes can reference the same location while retaining scene-specific lighting or prop state. | SCN-01; SCN-02 |
| CAST-AC06 | A reference generation submits only the displayed bounded scope and does not approve its result. | AC-07; PRD §11.3 |
| CAST-AC07 | Whole-song approval is checked for AI creative generation even when a deep link opens the view directly. | MAP-09; AC-05 |
| CAST-AC08 | Changing an approved reference preserves historical takes and lists affected consumers. | AC-11; PRD §13 |
| CAST-AC09 | A continuity rule remains distinct from verification that generated footage obeys it. | SYN-05; SYN-07; AC-15 |

## Accessibility and responsive behavior

Offer a list alternative to image-heavy cast cards and a linear assignment alternative to the continuity matrix. Images have editable descriptive labels; left/right body annotations include text. All assignments work through keyboard-selectable controls. Narrow layouts show one roster/detail/reference panel at a time with preserved selection. Critical reference conflicts and blocked actions remain visible outside collapsed media panels.

## Open design decisions

- Finalize the supporting schema for looks, locations, props, and scoped continuity rules during technical design.
- Choose how approved reference sets communicate priority to each adapter; unsupported ordering must be disclosed.
- Decide which reference annotations are structured fields versus free text without promising automated verification.
