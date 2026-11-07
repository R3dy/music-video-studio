# Area 01 — Project Setup

Version 0.1 · Draft · November 6, 2026  
[Master UI specification](00_UI_Master_Spec.md) · [Product requirements](Music_Video_Studio_PRD.md)

## Purpose and scope

Give a creator a durable production container, a clear starting point, and control over project-wide defaults. This area owns project selection, initial creation, and project settings. It does not perform audio alignment or launch creative generation during setup.

Authentication, account billing, teams, and public publishing are outside this specification. The initial owner is a single creator. General film production remains deferred even though the underlying entity is named `Production`.

## View inventory

Routes below are proposed navigation contracts, not implemented endpoints.

| ID | View | Presentation | Proposed path |
| --- | --- | --- | --- |
| V01 | Project browser | Full page, grid/list toggle | `/projects` |
| V02 | Create/import project | Focused page or substantial drawer | `/projects/new` |
| V03 | Project settings | Project workspace settings view | `/projects/:projectId/settings` |

## V01 — Project browser

The primary region lists projects with title, thumbnail when available, last saved time, selected cut, and concise completion/blocker information. Search and sorting operate on project metadata. A persistent Create project action opens V02. A project card opens the dashboard; its Resume action restores the last valid workspace and selection, falling back to the dashboard if the referenced object no longer exists.

Cards distinguish an empty project from one with imported audio, an unapproved map, or usable footage. Do not calculate a misleading single completion percentage by counting files. A compact summary such as “Map approved · 8 of 24 planned shots have selected takes” is preferable.

Read `Production` metadata and derived progress summaries. Opening or sorting projects does not mutate production content. Rename is an inline metadata edit with autosave and undo. Bulk deletion, duplication, and archival policies are open decisions; do not implement destructive card actions as an implicit part of this draft.

Primary actions: Create project, Open, Resume. Secondary actions: search, sort, switch grid/list, rename. A failed list request retains visible cached records with a retry action; it must not present failure as an empty library.

## V02 — Create/import project

Use a short form with title, optional description, recording upload, and lyrics entry/upload. The creation form may also offer an existing app-defined project package import. New projects can be saved before uploads finish so a user does not lose their entered information. Creation itself does not call paid inference.

For WAV and lyrics, show separate upload states, filenames, and validation errors. Audio import creates an immutable `AudioMasterRevision` and associated media metadata once validated. Supplied lyrics are retained as a source revision. Actual waveform inspection and source confirmation occur in [Song](03_Song.md); transcript reconciliation occurs in [Lyrics & Sync](04_Lyrics_and_Sync.md). Do not turn the setup form into a second audio or lyric editor.

For an app-defined package import, validate schema version, references, media availability, and checksums before activating the project. The importer must exclude embedded credentials, report missing media, and create a fresh project identity. Imported review records remain provenance rather than silently becoming the current owner's approval; require validation and current approval before dependent generation. This package round-trip is a proposed supporting refinement to the PRD's portable export, not a claim of compatibility with arbitrary editor projects. If deferred, keep the entry disabled with an explanation.

Submitting twice must not create two projects. On successful creation, move to V04 with the wizard focused on source inspection. On partial import, preserve the draft and identify exactly which files need attention. Users may resume upload or remove the failed attachment without recreating the project.

## V03 — Project settings

Use grouped settings for identity, output defaults, wizard preference, provider defaults, and project limits. Display the current master recording and performance-map revision as linked read-only summaries; changes to those sources occur in their owning workspaces.

Editable fields include title, description, planned aspect ratio, frame-rate/resolution profile, and default connections for supported task types. Provider credentials are managed in [Connections & Jobs](13_Connections_and_Jobs.md). The settings view stores references to connections, never secret values. A project-level budget summary can link to V43, which owns detailed limits and spending behavior.

Treat defaults as defaults for future actions. Changing a default video model does not rerun existing shots; changing aspect ratio or frame rate does not silently conform existing media or alter approved exports. Present a concise effect statement and, when required, open V47 to review dependent artifacts. Renaming a project is a simple metadata save and does not need an impact workflow.

Read/write `Production` settings and selected connection references. Keep per-cut delivery settings distinct from project defaults. Wizard enable/disable changes guidance only; it does not change approvals, unlock production, or fork the project. Save reversible metadata automatically; show validation errors next to their fields. Invalid connection selections cannot become the active default.

## AI and wizard behavior

The assistant may help name a project, explain setup fields, or identify missing inputs. It may not create a treatment, storyboard, or video before whole-song map approval. It may propose settings changes with their affected scope. Creating a project or uploading material never authorizes stem separation, alignment services with a charge, or production jobs by implication; those bounded actions are presented in their owning views.

The wizard resumes from structured state. If creation is complete but the recording is not confirmed, it directs the user to V05. If a package contains historical media but the current map is unapproved, the user can inspect all material while the production gate remains active.

## States and recovery

| Condition | Behavior |
| --- | --- |
| No projects | Show a brief explanation and Create project action. |
| Upload pending | Show per-file progress and retain entered metadata. |
| Unsupported/corrupt recording | Retain the draft; identify the rejected file and allow replacement. |
| Package media missing | List missing references; permit inspection of valid imported records. |
| Connection unavailable | Preserve the saved reference, mark it unavailable, and block only dependent execution. |
| Settings conflict | Retain the local proposal, show the latest saved values, and require explicit conflict resolution. |
| Network interruption | Mark unsaved changes clearly; do not claim upload or save completion. |

## Data and handoffs

This area owns production metadata, not the contents of master audio, maps, shots, or cuts. V02 hands uploaded audio to V05 and supplied text to V08. V03 links to V41 for connection setup and V43 for limits. V01 and V04 read shared progress projections rather than maintaining competing status flags.

## Acceptance criteria

| ID | Criterion | PRD trace |
| --- | --- | --- |
| SETUP-AC01 | Creating a project retains WAV/lyrics inputs and their identities without launching inference. | AUD-01; MAP-01; AC-01 |
| SETUP-AC02 | Repeated submit or refresh during creation does not duplicate the production or start generation. | AC-07; AC-17 |
| SETUP-AC03 | A failed attachment can be retried while title and successful uploads remain intact. | AC-17 |
| SETUP-AC04 | Switching wizard preference preserves the exact project objects and approval state. | AC-06 |
| SETUP-AC05 | Changing provider/output defaults leaves existing media versions and clip bindings intact. | Section 13; AC-11 |
| SETUP-AC06 | Imported package credentials are rejected/ignored and imported approvals cannot bypass current gates. | AC-05; AC-19 |

## Accessibility and responsive behavior

Provide keyboard-accessible cards, file inputs, sorting, and visible focus. Upload progress and validation messages must be announced without relying on color. On narrow screens, use a vertical project list and stacked setup/settings groups. Preserve clear saved/pending/error indicators; do not bury missing upload errors below a collapsed section.

## Open decisions

- Initial resource limits and supported lyric text formats.
- Whether project-package import ships in the first release or follows portable export.
- Long-term archive/deletion behavior and naming collision rules.

