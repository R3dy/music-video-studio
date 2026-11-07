# Area 08 — Assets

Version 0.1 · Draft · November 6, 2026  
[Master UI specification](00_UI_Master_Spec.md) · [Product requirements](Music_Video_Studio_PRD.md)

## Purpose and scope

Provide one consistent inventory of production media and references, with preview, provenance, versions, and usage. Assets remain accessible from every stage. They do not move through a sequence of stage-specific folders to become usable.

The Assets workspace owns media organization and inspection. Cast & World owns the semantic role of identity, wardrobe, location, and prop references. Shot Studio owns takes. Song owns the master recording. The inventory can display all of them without becoming a second editor for their domain-specific meaning.

## View inventory

| ID | View | Presentation | Proposed path |
| --- | --- | --- | --- |
| V23 | Asset browser and import | Workspace grid/list with filters | `/projects/:projectId/assets` |
| V24 | Asset detail and usage | Detail panel or focused page | `/projects/:projectId/assets/:assetId` |
| V25 | Asset version comparison | Focused comparison panel | `/projects/:projectId/assets/:assetId/compare` |

## V23 — Asset browser and import

Show thumbnails or media-type previews, name, type, current revision, approval/validity status, and usage count. Filters include media type, reference role, scene/cast association, imported/generated origin, and missing-media status. Category labels organize the inventory; they do not change file identity or downstream bindings.

Import accepts supported source media and creates an immutable `AssetRevision` after validation. Display per-file progress and clear type/size failures. An imported image can be assigned as a character or location reference through the owning workspace. An imported video intended as a take must enter Shot Studio's import/binding workflow so it receives a song-time relationship and technical validation. Merely uploading a clip does not approve it for the timeline.

Use the same browser component as a constrained picker from shot, cast, and storyboard editors. In picker mode, display eligible types and return the selected exact revision to the caller. Preserve search/filter state when returning from a preview. Multi-select import is allowed; multi-select generation is a separately bounded P1 production action, not a side effect of import.

Cross-project reuse is P1 under AST-03. If implemented, copied references retain provenance and production-specific review. Cross-project access must not leak private media or overwrite the source production's associations.

## V24 — Asset detail and usage

Center the media preview with an adjacent metadata/usage panel. Show the stable asset identity, revision, source/import/generation origin, technical properties, associated semantic roles, and review status. For audio and video, supply normal playback plus applicable metadata; precise vocal timing edits still belong in V10 or V31.

Usage lists exact consumers: character look, scene, storyboard frame, shot reference, take version, or timeline clip. Selecting a consumer navigates to that object while preserving the asset context. A generated asset can link to its existing GenerationJob in V42 and SkillRun in V45. Credentials and secret provider headers must never appear in provenance.

Primary actions are Use in current context, open the owning editor, and upload/create a new revision where permitted. Secondary actions include rename, categorize, download, inspect versions, and review provenance. Rename/category edits do not change media bytes or revoke approvals based solely on media identity. Replacing bytes creates a revision; consumers remain pinned to their earlier revision until explicitly updated.

Do not approve a take merely because its underlying media file is accepted as a reusable asset. Approval is contextual: a reference can be approved for character identity while a generated take still requires rendered-performance review. A destructive remove operation, if provided, must first reveal live dependencies and prevent silent media loss; permanent deletion policy is deferred.

## V25 — Asset version comparison

Present two labeled revisions with exact IDs, thumbnails/players, provenance, and relevant changed metadata. Images can use side-by-side comparison with synchronized zoom. Video can offer aligned playback where the timing relationship is known; if durations or origins differ, show that mismatch rather than implying frame equivalence. Audio comparisons must identify the reference clock and any alignment offset.

Show which consumers reference each revision. “Use this revision” opens a scoped consumer selection/impact review; it does not globally repoint every shot or clip. The proposed replacement should distinguish future defaults from existing pinned references. V47 owns the cross-artifact impact review and application of approved changes.

Read `AssetRevision`, dependencies, media properties, and reviews. Comparison is read-only until a bounded update is applied. Previous media, reviews, and usage history remain available after selecting a newer default. Revision selection must not create new inference jobs. A failed preview for one version should still leave its metadata and dependent consumers inspectable.

## AI and skill behavior

The assistant may suggest categories, locate suitable existing references, identify inconsistent associations, or propose a bounded asset-generation task through its owning production workflow. Suggestions do not silently rewrite identity constraints or approve a replacement reference.

Before whole-map approval, source import, inspection, and management of existing references remain available. Project-specific creative generation remains gated. An asset-generation request after approval must identify the affected entity, exact references, provider, and potential cost; “find me a reference” must not silently become an open-ended paid generation loop.

## States and recovery

| Condition | Behavior |
| --- | --- |
| Empty inventory | Offer import and explain where generated media will appear. |
| Import in progress | Show placeholders and per-file status; do not bind incomplete media to approved consumers. |
| Unsupported or corrupt input | Retain other successful imports and give an actionable file-specific error. |
| Media file unavailable | Keep metadata/usage visible and offer a verified relink or reimport path. |
| Newer revision exists | Show both selected and latest revision; never silently upgrade bindings. |
| Reference changed | Surface affected consumers and link to impact review. |
| Comparison loading/failure | Label each side independently; never show one revision under the other's label. |
| No eligible picker results | Explain required media types/roles and allow return without changing the caller. |

## Data and cross-area handoffs

The canonical media entity is `AssetRevision`; semantic roles remain explicit references from `Character`, `Scene`, `ShotRevision`, and other objects. Import metadata must include enough provenance to distinguish generated material from user-supplied files. Content checksums support integrity, not a promise that different assets with equal bytes have identical semantic roles.

V23 returns exact revision IDs to [Cast & World](06_Cast_and_World.md), [Storyboard](07_Storyboard_and_Animatic.md), and [Shot Studio](09_Shot_Studio.md). V24 links source audio to [Song](03_Song.md), and take-specific review to [Lip-Sync Lab](10_Lip_Sync_Lab.md). V25 hands dependency updates to [Shared Tools](14_Shared_Tools.md).

## Acceptance criteria

| ID | Criterion | PRD trace |
| --- | --- | --- |
| ASSET-AC01 | Importing multiple files preserves successful items when one fails. | AST-01; Section 17 |
| ASSET-AC02 | A picker returns an exact compatible revision without changing the caller on cancel. | AST-02; Section 9 |
| ASSET-AC03 | New media bytes create a revision while existing consumers stay pinned. | AST-01; AC-11 |
| ASSET-AC04 | Usage links resolve the consuming object and selected revision correctly. | AST-02 |
| ASSET-AC05 | Accepting a reference asset cannot approve its generated performances automatically. | SYN-05; SYN-07; AC-15 |
| ASSET-AC06 | Choosing a revision never launches generation or changes unrelated consumers. | AC-07; AC-11 |
| ASSET-AC07 | Imported footage enters the take-validation workflow before becoming an approved timeline source. | SHT-02; SHT-06; AC-14 |

## Accessibility and responsive behavior

Offer list view alongside the visual grid, textual file labels, keyboard selection, and clear focus on picker return. Avoid color-only approval marks. On smaller screens, asset detail and comparison can use stacked previews while retaining revision labels and usage information. Comparison playback controls must remain distinguishable by revision.

## Open decisions

- Supported media formats and resource limits.
- Initial category vocabulary and user-defined tagging.
- Retention, verified media relinking, and permanent deletion policy.
- Exact scope and access model for P1 cross-project reuse.

