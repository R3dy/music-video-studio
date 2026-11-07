# Music Video Production Studio

## Product Requirements Document

| Field | Value |
| --- | --- |
| Version | 0.1 |
| Status | Draft for product and engineering review |
| Date | November 6, 2026 |
| Product name | Working title; final name TBD |
| Primary format | Web application with optional user-hosted inference workers |
| Core input | A WAV recording and supplied lyrics |
| Core output | A reviewed music video assembled from individually controllable shots |

## 1. Product summary

Build a skill-driven AI production workspace that turns an existing song and its lyrics into a music video. Users can follow a guided wizard or navigate directly between dedicated workspaces for audio, alignment, creative direction, cast, storyboard, assets, shots, lip sync, editing, and delivery.

The foundation is a reviewed **Vocal Performance Map** tied to the exact source recording. The app must establish what is performed, by whom, and when before AI creative planning or video production begins. It must then verify the generated visual performance separately.

Users bring their own inference providers, API accounts, hosted models, or imported media. They can produce and finish one shot at a time, inspect its quality and cost, and stop without committing to the rest of the video.

The first release focuses on music videos. The internal root entity is `Production`, with `production_type = music_video`. The model may later support films or other productions, but general filmmaking is outside the initial scope.

## 2. Problem statement

Existing generation workflows can produce attractive footage while giving users insufficient control over lyric timing, vocal overlaps, performer assignment, and the quality of the resulting mouth movement. Incorrect alignment can propagate into storyboards, shot crops, generated performances, and the final edit.

The user also needs an economical iteration loop. A successful visual take should survive a failed lip-sync pass. Generating one shot should not require funding or submitting an entire sequence. Provider choice should remain under user control.

The product must solve four connected problems:

1. Establish a trustworthy, editable representation of the recorded vocal performance.
2. Carry that timing and performer identity consistently through production and editing.
3. Make every generated result independently reviewable, replaceable, and traceable.
4. Provide guided AI assistance without making project state dependent on chat history.

## 3. Goals and non-goals

### 3.1 Goals

- Make accurate vocal alignment a prerequisite for AI creative planning and production.
- Support natural singing and rap, including held vowels, rapid articulation, ad-libs, and overlapping voices.
- Provide dedicated views for every production stage.
- Make the wizard and direct navigation operate on the same project objects and actions.
- Allow one-shot generation, lip-sync repair, review, placement, and export.
- Preserve approved work when users revise upstream inputs.
- Support user-owned inference connections through capability-aware adapters.
- Give users explicit control over generation scope, retries, and spending.
- Export a finished video and enough structured project information to continue work elsewhere.

### 3.2 Non-goals for the first release

- Composing, rewriting, mastering, or replacing the input song.
- Guaranteeing that arbitrary generation models produce perfect lip sync.
- Training a proprietary video or lip-sync foundation model.
- Building a full professional nonlinear editor, compositor, or digital audio workstation.
- Providing unrestricted simultaneous multi-face singing through every provider.
- Automatically generating a complete video without review checkpoints.
- Building a marketplace for inference credits, models, or skills.
- Supporting general film production, collaborative editing, or sophisticated crowd simulation.

## 4. Users and primary jobs

| User | Primary job |
| --- | --- |
| Independent artist or creator | Turn an existing recording into a convincing music video while controlling appearance, performance, and cost. |
| Director or editor | Plan coverage, compare takes, preserve continuity, and assemble an intentional edit. |
| Technical creator | Connect owned models or API accounts, inspect generation inputs, and replace pipeline components. |

The MVP assumes one project owner. Collaboration roles and shared approval workflows can be added later.

### 4.1 Representative use case

A smooth R&B singer performs the chorus while a rapper supplies responses and ad-libs. Some responses overlap sustained sung words. The creator wants consistent faces, wardrobe, tattoos, accessories, locations, and performance style across multiple short shots. They need to validate a difficult excerpt before spending on the remaining footage.

The application must preserve the distinct vocal performances, bind them to the correct visible people, and permit a lip-sync repair without losing an otherwise successful take.

## 5. Product principles

1. **The recording is authoritative.** Preserve the source WAV and its timing. Derived media must retain a known relationship to it.
2. **Alignment and rendered performance are separate approvals.** An approved map does not automatically approve generated mouth movement.
3. **One project, two navigation modes.** The wizard guides the same workspaces available through direct navigation.
4. **Every stage is inspectable.** Users can open any workspace and understand its inputs, outputs, status, and blockers.
5. **One shot is a complete unit of work.** No mandatory batch purchase or generation.
6. **Approved outputs are preserved.** Repairs and retries create new candidates or revisions.
7. **Changes have explicit consequences.** Show affected dependencies before applying upstream revisions.
8. **Skills operate on structured state.** Application rules enforce prerequisites, budgets, and approval boundaries.
9. **Uncertainty remains visible.** Model confidence and timestamp precision must not be presented as proof of accuracy.

## 6. Information architecture and navigation

### 6.1 Project shell

The project shell contains:

- A persistent stage navigator with completion state and unresolved-item counts.
- A shared playback transport and master-song time cursor.
- A context panel for the selected lyric span, asset, shot, take, or timeline clip.
- A contextual AI assistant with access to relevant structured project state.
- Generation queue, cost/runtime information, and connection status.
- Revision history, undo for reversible edits, and autosave status.
- A wizard control showing the next eligible action and its prerequisites.

Selecting a lyric span should reveal its linked shots. Selecting a timeline clip should reveal its take, shot, song interval, and relevant lyric events.

### 6.2 Workspaces

| Workspace | Required views | Primary outputs |
| --- | --- | --- |
| Project dashboard | Progress, blockers, active cut, running jobs, spend, next action | Project status and recommended next step |
| 1. Song | Source metadata, waveform, stems, sections, optional beat map | Approved audio source and derived analysis |
| 2. Lyrics & Sync | Transcript comparison, vocal lanes, timing editor, uncertainty queue, approvals | Approved Vocal Performance Map |
| 3. Brief & Outline | Treatment, visual direction, performance/story balance, scene outline | Creative brief and scene plan |
| 4. Cast & World | Cast, character identities, wardrobe, locations, props, continuity rules | Approved visual and performance references |
| 5. Storyboard & Animatic | Shot cards, storyboard frames, timed playback, coverage map | Planned shots and a timed visual preview |
| 6. Assets | Library, categories, versions, usage references, approval status | Reusable production assets |
| 7. Shot Studio | Shot detail, prompt/settings editor, takes, comparison, job history | Generated or imported takes |
| 8. Lip-Sync Lab | Performer binding, audio packet, face selection, sync passes, frame review | Reviewed lip-sync results |
| 9. Timeline | Video tracks, locked master music, trims, transitions, alternate cuts | Editorial sequence |
| 10. Review & Export | Issue list, coverage, continuity, sync checks, export profiles | Approved delivery and project package |
| Connections & Jobs | Provider configuration, capabilities, queue, failures, spend | Working inference connections and job records |

Assets are shared across the application. The Assets workspace is an inventory and management view, not a mandatory point at which assets move between stages.

### 6.3 Wizard behavior

- Use the same actions and records as direct navigation.
- Explain the current step, its required inputs, and its completion conditions.
- Recommend the next eligible action without hiding other workspaces.
- Resume from saved project state after a session ends.
- Permit manual operation of each core step without requiring chat.
- Never mark an artifact approved merely because its generating job completed.

### 6.4 Navigation and execution gates

All workspaces remain accessible. AI treatment generation, storyboard generation, and production actions are blocked until the current whole-song performance map is approved. The same gate must be enforced for UI controls, API requests, and AI skill execution, against the exact input revision of the requested action.

Users may inspect requirements, import source material, configure connections, and manage existing reusable assets before that approval. If the map is later revised, previously generated work remains accessible. The application evaluates affected dependencies and blocks actions that require an approval no longer valid for their inputs.

Region-level approval is supported in the underlying model for review and future workflows. The MVP production policy still requires whole-song map approval before creative generation.

## 7. Core user journeys

### 7.1 Create a production

1. Create a music-video project.
2. Upload the source WAV and supplied lyrics.
3. Import available vocal stems or request separation through a configured provider.
4. Inspect the source duration, playback, and derived-media alignment.
5. Confirm the source recording for this production revision.

### 7.2 Establish the vocal performance map

1. Preserve the supplied lyrics unchanged as an input revision.
2. Propose a transcript of the actual performed words and vocal events.
3. Show omissions, additions, repetitions, and pronunciation differences.
4. Resolve discrepancies and assign vocal lanes.
5. Run alignment against the reconciled performance transcript.
6. Review uncertain regions using waveform, spectrogram, loops, and slowed playback.
7. Correct boundaries, pronunciations, vocal assignments, and nonverbal events.
8. Approve sections and then the complete map.

### 7.3 Plan the video

1. Develop a brief using the approved map and song structure.
2. Define cast, visual identities, locations, wardrobe, props, and performance direction.
3. Create a scene outline and shot coverage plan.
4. Develop storyboard frames and play the animatic against the master song.
5. Resolve missing coverage and approve the planned material for production.

### 7.4 Complete one shot

1. Select a shot and its exact song interval.
2. Assign visible cast and vocal-lane bindings.
3. Review references, framing, movement, performance instructions, and estimated cost.
4. Generate or import one visual take.
5. Run the required lip-sync operation or inspect integrated audio-driven generation.
6. Compare candidates and review the result against the corresponding master audio.
7. Approve the take or repair only the failing stage.
8. Place the result into a cut and export a preview or final excerpt.

No step in this journey may automatically start another shot.

### 7.5 Revise upstream work

1. Edit a source annotation, performance binding, reference, or recording revision.
2. Review the dependency impact: unaffected, requires review, or requires regeneration.
3. Apply the change while preserving prior revisions and approvals.
4. Repair or reapprove affected work.
5. Continue from the updated project state.

## 8. Functional requirements

Priority definitions: **P0** is required for MVP release; **P1** follows after the core workflow is validated; **P2** is an extension.

### 8.1 Song ingestion and audio preparation

| ID | Priority | Requirement |
| --- | --- | --- |
| AUD-01 | P0 | Accept WAV input and retain the original bytes, checksum, duration, sample rate, and channel metadata. |
| AUD-02 | P0 | Create derived playback/analysis media without silently trimming, stretching, or replacing the original recording. |
| AUD-03 | P0 | Support imported stems and optional provider-based separation. Record source revision, origin, offsets, and transformations. |
| AUD-04 | P0 | Provide full-mix and selected-stem playback against the same master timebase. |
| AUD-05 | P0 | Support editable song-section markers and instrumental intervals. |
| AUD-06 | P1 | Propose beat, tempo-change, energy, and musical-event markers for editing and performance planning. |

Source separation is not assumed to recover clean individual voices from every mixed recording. Unresolved overlaps must remain visible and may require user-provided stems or an alternative production approach.

### 8.2 Lyrics and Vocal Performance Map

| ID | Priority | Requirement |
| --- | --- | --- |
| MAP-01 | P0 | Store supplied lyrics separately from the reconciled performed transcript. |
| MAP-02 | P0 | Represent song sections, phrases, words, syllables, phonemes, and relevant nonverbal vocal events. |
| MAP-03 | P0 | Support simultaneous vocal lanes; the MVP editor must support at least two independently timed lanes. |
| MAP-04 | P0 | Give repeated phrases and choruses distinct occurrence IDs and timings. |
| MAP-05 | P0 | Support pronunciation overrides, including sung pronunciation, names, acronyms, and slang. |
| MAP-06 | P0 | Offer waveform, spectrogram, looping, slowed playback, boundary dragging, and precise keyboard adjustment. |
| MAP-07 | P0 | Track alignment provenance, confidence when available, uncertainty, corrections, and approval separately. |
| MAP-08 | P0 | Maintain an unresolved-items queue covering missing text, uncertain timing, and unassigned vocals. |
| MAP-09 | P0 | Require review of blocking issues and explicit whole-map approval before creative generation. |
| MAP-10 | P0 | Export the canonical structured map and line/word timing formats where representable. |
| MAP-11 | P1 | Compare multiple alignment proposals for difficult passages. |

The canonical map must retain held vowels, breaths, ad-libs, laughter, and meaningful silence. A syllable extending across several notes is not automatically several syllables or phonemes.

Phoneme timing and visible mouth configuration are related but distinct. The app must not assume one discrete mouth pose per phoneme or require every visible movement to begin at the exact acoustic onset.

### 8.3 Brief, cast, scenes, and continuity

| ID | Priority | Requirement |
| --- | --- | --- |
| DIR-01 | P0 | Capture concept, audience, mood, visual style, output format, and performance/narrative balance. |
| DIR-02 | P0 | Create a scene outline linked to song sections and intended emotional or narrative progression. |
| CAST-01 | P0 | Separate reusable character identity from participation in a particular production. |
| CAST-02 | P0 | Store approved reference images and appearance constraints, including wardrobe, hair, accessories, and distinctive features. |
| CAST-03 | P0 | Represent leads, supporting cast, extras, and basic extra groups. |
| CAST-04 | P0 | Bind recorded vocal lanes to visible cast members for specified intervals. |
| CAST-05 | P0 | Capture performance direction such as posture, gaze, gestures, expression, intensity, and movement. |
| SCN-01 | P0 | Define scene location, cast, wardrobe, lighting, props, action, and continuity requirements. |
| SCN-02 | P0 | Allow an edit to revisit a scene at multiple noncontiguous song intervals. |

Recorded vocalist identity and visible character identity must not be the same database field. A character may portray a voice, and a visible extra may have no vocal assignment.

### 8.4 Storyboard, animatic, and assets

| ID | Priority | Requirement |
| --- | --- | --- |
| BRD-01 | P0 | Define shots with intended song coverage, framing, camera motion, blocking, cast, references, and lip-sync requirements. |
| BRD-02 | P0 | Support imported or generated storyboard frames and text-only placeholders. |
| BRD-03 | P0 | Play a timed animatic against the master recording before video generation. |
| BRD-04 | P0 | Show performance coverage, narrative coverage, gaps, and overlapping shot plans. |
| AST-01 | P0 | Manage reusable images, clips, audio, locations, props, and performance references with immutable media revisions. |
| AST-02 | P0 | Show where each asset revision is used and which references are approved. |
| AST-03 | P1 | Reuse production assets across projects through a shared library. |

### 8.5 Shot Studio

| ID | Priority | Requirement |
| --- | --- | --- |
| SHT-01 | P0 | Generate exactly one selected shot/take without starting other jobs beyond the explicitly selected workflow. |
| SHT-02 | P0 | Support manual import of a take created outside the application. |
| SHT-03 | P0 | Keep multiple takes and derived versions; support comparison and explicit selection. |
| SHT-04 | P0 | Store provider, model/version when available, prompts, references, settings, seeds when supported, and input revisions. |
| SHT-05 | P0 | Show the scope and available cost/runtime estimate before a generation request. |
| SHT-06 | P0 | Validate duration, frame rate, resolution, usable interval, and audio relationship of returned media. |
| SHT-07 | P0 | Preserve separate visual generation and lip-sync outputs so either can be reviewed or repaired independently. |
| SHT-08 | P0 | Support additional head/tail context around intended edit boundaries and record the mapping to usable frames. |
| SHT-09 | P1 | Offer explicitly selected batches with a clear scope, spending limit, and stop conditions. |

Typical generated footage may be 5–15 seconds, depending on the provider. This is a planning default, not a fixed restriction on shot or timeline-clip duration.

### 8.6 Lip-Sync Lab and audition gate

| ID | Priority | Requirement |
| --- | --- | --- |
| SYN-01 | P0 | Construct an exact shot audio packet containing selected vocal audio, full-mix review audio, relevant events, performer bindings, source offsets, context handles, and map revision. |
| SYN-02 | P0 | Support separate lip-sync processing and integrated audio-conditioned generation through adapter-specific workflows. |
| SYN-03 | P0 | Preflight target face selection, visible face size, pose, obstructions, audio quality, and provider constraints. |
| SYN-04 | P0 | Provide frame inspection, looping, normal-speed review, and comparison between source and processed takes. |
| SYN-05 | P0 | Keep timing/articulation, identity, expression/body performance, continuity, and technical validity as separate review dimensions. |
| SYN-06 | P0 | Allow a lip-sync retry against an existing take without overwriting it or requiring full visual regeneration. |
| SYN-07 | P0 | Require user approval of rendered vocal performances; automated scores cannot approve them by themselves. |
| SYN-08 | P0 | Track timing transformations and reject or flag unexplained offsets, trimming, looping, stretching, or duration mismatch. |
| SYN-09 | P1 | Add automated diagnostics for offset, drift, silence behavior, face assignment, and likely visual artifacts. |

Before offering batch generation, the product must require a reviewed performance audition for the relevant cast/reference/provider configuration. The audition is an ordinary reusable take, not disposable work. Material changes to that configuration require a new review.

Simultaneous vocal lanes are required in the map. Simultaneous lip syncing of multiple visible faces is capability-dependent. Unsupported combinations must be blocked with an explanation and alternatives, such as separate coverage or imported footage; they must not be flattened into one voice assignment.

### 8.7 Timeline, review, and export

| ID | Priority | Requirement |
| --- | --- | --- |
| EDT-01 | P0 | Assemble selected takes into a cut with source in/out points, sequence placement, and non-destructive trimming. |
| EDT-02 | P0 | Keep the original song as the authoritative music track; generated audio is muted or excluded by default. |
| EDT-03 | P0 | Preserve music-relative sync bindings through trims and placement. Warn or block moves that break required performance sync. |
| EDT-04 | P0 | Support basic transitions, alternative cuts, and preview of neighboring shots. |
| EDT-05 | P0 | Do not silently speed-adjust singing footage to fit a timeline slot. |
| QC-01 | P0 | Show missing coverage, unresolved dependencies, unapproved performance spans, and technical export issues. |
| QC-02 | P0 | Distinguish draft exports from approved final delivery. |
| EXP-01 | P0 | Export a finished video for a selected aspect ratio, resolution, and frame-rate profile. |
| EXP-02 | P0 | Export structured project metadata, performance map, selected media, and timing/subtitle files. |
| EXP-03 | P0 | Review the actual encoded result for duration, source-audio relationship, offsets, and drift. |
| EXP-04 | P1 | Add tested interchange with external editors and independently reviewed alternate-aspect-ratio cuts. |

The MVP project package is an app-defined, documented format. Compatibility with a specific professional editor must not be claimed until its interchange path is implemented and tested.

## 9. Canonical data model

This section defines conceptual entities and relationships, not a prescribed database or framework.

| Entity | Key fields or relationships |
| --- | --- |
| `Production` | ID, title, production type, owner, active source/map revisions, creative settings, selected cut |
| `AudioMasterRevision` | Production ID, immutable asset reference, checksum, sample rate, channels, sample count |
| `DerivedAudio` | Source master revision, stem/analysis role, source offset, sample rate, transformations, asset revision |
| `VocalLane` | Production ID, recorded vocalist label, role, associated stem, language/pronunciation context |
| `PerformanceMapRevision` | Master revision, transcript revision, event set, provenance, review state |
| `VocalEvent` | Stable occurrence ID, lane, event type, parent event, text/phone, start/end samples, uncertainty, provenance |
| `CreativeBriefRevision` | Production ID, concept, style, narrative, intended format, referenced map revision |
| `Character` | Reusable identity, description, appearance references, identity constraints |
| `CastMember` | Production ID, character ID or extra-group definition, role, performance direction |
| `PerformanceBinding` | Vocal lane, cast member, song interval, shot scope when needed, target face reference |
| `Scene` | Production ID, intent, setting, cast participation, location, wardrobe, props, continuity rules |
| `ShotRevision` | Scene ID, intended song interval, shot type, framing, motion, blocking, performance bindings, referenced assets |
| `Take` | Shot revision, source/import provenance, base media, candidate status, parent take when derived |
| `TakeVersion` | Take ID, media revision, applied processing, job references, usable interval, time mapping |
| `AssetRevision` | Stable asset ID, immutable file/object reference, media metadata, checksum, provenance |
| `GenerationJob` | Task type, adapter/connection, input snapshot, provider job ID, status, attempts, outputs, cost/runtime |
| `Cut` | Production ID, name, output profile, master-audio binding, sequence revision |
| `TimelineClip` | Cut ID, take version or asset revision, source in/out, sequence in/out, track, transformations |
| `Review` / `Approval` | Target revision, review dimension, interval if applicable, outcome, reviewer, time, notes |
| `SkillDefinition` / `SkillRun` | Contract and version; input snapshot, proposed changes, validations, outputs, execution state |
| `Dependency` | Source revision/span, consuming artifact, dependency type, impact classification |

### 9.1 Relationship rules

- A production has one active master recording revision for a given edit; previous revisions remain addressable.
- A performance map references one exact master recording revision.
- A vocal lane contains many events; events in different lanes may overlap.
- A production contains scenes; a scene contains planned shots.
- A scene can recur throughout a cut without being duplicated solely for editorial order.
- A shot can have many takes. A take can have multiple processed versions.
- A generation job is an execution record, not a shot or a media file. A failed job may produce no media.
- Timeline clips reference exact media versions. One take may be used in multiple cuts.
- Cast participation in scenes and shots is many-to-many.
- Approvals reference exact artifact revisions and, when appropriate, exact time intervals.

### 9.2 Shot types

At minimum, support `performance`, `narrative`, `dance`, `abstract`, and `b_roll` shot types. Lip-sync requirements are explicit per shot and visible performer; they are not inferred solely from the presence of vocals in the song.

## 10. Timing and media invariants

1. The immutable master recording is the canonical clock.
2. Store source annotation boundaries as integer sample positions with the associated sample rate. Use half-open intervals: `[start, end)`.
3. Store video rates and conversions with sufficient precision to represent rational rates; do not accumulate rounded per-clip durations.
4. Keep source-song time, generated-media-local time, and sequence time distinct.
5. Preserve explicit transformations for resampling, offsets, trims, provider output, and export.
6. Quantize to video frames only at the appropriate rendering or presentation boundary, with a defined rounding policy.
7. Audio stems and analysis proxies must retain their relationship to the master.
8. Lip-sync context handles must not change the intended in/out points of the placed clip.
9. Do not silently loop, stretch, or retime performance media to satisfy a provider or sequence duration.
10. The final encoded file must be checked; a synchronized preview alone is insufficient.

These invariants should be enforced by deterministic media and validation services rather than inferred by an LLM.

## 11. Skills and orchestration

### 11.1 Skill contract

Every executable skill must declare:

- Stable identifier and version.
- Required input schemas and prerequisite approval states.
- Tools and provider capabilities it may use.
- Object types and fields it may propose or modify.
- Structured output schema and validation rules.
- Whether the action can incur cost and how scope/limits are determined.
- Human review requirements.
- Retry, cancellation, checkpoint, and resume behavior.

Application orchestration must enforce these contracts. Skill instructions alone must not authorize extra spending, bypass approval, or overwrite approved artifacts.

### 11.2 Initial skills

| Skill | Inputs | Outputs |
| --- | --- | --- |
| Prepare song | Master recording, optional stems | Validated derived audio, section proposals |
| Reconcile lyrics | Supplied lyrics, audio observations | Proposed performed transcript and discrepancy list |
| Align performance | Reconciled transcript, vocal lanes, audio | Timing proposal and uncertainty queue |
| Develop treatment | Approved map, song structure, creative preferences | Brief and scene-outline proposal |
| Define cast and world | Brief, references, creator input | Cast/world specifications and asset requirements |
| Plan coverage | Brief, scenes, approved map | Shot plan and coverage checks |
| Build animatic | Shot plan, frames, master recording | Timed storyboard preview |
| Generate take | Approved inputs, shot, connection, budget scope | Job record and candidate media |
| Synchronize performance | Visual take, audio packet, face bindings | Processed candidate version |
| Review performance | Candidate media, reference audio/map | Findings and review suggestions |
| Assemble rough cut | Shot plan, selected takes, master | Proposed cut |
| Validate delivery | Cut, approvals, export profile | Export checks and delivery record |

### 11.3 AI interaction rules

- Store authoritative project state outside chat.
- Present structured changes with a clear scope before applying significant revisions.
- Allow users to accept, edit, or reject creative proposals.
- A direct request to generate one take authorizes that bounded job or clearly displayed workflow, not indefinite retries.
- Automatic retries must obey explicit policies, cost limits, and provider duplicate-submission constraints.
- Preserve the input snapshot and skill/model versions needed to explain each result.

## 12. Inference connections and job execution

### 12.1 Supported connection categories

- User-owned commercial API accounts.
- User-hosted inference services.
- Local/desktop inference workers connected through a companion or worker service.
- Manual exchange: export a request package and import generated media.

The web application must not assume it can directly reach a user's local model from its server. The transport design for local workers remains an implementation decision.

### 12.2 Adapter capabilities

Each adapter declares supported task types and relevant constraints, including:

- Input media and reference formats.
- Minimum/maximum duration and duration increments.
- Resolution, aspect ratio, frame rate, and output format.
- Audio conditioning and lyric/phoneme control, if actually supported.
- Single/multiple-face selection and simultaneous-performer support.
- Image references, masks, camera/motion controls, and seed support.
- Asynchronous jobs, status lookup, callbacks, cancellation, and retry semantics.
- Pricing information when available, rate limits, and concurrency limits.

Unsupported controls must be disabled or explained. The system must not claim that a provider consumes the performance map when it only accepts audio and video.

### 12.3 Job state and spend controls

Track `draft`, `queued`, `submitted`, `running`, `succeeded`, `failed`, `cancel_requested`, `canceled`, and `unknown` as appropriate to the adapter.

- Record provider job IDs and request identifiers.
- Use provider idempotency features where available and application duplicate protection in all cases.
- On a timeout after submission, query the existing job when possible. An unknown outcome must not trigger a second chargeable submission automatically.
- Distinguish local cancellation requests from provider-confirmed cancellation.
- Show estimated and actual cost separately; unavailable cost is `unknown`, not zero.
- Store credentials as secrets outside prompts, logs containing user-visible content, and exported projects.
- Stop submission of additional work when a spending or runtime limit is reached.
- A successful job with an invalid output must retain its cost and history while the output is marked unusable.

## 13. Revisions, approvals, and dependency impact

Artifact review state and dependency validity are separate dimensions. For example, a take may remain historically approved while being stale for the current cut's inputs.

Recommended artifact review states are `draft`, `needs_review`, `approved`, and `rejected`. Dependency validity is `current` or `stale`, with a reason and affected interval.

### 13.1 Change classification

| Change | Expected consequence |
| --- | --- |
| Correct spelling without changing pronunciation or timing | Update text-dependent outputs; do not automatically regenerate video. |
| Adjust a word/phone boundary | Reassess consumers of that event or interval; do not invalidate unrelated assets. |
| Change a vocal lane or visible performer assignment | Re-review affected audio packets, bindings, and sync passes. |
| Change a reference image or wardrobe rule | Flag shots depending on that revision; preserve old takes. |
| Change the actual audio crop or timing transform | Revalidate or rerun affected sync processing and placement. |
| Replace the master recording | Create a new source revision, require a corresponding approved map, and assess all dependent timing. |

Changing a map revision must not automatically invalidate every consumer solely because the map version number changed. Impact analysis should use actual content, timing, and dependency differences.

No upstream edit may silently delete media, overwrite an approved version, or launch paid regeneration.

## 14. Quality definition and measurement

### 14.1 Alignment quality

Map approval means the user has reviewed the represented performance and resolved blocking issues. It is a workflow approval, not a mathematical claim that every acoustic boundary is uniquely measurable.

Measure:

- Missing, extra, or incorrectly repeated lyric events.
- Vocal-lane assignment errors.
- Word-boundary and phone-boundary errors separately.
- Median, 95th-percentile, and worst-case error against reviewed reference annotations.
- Human annotator disagreement and uncertainty for ambiguous boundaries.
- Time required to reach an approved map.

Do not use transcription accuracy alone as a proxy for timing accuracy.

### 14.2 Rendered performance quality

Review timing/articulation, identity, expression, continuity, and technical validity separately. Inspect constant offset and drift across the beginning, middle, and end of a shot and the exported cut.

An initial proposed engineering target is no residual timing error greater than one output frame at manually reviewed, unambiguous visual sync landmarks. This target must be validated on singing and rap examples before becoming a release threshold. Natural anticipatory mouth movement and ambiguous acoustic boundaries must be considered; there is no universal visible event for every phoneme.

Automated sync scores are diagnostic signals. They must not replace user approval or be displayed as a universal percentage of perfection. Evaluation must examine the delivered timing; it must not silently realign a clip and then report the original as synchronized.

### 14.3 Benchmark set

Include slow R&B, rapid rap, held vowels, melisma, overlapping ad-libs, repeated choruses with different delivery, quiet entries, breaths, silence, and instrumental passages. Visual examples should include intended production conditions such as glasses, facial hair, gestures, microphones, and changing face angles.

Benchmark provider/model configurations before recommending them for a particular production style. Final adapter selection and numerical alignment thresholds remain decisions to validate, not assumed performance claims.

## 15. MVP scope and delivery milestones

### 15.1 Milestone 1: prove the core capability

Deliver WAV plus lyrics to approved whole-song map, then one convincing performance shot, precise timeline placement, and an exported excerpt. Include two vocal lanes, manual timing correction, one generation/import path, one lip-sync path, and preserved take history.

Exit condition: the workflow succeeds on representative singing and rap material, and users can repair the failing stage without starting over or generating unrelated shots.

### 15.2 Milestone 2: complete the production workspace

Add the brief, cast/world, scene outline, storyboard/animatic, asset inventory, coverage view, and basic full-song timeline. Implement dependency impact and the guided wizard over the same actions.

Exit condition: users can plan and finish a multi-scene music video with reviewed individual takes and complete source-to-export traceability.

### 15.3 Milestone 3: reliability and delivery

Harden connection management, resumable jobs, duplicate-submission protection, cost/runtime reporting, export validation, and portable project packaging.

Exit condition: all P0 requirements and release acceptance criteria pass. Milestone order is a suggested delivery sequence, not a calendar estimate.

### 15.4 Subsequent enhancements

- Optional batches after successful auditions.
- Multiple alignment engines and automated sync diagnostics.
- Beat/energy-driven editing suggestions.
- More provider adapters and external-editor interchange.
- Cross-project asset reuse, alternate output cuts, and collaboration.
- Advanced multi-face performance and compositing where supported.

## 16. Release acceptance criteria

| ID | Scenario | Required result |
| --- | --- | --- |
| AC-01 | A user uploads a WAV and supplied lyrics. | Original files are preserved; all derived media and annotations identify the exact recording revision. |
| AC-02 | Lyrics omit an ad-lib or contain an extra repeated line. | The discrepancy is visible and must be resolved before whole-map approval. |
| AC-03 | A singer holds a note while a rapper responds. | Overlapping events remain in separate lanes and can bind to different visible performers. |
| AC-04 | Two choruses contain the same words with different delivery. | Each occurrence retains its own event IDs and timings. |
| AC-05 | The current map is unapproved and a user opens Storyboard or requests production through the UI, API, or a skill. | The workspace opens and explains prerequisites; treatment/storyboard generation and production remain blocked through every execution path. |
| AC-06 | A user switches between the wizard and direct navigation. | Both modes display and modify the same project state, approvals, and selected objects. |
| AC-07 | A user generates one take. | Only the selected bounded workflow executes; no other shot or hidden retry is submitted. |
| AC-08 | The visual take is good but its lip sync is poor. | A new sync pass can reuse the visual take while preserving previous outputs and costs. |
| AC-09 | A provider cannot sync two visible faces simultaneously. | The app explains the limitation and blocks the unsupported request rather than misassigning the audio. |
| AC-10 | A provider submission times out after potentially starting. | The job is reconciled or marked unknown; no automatic duplicate paid request is created. |
| AC-11 | A user corrects two timing boundaries. | Affected consumers are identified; unrelated assets stay valid and no paid regeneration starts automatically. |
| AC-12 | A clip with context handles is trimmed and placed. | The intended source-song interval remains aligned through preview and export. |
| AC-13 | A user moves a lip-synced clip to incompatible song time. | The app warns or blocks the move unless an explicit valid timing relationship is established. |
| AC-14 | A provider returns unexpected duration, rate, or timing. | The mismatch is detected and reviewed before the output can be approved for use. |
| AC-15 | An automatic metric rates a visibly poor performance highly. | Human rejection remains authoritative; the metric cannot mark the take approved. |
| AC-16 | A user cancels a running provider job. | The UI distinguishes the request from confirmed cancellation and retains the eventual cost/outcome. |
| AC-17 | A project session is interrupted. | Saved work and job identifiers survive; the user can resume without resubmitting completed or uncertain jobs. |
| AC-18 | A user exports an approved final cut. | Required approvals and dependencies are current, source-song timing is preserved, and the actual encoded output is checked at the beginning, middle, end, and edited joins for offsets or drift. |
| AC-19 | A user exports a project package. | Selected media and structured relationships are included; secrets and credentials are excluded. |
| AC-20 | A user finishes only one shot. | They can review and export it without producing or funding the remainder of the video. |

## 17. Nonfunctional requirements

- **Durability:** autosave structured edits; retain immutable source and generated-media revisions; support recovery after interrupted sessions.
- **Responsiveness:** local editing and navigation must not wait for remote generation. Show progress and queued work independently.
- **Media handling:** use suitable playback proxies and range loading; preserve original-quality media for final delivery.
- **Accessibility:** provide keyboard operation for critical timing/editing actions, labeled controls, and status indicators beyond color alone.
- **Security:** protect credentials, isolate project access, validate uploaded/returned media, and authorize workers and provider calls through defined capabilities.
- **Observability:** retain action history, skill runs, job IDs, relevant errors, timing transformations, and estimated/actual cost without exposing secrets.
- **Reproducibility:** record inputs and settings; do not promise bit-identical regeneration from nondeterministic or changing providers.
- **Portability:** document project-export schemas and preserve stable identifiers between objects and media.
- **Performance targets:** determine supported song lengths, file sizes, browser/device profiles, and interaction latency targets during technical validation.

## 18. Success metrics

| Metric | Purpose |
| --- | --- |
| Time to approved performance map | Measures whether precision is achievable without excessive manual work. |
| Word/phone boundary error distribution | Measures alignment quality independently of transcription quality. |
| First-pass and eventual take approval rates | Distinguishes initial generation quality from repair effectiveness. |
| Cost per approved second of footage | Captures the economics of useful output, including unsuccessful attempts. |
| Share of sync failures repaired without full video regeneration | Measures the value of independent processing stages. |
| Unexpected chargeable submissions | Target: zero application-caused submissions outside authorized scope. |
| Export timing/placement regressions | Target: zero known unresolved regressions in approved delivery. |
| One-shot completion rate | Measures whether the smallest useful workflow succeeds. |
| Rework caused by upstream changes | Measures the effectiveness of selective dependency handling. |

Baseline values and improvement targets must be established through representative pilot projects. No model-quality baseline is assumed in this draft.

## 19. Risks and open decisions

| Topic | Current position / decision needed |
| --- | --- |
| Product name | Working title only. |
| Initial providers | Select a small set after singing/rap benchmarks; retain adapter extensibility. |
| Hosted versus local execution | Support both conceptually; choose the first deployment and worker transport. |
| Initial languages | Decide supported alignment languages and pronunciation workflows before launch. |
| Stem quality | Original stems are preferable when available; separated audio may remain unsuitable for some overlaps. |
| Multi-face singing | Capability-dependent; independent vocal lanes do not imply all providers can render simultaneous performers. |
| Precision thresholds | Validate alignment and visual-sync targets against reviewed benchmarks and annotator agreement. |
| Resource limits | Define supported duration, source sizes, storage policy, and concurrency after implementation tests. |
| Output profiles | Choose initial aspect ratios, resolutions, codecs, and frame rates; test their timing conversions. |
| Editor interchange | Start with a documented project package; select and test specific editor formats later. |
| Regional approvals | Data model supports them; MVP production still requires whole-song approval. |
| Model/API changes | Pin versions where possible and revalidate adapters when behavior changes. |

## 20. Research references and implementation caveats

The following primary sources informed the design. They are examples to evaluate, not a commitment to particular vendors or a guarantee of quality on arbitrary songs.

1. [WhisperX official repository](https://github.com/m-bain/whisperX): word alignment and diarization capabilities; documented limitations include overlapping speech, imperfect diarization, and alignment dictionary/language constraints.
2. [Montreal Forced Aligner documentation](https://montreal-forced-aligner.readthedocs.io/en/latest/user_guide/corpus_structure.html): audio/transcript preparation, pronunciation resources, and speaker-specific annotation structures.
3. [A Strategy for Improved Phone-Level Lyrics-to-Audio Alignment for Speech-to-Singing Synthesis](https://www.isca-archive.org/interspeech_2019/ayllon19b_interspeech.pdf): singing-specific alignment challenges and the role of human verification in precise phone-level work. Reported research results are study-specific.
4. [Sync: Improving Lip Sync Quality](https://sync.so/docs/compatibility-and-tips/improving-lip-sync-quality): isolated vocal input for songs, individual-speaker audio, face-selection considerations, and duration handling.
5. [LatentSync official repository](https://github.com/bytedance/LatentSync): an example of audio-conditioned lip-sync generation. A provider accepting audio does not necessarily accept external phoneme timing as a control signal.
6. [VidMuse music-video workflow](https://vidmuse.ai/feature/ai-music-video-generator): current advertised storyboard, shot-refinement, timeline, and asset workflows. This PRD does not assert that individual-shot refinement is unique to this product.

The intended differentiation is the combination of reviewed vocal timing, preserved audio relationships, independent rendered-performance approval, controlled per-shot spending, selective repair, and user-owned inference.
