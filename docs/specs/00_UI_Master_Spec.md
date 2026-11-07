# Music Video Studio — Master UI Specification

Version 0.1 · Draft for design and engineering · November 6, 2026

## 1. Purpose and document hierarchy

This specification translates the Music Video Studio PRD into a concrete inventory of **47 distinct view types across 14 functional areas**, including **12 main workspaces**. It defines the shared interaction rules and links to a separate specification for each area.

This is a proposed UI architecture, not a description of implemented software. Routes, layout choices, and presentation forms are design proposals. Product requirements, scope, and P0/P1 priorities remain governed by the [PRD snapshot](Music_Video_Studio_PRD.md). The original source is also available as [Music_Video_Studio_PRD.md](https://chatgpt.com/library?file_id=libfile_3f72c58a094481919cffbd71f1e7ab74&account_id=personal).

Use this document for navigation and cross-area decisions. Use the area specs for individual view behavior. If a design proposal conflicts with a PRD requirement, preserve the requirement and record the needed design change. This package leaves the original PRD unchanged.

All area links use relative paths so the downloaded Markdown package remains portable. The package includes the source PRD snapshot, this master specification, and 14 area specifications.

## 2. Area specifications

| Area | Views | IDs | Specification |
| --- | ---: | --- | --- |
| Project setup | 3 | V01–V03 | [01_Project_Setup.md](01_Project_Setup.md) |
| Project dashboard | 1 | V04 | [02_Project_Dashboard.md](02_Project_Dashboard.md) |
| Song | 3 | V05–V07 | [03_Song.md](03_Song.md) |
| Lyrics & Sync | 5 | V08–V12 | [04_Lyrics_and_Sync.md](04_Lyrics_and_Sync.md) |
| Brief & Outline | 2 | V13–V14 | [05_Brief_and_Outline.md](05_Brief_and_Outline.md) |
| Cast & World | 5 | V15–V19 | [06_Cast_and_World.md](06_Cast_and_World.md) |
| Storyboard & Animatic | 3 | V20–V22 | [07_Storyboard_and_Animatic.md](07_Storyboard_and_Animatic.md) |
| Assets | 3 | V23–V25 | [08_Assets.md](08_Assets.md) |
| Shot Studio | 4 | V26–V29 | [09_Shot_Studio.md](09_Shot_Studio.md) |
| Lip-Sync Lab | 4 | V30–V33 | [10_Lip_Sync_Lab.md](10_Lip_Sync_Lab.md) |
| Timeline | 3 | V34–V36 | [11_Timeline.md](11_Timeline.md) |
| Review & Export | 3 | V37–V39 | [12_Review_and_Export.md](12_Review_and_Export.md) |
| Connections & Jobs | 4 | V40–V43 | [13_Connections_and_Jobs.md](13_Connections_and_Jobs.md) |
| Shared tools | 4 | V44–V47 | [14_Shared_Tools.md](14_Shared_Tools.md) |
| **Total** | **47** | **V01–V47** | **14 area specs** |

The 12 main workspaces are the dashboard, the ten production stages, and Connections & Jobs. Project setup and shared tools complete the application around those workspaces. They are not two additional production stages.

## 3. Counting and scope rules

A view is a reusable interaction surface with a meaningful purpose, content structure, and action set. It can be a full page, workspace tab, focused editor, or substantial inspector/drawer. Presentation form can change responsively without creating another view type.

- Editing 40 shots reuses the same shot and take views; it does not add 40 screens.
- The wizard guides these same views and does not create a duplicate screen set.
- Small confirmations, file pickers, menus, tooltips, and media transport controls are components rather than additional numbered views.
- Empty, loading, running, failed, blocked, stale, and read-only appearances are states of the same view.
- An audition uses an ordinary shot/take workflow, not a separate screen.
- P1 capabilities such as optional batching or multiple alignment proposals fit inside the assigned views and retain their deferred priority.
- Authentication, billing, team administration, public publishing, a skill marketplace, and general film editing are outside this inventory.

The 47-view decomposition is the working design baseline. Merging or splitting views later requires an explicit inventory update and traceability review; it must not quietly remove functionality or change the agreed scope.

## 4. Canonical view register

| ID | View | Owner |
| --- | --- | --- |
| V01 | Project browser | Project setup |
| V02 | Create/import project | Project setup |
| V03 | Project settings | Project setup |
| V04 | Progress, blockers, and next actions | Dashboard |
| V05 | Master recording and waveform | Song |
| V06 | Stems | Song |
| V07 | Song structure and musical markers | Song |
| V08 | Transcript reconciliation | Lyrics & Sync |
| V09 | Vocal-lane assignment | Lyrics & Sync |
| V10 | Word/phoneme timing editor | Lyrics & Sync |
| V11 | Alignment comparison and unresolved passages | Lyrics & Sync |
| V12 | Map approval and export | Lyrics & Sync |
| V13 | Creative treatment and visual direction | Brief & Outline |
| V14 | Scene outline and scene details | Brief & Outline |
| V15 | Cast roster | Cast & World |
| V16 | Character identity and performance profile | Cast & World |
| V17 | Wardrobe and looks | Cast & World |
| V18 | Locations and sets | Cast & World |
| V19 | Props and continuity | Cast & World |
| V20 | Storyboard and shot coverage | Storyboard & Animatic |
| V21 | Frame editor | Storyboard & Animatic |
| V22 | Timed animatic playback | Storyboard & Animatic |
| V23 | Asset browser and import | Assets |
| V24 | Asset detail and usage | Assets |
| V25 | Asset version comparison | Assets |
| V26 | Shot direction and generation settings | Shot Studio |
| V27 | Generation preflight and cost review | Shot Studio |
| V28 | Take gallery and player | Shot Studio |
| V29 | Take comparison and selection | Shot Studio |
| V30 | Voice-to-face assignment | Lip-Sync Lab |
| V31 | Vocal crop and timing inspection | Lip-Sync Lab |
| V32 | Sync configuration | Lip-Sync Lab |
| V33 | Rendered sync review and repair | Lip-Sync Lab |
| V34 | Main editing timeline | Timeline |
| V35 | Clip and transition inspector | Timeline |
| V36 | Alternate cuts and comparison | Timeline |
| V37 | Final quality checklist and issues | Review & Export |
| V38 | Export configuration | Review & Export |
| V39 | Rendered delivery review and downloads | Review & Export |
| V40 | Connection inventory | Connections & Jobs |
| V41 | Provider configuration and testing | Connections & Jobs |
| V42 | Job queue and execution details | Connections & Jobs |
| V43 | Spending and runtime limits | Connections & Jobs |
| V44 | AI director conversation | Shared tools |
| V45 | Skill proposals and execution details | Shared tools |
| V46 | Revision history and comparison | Shared tools |
| V47 | Change-impact review | Shared tools |

## 5. Shared application shell

### 5.1 Layout regions

1. **Project header:** title, selected cut, save status, and project-level navigation.
2. **Workspace navigator:** twelve main destinations with status and issue counts. Direct navigation remains available in wizard mode.
3. **Primary canvas:** the active view's editor, table, board, preview, or timeline.
4. **Context region:** selection-specific inspector and access to the AI director. Only relevant panels need to be open.
5. **Playback transport:** master time, playback, looping, and audio-source indication where media is relevant.
6. **Execution status:** background jobs and connection/cost summaries with links to V42/V43.
7. **Wizard guidance:** current objective, prerequisites, and Continue/Back/Skip guidance where skipping is valid. Guidance does not bypass execution gates.

The visual design should support prolonged media work and readable precision controls. A specific theme, color palette, typography system, or component library is not selected by this specification.

### 5.2 Selection and navigation context

Maintain a selection context containing project, active source/map revisions, selected cut, current song time or interval, and selected entity IDs. Views consume the portions relevant to them. Switching from a lyric to its shot should preserve the source-song interval. Returning from an asset picker should restore the caller's selection and scroll position.

Opening a preview may temporarily use a clip-local clock, but the UI must label it and retain the mapping back to song time. Switching audio audition source must not silently change the project master.

Deep links must resolve stable entity identities and selected revisions where needed. A missing entity should produce a useful unavailable state with a return path, not silently select another object. Browser back/forward should not trigger generation or replay an approval action.

## 6. Guided and direct operation

Both navigation modes use the same records, action handlers, validations, and approvals. The wizard is an orchestration and explanation layer.

| Situation | Required guidance |
| --- | --- |
| No confirmed source | Direct the user to recording inspection in V05. |
| Unresolved performed text or voices | Open the relevant V08/V09 item. |
| Timing issues remain | Open V10/V11 at the affected interval. |
| Map ready for review | Open V12; do not auto-approve it. |
| Map approved, creative plan missing | Offer V13/V14 and cast/world setup. |
| Plan ready, no footage | Open the selected shot in V26 and its preflight in V27. |
| Visual take ready, sync incomplete | Open V30–V33 with the take and source interval. |
| Takes selected | Open the cut in V34 without silently replacing existing clips. |
| Cut ready for delivery | Open V37, then V38/V39 for render and encoded review. |
| Upstream inputs changed | Open V47 with the impact set; preserve existing media. |

“Skip” may defer optional work or navigate elsewhere; it must not mark a required approval complete. “Continue” opens or proposes the next action. Cost-incurring execution requires a bounded action scope or an existing explicit policy that covers it.

## 7. Shared execution gates

| Gate | Applies to | Required behavior |
| --- | --- | --- |
| Whole-song map approval | AI treatment, storyboard, and production generation | Check the exact current input revision on the server, including requests from UI, API, and skills. |
| Input readiness | Any processing job | Confirm complete, valid, revision-pinned input media and required bindings. |
| Provider compatibility | Inference requests | Show actual capabilities; block unsupported controls or performer combinations. |
| Bounded authorization | Cost-incurring work | Identify task, inputs, provider, selected scope, cost/unknown estimate, and applicable limits. |
| Audition approval | P1 batch production | Require review for the relevant reference/performer/provider configuration. |
| Rendered-performance approval | Final-use visible vocal performance | Treat map approval and automatic metrics as insufficient on their own. |
| Delivery validity | Approved final export | Verify required current approvals and check the actual encoded result. |

All workspaces remain navigable before these conditions are met. Source import, connection configuration, reference management, and alignment work remain possible before creative generation is allowed. Existing material remains inspectable after an upstream change.

## 8. State, save, and recovery contract

Artifact review state (`draft`, `needs_review`, `approved`, `rejected`), dependency validity (`current`, `stale`), and execution state are distinct. A failed job is not a rejected creative artifact. A historically approved take may be stale for current inputs without losing its historical review.

| Shared state | Required presentation and actions |
| --- | --- |
| Empty | Explain the missing object/input and offer the relevant import/create action. |
| Loading | Preserve stable layout and existing selection; distinguish processing from network loading. |
| Saving | Show pending/saved status; surface failure without discarding local edits. |
| Running | Identify the existing job and its scope; allow other valid work. |
| Failed | Explain the failed operation and whether work or cost already occurred; offer scoped recovery. |
| Blocked | Name the unmet prerequisite and link to its owner. |
| Stale | Show which dependency changed and open impact review; retain old media and approvals. |
| Unknown outcome | Reconcile the existing provider request; do not submit a duplicate automatically. |
| Read-only history | Show the revision context and provide a deliberate path to create a new editable revision. |
| Conflict | Preserve the pending proposal and compare it with the latest saved state. |

Save reversible metadata and edits without repeated approval dialogs. Use explicit review for impactful dependency changes, approval decisions, and bounded generation requests. Refreshing a page must never resubmit a paid request.

## 9. Ownership and cross-area contracts

| Authoritative concern | Owning area | Typical consumers |
| --- | --- | --- |
| Production metadata/defaults | Project setup | All workspaces |
| Master recording and derived audio | Song | Alignment, sync, timeline, export |
| Performed transcript and timing map | Lyrics & Sync | Brief, shot planning, sync, QA |
| Creative intent and scene outline | Brief & Outline | Cast, storyboard, shot planning |
| Identity, looks, location/prop continuity | Cast & World | Assets, storyboard, shots, QA |
| Planned coverage and animatic | Storyboard & Animatic | Shots, dashboard, timeline |
| Media identity, revisions, and usage | Assets | All media-consuming areas |
| Shot plans and visual take candidates | Shot Studio | Sync, timeline, QA |
| Voice/face binding and rendered sync review | Lip-Sync Lab | Timeline, final review |
| Editorial placements and alternate cuts | Timeline | Dashboard, review, export |
| Delivery validation and encoded output | Review & Export | Dashboard, downloads |
| Provider capabilities, jobs, and cost facts | Connections & Jobs | Every processing/generation view |
| AI proposals, revision history, impact application | Shared tools | All workspaces |

Area ownership means editing authority over the concern, not exclusive access to its presentation. The same job can appear in Shot Studio and the queue while having one execution record.

Selection of a preferred take is distinct from replacing clips in a cut. Selecting a newer reference is distinct from repointing all consumers. Change-impact review must make those scopes explicit.

## 10. Timing and media rules

- Keep the original WAV as the canonical clock, using integer sample positions and sample-rate metadata.
- Keep song time, media-local time, and timeline time distinct and visibly labeled when ambiguity is possible.
- Preserve exact source revision, vocal lane, event occurrence, crop boundaries, context handles, and transformations through handoffs.
- Use rational frame-rate handling and defined rounding at render boundaries; do not accumulate rounded clip durations.
- Represent simultaneous voices independently and repeated lyrics as distinct occurrences.
- Do not silently stretch, loop, or shift singing footage to fit a duration.
- Derived stems, previews, and provider media do not replace the master soundtrack by default.
- The waveform, map, and sync diagnostics support review; none can alone certify natural rendered mouth movement.

## 11. Accessibility and device behavior

Full authoring is designed first for a desktop workspace, with keyboard operation for precise editing. Important timing values must also be editable without drag-only interaction. Focus, labels, error descriptions, and selection context must be preserved across drawers and dialogs.

On narrower displays, convert simultaneous panels to focused or stacked views. Keep all essential data accessible; do not hide voice lanes, approval blockers, or costs to make a layout fit. Heavy editing may use a focused workspace with panning/zoom, while review and simple edits remain practical on touch devices. Final minimum viewport and supported browser/device targets remain open implementation decisions.

Use text alongside status colors and icons. Respect reduced-motion preferences where animation is decorative. Live progress should not steal focus or flood assistive announcements. Video and audio controls must identify the source being auditioned.

## 12. Acceptance and handoff requirements

| ID | Shared acceptance criterion | PRD trace |
| --- | --- | --- |
| UI-AC01 | All 47 registered views have one owning area and a documented entry/exit path. | Section 6 |
| UI-AC02 | Wizard and direct navigation share state, approvals, and actions. | AC-06 |
| UI-AC03 | Required production gates apply identically through UI, API, and skills. | AC-05 |
| UI-AC04 | A user can complete and export one shot without submitting another shot. | AC-07; AC-20 |
| UI-AC05 | Timing relationships and vocal-lane identity survive cross-area handoffs. | AC-03; AC-12; AC-13 |
| UI-AC06 | A reference or take revision does not silently replace already-pinned consumers. | AC-08; AC-11 |
| UI-AC07 | Unknown job outcomes cannot cause automatic duplicate chargeable submissions. | AC-10; AC-17 |
| UI-AC08 | Final approved delivery depends on current reviews and encoded-output validation. | AC-18 |
| UI-AC09 | Loading, empty, error, blocked, stale, and recovery states are designed without adding duplicate business logic. | Section 17 |
| UI-AC10 | Responsive presentation and keyboard access preserve the same essential actions and information. | Section 17 |

Each area spec adds local acceptance criteria and PRD requirement references. Wireframes should show a representative populated state plus the important alternate states, identify action scope, and demonstrate navigation to related view IDs. Data/API design should preserve the conceptual ownership and revision invariants; this package does not prescribe a framework, database, provider, or hosting topology.

## 13. Decisions retained as open

- Final visual design and component system.
- Initial inference adapters, model configurations, and local worker transport.
- Initial output profiles, resource limits, and device/browser targets.
- Quantitative alignment/sync benchmarks after validation on representative songs.
- Whether portable project import accompanies initial export; the setup view reserves that path as a proposed refinement.
- External editor interchange formats and cross-project asset reuse timing.

Changes to these choices should be recorded in the affected area spec and this index when they alter the shared contract. They must not dilute the foundational whole-song approval, independent rendered review, or individually controlled production workflow.

