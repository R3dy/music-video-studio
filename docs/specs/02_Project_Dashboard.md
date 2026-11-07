# Area 02 — Project Dashboard

Version 0.1 · Draft · November 6, 2026  
[Master UI specification](00_UI_Master_Spec.md) · [Product requirements](Music_Video_Studio_PRD.md)

## Purpose and scope

Provide an accurate project overview and the next useful action without forcing a linear journey. The dashboard summarizes production state; it does not own alternate copies of approvals, job status, or timeline selections.

## View inventory

| ID | View | Presentation | Proposed path |
| --- | --- | --- | --- |
| V04 | Progress, blockers, and next actions | Project overview page | `/projects/:projectId/overview` |

The overview is one view with several coordinated panels. Its status cards, activity feed, and recommended action are not additional numbered screens.

## V04 — Progress, blockers, and next actions

### Layout and content

Place the project title, save status, and selected cut in the header. The main region has a stage-progress summary, the highest-priority blocking issues, a preview of the current cut or animatic, and the next recommended action. A secondary column shows running/recent jobs and summarized cost/runtime. Recent meaningful activity appears below, with links to the affected revisions.

Stage cards use factual states: no input, work in progress, needs review, approved for current inputs, or changed dependencies. Historical approval and current validity must remain distinguishable. For example, “Take approved; audio binding changed” is more informative than silently changing it to “unapproved.”

Report map coverage and uncertainty, planned shot coverage, selected approved takes, and export status as separate measures. Do not claim that every generated file completes a shot. The existence of 30 takes for one shot must not inflate progress relative to 30 distinct planned shots.

### Actions and navigation

The main action is a contextual Continue button. Before source confirmation it opens V05; before map approval it opens the relevant alignment issue or V12; after map approval it can guide the creator into brief, cast, storyboard, or production according to actual prerequisites. If a generation is already running, Continue should offer review or another valid preparatory task rather than submitting the same job again.

Each stage card opens its workspace even when actions there are blocked. Blocker entries link to the exact vocal span, reference, shot, job, or export issue when possible. The preview opens V22, V34, or V39 depending on the referenced artifact. Job links open V42; cost links open V43; change-impact notices open V47.

The user may dismiss a recommendation without completing it. Dismissal changes guidance visibility only. It does not approve work, resolve a defect, or alter the underlying production gate. The dashboard must never expose a vague “Finish everything” action that silently creates a paid batch.

### Inputs, outputs, and freshness

Read `Production`, active master/map revisions, artifact approvals, dependency validity, planned shots, selected take versions, cuts, jobs, and export records. Maintain only lightweight presentation preferences such as selected preview and dismissed recommendation IDs. Other changes are executed through the owning domain action and reflected here.

Summaries should identify their current source revision. During refresh, retain the last known data with an updating indicator. If job status is unknown, display unknown rather than failed or completed. Preview availability must not imply final quality approval.

## AI and wizard behavior

The AI director may explain blockers, summarize changes, or propose an ordered next step. Recommendations use structured prerequisite checks. A generated explanation cannot waive a server-enforced gate.

In wizard mode, highlight one next action and briefly explain its purpose. In free navigation, preserve the same stage status and route links. Both modes share selections, saved state, and approvals. If the map changes after production, the dashboard should show an impact summary while retaining access to historical approved takes.

Suggested next steps should prioritize unresolved source/map issues, then user-selected production goals. A pending expensive job is not a reason to submit more work automatically. Batch suggestions, when implemented as P1, must open a bounded review in V27 and obey the relevant audition requirements.

## States and recovery

| Condition | Dashboard response |
| --- | --- |
| Newly created production | Show the source/lyrics checklist and Continue to source inspection. |
| Map incomplete | Identify remaining discrepancies and timing issues; creative generation stays gated. |
| Map approved, no plan | Offer treatment/outline work while all other areas remain accessible. |
| No generated media | Preview the animatic or show a meaningful placeholder, not a broken video player. |
| Jobs running | Show status and link to the existing jobs; navigation stays available. |
| Provider outcome unknown | Mark reconciliation needed; do not recommend automatic retry. |
| Upstream revision | Show affected object counts and link to V47 without destroying old selections. |
| Final render created | Label it awaiting delivery review until encoded-output checks and required approvals pass. |
| Partial overview refresh failure | Preserve available panels and explain which summary is temporarily unavailable. |

## Data ownership and handoffs

The dashboard consumes status from every area. [Shared Tools](14_Shared_Tools.md) owns proposals, history, and dependency comparisons. [Connections & Jobs](13_Connections_and_Jobs.md) owns job outcomes and costs. [Review & Export](12_Review_and_Export.md) owns delivery approval. [Lyrics & Sync](04_Lyrics_and_Sync.md) owns whole-map approval.

A progress projection must be reproducible from these authoritative records. No user action on a dashboard card may set an independent boolean that disagrees with its owning artifact.

## Acceptance criteria

| ID | Criterion | PRD trace |
| --- | --- | --- |
| DASH-AC01 | Every stage remains navigable regardless of its execution prerequisites. | AC-05; Section 6.4 |
| DASH-AC02 | Continue opens an eligible existing task and does not submit paid work by itself. | AC-07; Section 11.3 |
| DASH-AC03 | Dashboard approval/validity summaries match the exact source records and revisions. | Section 13; AC-11 |
| DASH-AC04 | Multiple takes for one shot do not count as several completed planned shots. | SHT-03; Section 9 |
| DASH-AC05 | Unknown jobs remain visibly unknown and cannot be accidentally resubmitted by resuming the wizard. | AC-10; AC-17 |
| DASH-AC06 | A rendered export is distinguished from a reviewed approved delivery. | QC-02; EXP-03; AC-18 |

## Accessibility and responsive behavior

Use descriptive text with each status icon. Reading and keyboard order should follow project status, blockers, next action, preview, then activity. A live job update must not steal focus. On narrow screens, stack panels and preserve direct blocker links. The full navigation remains reachable without forcing the user through the wizard.

## Open decisions

- Exact ranking of equally eligible next actions.
- Whether creators can pin a specific production goal to influence recommendations.
- Final preview and progress-card visual design; the semantics above are required regardless of layout.

