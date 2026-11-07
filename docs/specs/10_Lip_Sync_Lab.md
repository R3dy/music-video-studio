# Lip-Sync Lab — Area Specification

| Field | Value |
| --- | --- |
| Version | 0.1 |
| Status | Draft for UX and engineering review |
| Date | November 6, 2026 |
| View IDs | V30–V33; four view types |
| Governing documents | [Master UI specification](00_UI_Master_Spec.md); [Product requirements](Music_Video_Studio_PRD.md) |

## Purpose and scope

Lip-Sync Lab connects the verified audio performance to the correct visible performer, prepares exact shot audio, runs a supported synchronization process, and evaluates the resulting footage. Its central output is a reviewed `TakeVersion` with an explicit relationship to the master recording.

The performance map establishes intended timing. It does not certify generated mouth movement. The lab must make it practical to retain a successful visual performance and rerun only the failing sync stage.

P0 includes performer binding, exact audio inspection, capability-aware configuration, retained processing versions, human review, and selective repair. Automated offset/drift/artifact diagnostics are P1; manual review and deterministic media validation are P0. Simultaneous vocal data is required, while simultaneous multi-face rendering remains provider-dependent.

## Entry, dependencies, and completion

Enter with a shot and selected take version from [Shot Studio](09_Shot_Studio.md), a timeline issue, or a quality-review finding. The master-song interval and selected cast remain visible throughout. Missing input is an explained state, not a navigation error.

New sync production requires current whole-song map approval, a compatible visual take, valid vocal audio and offsets, complete required bindings, a compatible adapter, and authorized job scope. Existing results remain viewable when prerequisites change. A take can also be reviewed here after integrated audio-conditioned video generation; a second processing job is not required merely because the lab is opened.

Completion requires valid media and explicit review of each required visible vocal performance over the usable interval. Export and timeline review remain separate later checks. “Approved” records who reviewed what version and interval; it never means an automatic accuracy guarantee.

## View inventory

Proposed routes identify a particular shot and optional take-version query parameter.

| ID | View | Presentation | Proposed route |
| --- | --- | --- | --- |
| V30 | Voice-to-face assignment | Video canvas with vocal and cast binding inspector | `/projects/:projectId/shots/:shotId/sync/bindings` |
| V31 | Vocal crop and timing inspection | Waveform/event editor with source mapping | `/projects/:projectId/shots/:shotId/sync/audio` |
| V32 | Sync configuration | Capability-aware setup and bounded-job review | `/projects/:projectId/shots/:shotId/sync/configure` |
| V33 | Rendered sync review and repair | Frame review, comparison, and dimensioned assessment | `/projects/:projectId/shots/:shotId/sync/review` |

## V30 — Voice-to-face assignment

Use the selected take as the primary canvas, with visible face candidates or user-specified targets when supported. Alongside it, show the source vocal lanes, their active intervals, cast names, and existing `PerformanceBinding` records. A compact lane timeline makes simultaneous lead vocals and ad-libs visible. The interface must not present overlap as a sequence of mutually exclusive speakers.

The primary action saves or confirms bindings for the selected shot interval. Secondary actions audition a lane, inspect the cast profile, scrub to a reference frame, change the supported face target, or mark a vocalist as off-screen. A silent visible extra requires no invented vocal binding. Each required visible singing interval must have a deliberate lane-to-cast relationship and an adapter-supported target representation.

Read the map, lanes, cast, take version, and adapter capabilities. Write versioned binding changes and target references without changing the recorded vocalist identity. Use stable cast IDs plus a media-version-specific target; a detection index alone must not be treated as a persistent identity across regenerated footage.

If the provider supports only one target or alternating speakers, block an unsupported simultaneous-duet request. Preserve the full map and offer coverage changes or external media import. Uncertain face identity, occlusion, or a disappearing performer remains a review issue. Suggested bindings require user review and cannot start processing.

## V31 — Vocal crop and timing inspection

Show the intended usable song range, the requested context range, the relevant vocal waveform, the full-mix reference, and words/phonemes from the approved map. Display master time and clip-local time together; samples and timecode are inspectable without making the main layout technical. A selected event reveals its exact occurrence, lane, map revision, and timing provenance.

The primary action validates and saves the audio packet. Secondary controls play isolated vocals or the original mix, loop the intended cut or its handles, inspect source offsets, and open the original annotation in [Lyrics & Sync](04_Lyrics_and_Sync.md). Editing source phoneme timing occurs in that authoritative workspace. The lab does not create an independent transcript or silently change the approved map.

The packet includes vocal media, review mix, selected events, lane/face bindings, source-master revision, sample ranges, transformations, handles, and intended usable interval. Resampling creates a derived input with an explicit conversion. A waveform that starts at local zero may still begin at a nonzero master sample; both relationships must survive serialization and reload.

Detect crops outside available stem media, missing coverage, unknown stem offsets, or mismatched master revisions. Do not automatically pad, loop, or stretch vocal audio to hide a mismatch. A provider-required duration change must have an explicit validated mapping and remain visible before submission. The unaltered master mix is the authority for final review.

## V32 — Sync configuration

Present a read-only summary of the selected visual base, validated audio packet, visible performer bindings, and intended output. Below it, expose only the adapter's supported controls: output profile, face selection, supported obstruction handling, strength or seed settings if available, and duration behavior. Explain when a model consumes audio only rather than external phoneme timing.

Preflight distinguishes hard constraints from composition risks. Missing approval, unsupported simultaneous targets, invalid media, unexplained timing transforms, incompatible duration, or exceeded limits block submission. Small faces, extreme angles, or mouth occlusion generate specific review warnings based on the chosen adapter; they must not be universal claims about every model.

The primary action is **Run 1 sync pass**, with known estimate or “Estimate unavailable,” requested scope, and remaining configured limits. Capture exact take version, packet, map/binding revisions, model/settings, and request identity. Saving settings or selecting a model does not run a job. If review concerns can be evaluated before spending, provide links back to V30/V31 or the visual take.

A completed job creates a new `TakeVersion` and proceeds to V33. A provider-unknown submission is reconciled before another chargeable request. A canceled request remains distinct from confirmed cancellation and any final charge. Existing integrated-generation output can go directly to V33 without an unnecessary paid pass. A reviewed audition uses this same flow and ordinary take records.

## V33 — Rendered sync review and repair

Place the actual processed clip beside its visual base or previous version, with linked playback at the intended song time. Use the original mix as the default audible reference and allow isolated vocals for diagnosis. Provide frame stepping, short-region loops, normal-speed playback, optional slowed inspection, and visible selection of the exact media version. Any diagnostic preview adjustment must be labeled and cannot alter the evidence used to approve the delivered version.

The review inspector separates timing/articulation, identity/detail, expression/body performance, continuity, and technical validity. Annotate specific ranges with findings such as early consonant release, incorrect sustained vowel, mouth movement in silence, wrong face, identity change, or seam artifact. A reviewer can reject sync while approving the retained visual base. The same operation must not approve untouched dimensions implicitly.

Primary actions approve required dimensions for the selected version and usable interval, or record a problem and choose a repair path. Repairs route to bindings, audio packet, configuration, or Shot Studio as appropriate. Creating another sync pass preserves previous media, reviews, and costs. A global offset correction, if supported as a deterministic repair, creates an explicit derived version and requires re-review; it does not modify the original evidence.

P1 diagnostics can propose issue locations and estimated offset/drift. They must identify assumptions and uncertainty, and may not label a performance “perfect” or grant approval. Review natural articulation rather than forcing every mouth pose to align mechanically with each phoneme. A visually unobservable consonant is not automatically a failed event. Approval still requires normal-speed inspection of the delivered usable interval, including its beginning and end.

## Skills and review authority

The synchronization skill consumes the exact visual version, validated packet, supported controls, and bounded authorization. Its output is a candidate version and execution history. A review skill proposes findings; a human decides approval. Deterministic validation verifies metadata, coverage, mappings, and prerequisites independent of either skill.

The app can suggest an audition using a difficult excerpt with rapid articulation or a held vowel. Before a P1 batch, require a reviewed result for the relevant cast/reference/provider configuration. Reusing that approved take is allowed. Changing material inputs marks the audition relationship for reassessment rather than manufacturing a separate audition asset type.

## State and recovery behavior

| State | Required behavior |
| --- | --- |
| No visual take | Explain the missing input and link to Shot Studio without blocking navigation. |
| No usable vocal source | Show the exact missing lane/offset; link to stem preparation or source import. |
| Unapproved/stale map | Preserve historical review; block new processing against ineligible current inputs. |
| Unsupported performer configuration | Explain the adapter constraint without merging vocal lanes or selecting an arbitrary face. |
| Loading/processing | Keep packet and job identity visible; distinguish playback loading from provider execution. |
| Failed/unknown job | Retain inputs and incurred cost; reconcile uncertain submissions before another request. |
| Invalid returned duration or timing | Preserve output for inspection and prevent approval until the mapping is resolved. |
| Partly reviewed result | Show reviewed and unresolved intervals/dimensions; no whole-take approval inference. |
| Upstream revision | Show the changed dependency and affected span; allow reuse of unaffected visual media. |

## Data and handoffs

`PerformanceBinding` connects a vocal lane to visible cast over a song interval. The audio packet is a proposed implementation record derived from PRD SYN-01; it references immutable inputs instead of copying them into independent authorities. `TakeVersion` captures processed media and its time mapping. `Review`/`Approval` references the exact version, dimensions, and usable intervals.

Timeline receives explicit media version, valid range, song mapping, and required review coverage. It must not infer sync readiness from a job-success flag. Review & Export receives findings still relevant after trimming, conversion, or placement. Dependency impact uses changed bindings, actual audio/crops, and referenced annotations rather than treating every map revision number as a reason to rerender.

## Acceptance criteria

| ID | Behavior | PRD traceability |
| --- | --- | --- |
| SYNC-AC01 | Overlapping lead and rapper events remain separately assignable, including off-screen vocal roles. | CAST-04, MAP-03; AC-03 |
| SYNC-AC02 | A one-face adapter cannot receive an unsupported simultaneous multi-face job through UI, API, or skills. | SYN-03; AC-09 |
| SYNC-AC03 | Every packet retains master samples, crop/handle ranges, media offsets, and map/binding revisions. | SYN-01, SYN-08; AC-12 |
| SYNC-AC04 | Source timing edits return to the authoritative map and expose resulting approval impact. | MAP-06; AC-11 |
| SYNC-AC05 | Opening or reviewing integrated output creates no unnecessary processing job. | SYN-02; AC-07 |
| SYNC-AC06 | One authorized pass produces a new candidate version and never overwrites the visual base or prior sync version. | SYN-06; AC-08 |
| SYNC-AC07 | Unknown submissions reconcile before retry; cancellation is accurately qualified. | AC-10, AC-16 |
| SYNC-AC08 | A correct map or high automatic score cannot approve generated mouth movement. | SYN-05, SYN-07; AC-15 |
| SYNC-AC09 | Output with unexplained duration or timing changes cannot be approved silently. | SYN-08; AC-14 |
| SYNC-AC10 | Review distinguishes visual-base quality from sync quality and can request the smallest valid repair. | SYN-04–SYN-07; AC-08 |
| SYNC-AC11 | Diagnostic shifts do not contaminate the measured timing of the delivered original version. | §14.2; AC-18 |

## Accessibility and responsive behavior

Provide labeled face targets with keyboard-selectable alternatives to drawing boxes. Offer numeric range editing beside drag handles. Announce the active audio source and playback speed. Keep phoneme, lane, and review labels readable and distinguishable without color alone. Ensure shortcuts do not conflict with text entry.

On narrow screens, stack the player, lanes, and inspector and permit single-player A/B review. Retain the selected time and target when switching panels. Full side-by-side inspection is optimized for desktop, but essential bindings, evidence, and approval constraints must remain available through focused layouts.

## Open design decisions

- Initial adapters, face-target formats, and supported simultaneous-performer workflows require validation.
- Numerical diagnostic thresholds and the proposed one-frame landmark target need benchmark evidence before release use.
- Exact diagnostics and automated face tracking are P1 unless required by a selected adapter's supported workflow.

