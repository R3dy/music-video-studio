# Song — Area Specification

| Field | Value |
| --- | --- |
| Version | 0.1 |
| Status | Draft for product and engineering review |
| Date | November 6, 2026 |
| Area | Song |
| Assigned views | V05–V07; 3 distinct view types |
| Parent documents | [Master UI specification](00_UI_Master_Spec.md), [Product requirements](Music_Video_Studio_PRD.md) |

## Purpose and scope

Establish the recording that every later annotation, shot, and edit refers to. Users inspect the original WAV, organize aligned stems, and mark the song's structure. The workspace must make the relationship between original audio and derived audio understandable while preserving the recording unchanged.

WAV ingestion, derived playback media, stem import/separation, synchronized audition, and manual song-section markers are P0. Automatic beat, tempo, energy, and musical-event proposals are P1 under AUD-06. These later capabilities occupy V07 without adding another view.

This area does not mix, master, rewrite, or stretch the song. Playback gain and temporary playback speed are monitoring controls; they do not modify the master or exported soundtrack.

## Entry, dependencies, and completion

Entry is available from [Project Setup](01_Project_Setup.md), the stage navigator, or an audio-related issue anywhere in the project. An empty Production may enter V05 before a recording exists. Source preparation and analysis remain available before performance-map approval.

The successful handoff is a validated, selected `AudioMasterRevision`, usable audition media, and any available `DerivedAudio` records with explicit timing relationships. Stems are helpful but are not mandatory when a user can review and align the full mix successfully. Unusable separation must remain a visible limitation rather than a false prerequisite completion.

Selecting “Continue to lyrics” opens [Lyrics & Sync](04_Lyrics_and_Sync.md) with the same song position and selected audition source. Confirming the recording is distinct from approving the performance map.

## View inventory

Routes are proposed deep links, not implemented interfaces.

| ID | View | Presentation | Proposed route |
| --- | --- | --- | --- |
| V05 | Master recording and waveform | Main Song tab with metadata inspector | `/projects/:projectId/song/source` |
| V06 | Stems | Main Song tab with aligned audio tracks | `/projects/:projectId/song/stems` |
| V07 | Song structure and musical markers | Main Song tab with marker lanes | `/projects/:projectId/song/structure` |

## V05 — Master recording and waveform

### Layout and content

The header identifies the selected master revision, original filename, duration, source validation state, and whether downstream work uses this revision. The center contains a waveform with overview and zoomed ranges, a shared playhead, selection handles, and the application transport. The inspector shows sample rate, channels, sample count, file size, and checksum on demand. Operational metadata should be accessible without occupying the main creative workspace.

The empty state offers WAV upload and an explanation that this recording becomes the timing reference. During upload, show byte progress separately from decoding or waveform preparation. Once playable media is ready, enable audition even if optional analysis remains incomplete. An unavailable waveform must not be represented as silence.

### Actions and validation

Primary actions are “Upload WAV,” “Use this recording,” and “Continue to lyrics,” depending on state. Secondary actions include auditioning a selected range, inspecting source metadata, downloading the original, and adding a new recording revision.

Validate the actual decodable media and duration, not only the filename extension. Unsupported encoding, damaged files, or incomplete transfer must produce an actionable error without creating a usable master reference. Exact supported WAV encodings and size limits remain deployment decisions.

Using a different recording creates a new `AudioMasterRevision`. If dependent work exists, open V47 change-impact review in [Shared Tools](14_Shared_Tools.md). Preserve previous master revisions, maps, and takes. The new recording must never inherit a map approval solely because it has the same filename or similar duration.

### Data and navigation

Read/write the Production's selected master reference and source asset metadata; create immutable master/media records. Waveform caches and playback proxies are derived records. Selecting a range updates shared song-time context. It does not trim the master. Changes to monitoring volume, zoom, or playback speed are view preferences.

## V06 — Stems

### Layout and content

Show the master waveform above aligned stem rows. Each row identifies source revision, role, origin, usable range, synchronization state, and processing status. Users can solo, mute, compare, rename a descriptive label, and associate a stem with a vocal lane. Names such as “Lead,” “Rapper,” and “Backing” are editable; model-assigned labels remain proposals.

Display a timing inspector for a selected stem with source offset, sampling information, and transformations. A user can listen to a transition in the mix and stem at the same song time. Multiple soloed rows may be auditioned together, but audio routing must prevent accidental doubling of the full mix and its components unless deliberately selected.

### Actions and validation

Primary actions are “Import stems” and “Separate vocals.” Separation opens an inline request summary identifying the recording, provider task, output expectation, and estimated cost or “Estimate unavailable.” Submission authorizes that bounded job. Progress and cancellation reflect the underlying `GenerationJob`; a lost response must not trigger another chargeable request automatically.

Imported stems retain their original bytes. Users provide or correct the mapping to the master when timing is unknown. The app must distinguish a known source offset from an offset merely suggested by an analysis. A stem of different length or sample rate is not automatically invalid, but its coverage and conversion must be explicit and validated.

Source separation may preserve instrumentation or fail to isolate simultaneous vocalists. Mark questionable passages for review and link them to V11. A “vocals” stem containing two people must not be silently labeled a single-performer source. Accepting a stem as useful for audition does not certify every passage for downstream lip sync.

### Data and navigation

Create `DerivedAudio` records referencing the source master, asset revision, role, origin, offset, and transformations. Keep separation outputs as candidates until accepted for their intended use. Choosing a different stem for an existing lane triggers impact analysis for dependent alignment or audio packets. Users may open the stem asset in [Assets](08_Assets.md), its provider job in [Connections & Jobs](13_Connections_and_Jobs.md), or its vocal lane in V09.

## V07 — Song structure and musical markers

### Layout and content

Present an editable section lane over the master waveform: intro, verse, chorus, bridge, instrumental break, outro, or custom labels. Each section instance has its own ID, interval, and label. Repeated labels do not imply identical timing. A section list provides numeric editing and direct playback navigation alongside the graphical lane.

P1 musical analysis appears in separate optional lanes for beats, tempo changes, energy, and suggested events. The interface must identify machine proposals and accepted markers. In the MVP, manual section work remains complete and usable without those lanes.

### Actions and validation

Primary actions add, split, rename, resize, and reorder the descriptive section structure. Moving a section boundary changes annotation only. It does not rearrange the song. Numeric entry and drag actions enforce recording bounds and positive intervals. Where a primary section partition is used, show gaps or overlaps explicitly and require their resolution or intentional labeling; secondary musical-event lanes may overlap naturally.

Instrumental and silence annotations must not create invented lyrics. A repeated chorus can share a name while remaining a distinct performance occurrence in V08–V10. Users may loop a section and send its context to the AI assistant without authorizing creative production before whole-map approval.

### Data and navigation

Read the selected master and save versioned section/marker annotations tied to its sample clock. The PRD does not prescribe a standalone marker entity; its schema is a proposed refinement associated with the master and map. Section edits feed transcript grouping, alignment review, storyboard coverage, and later timeline navigation. Changes affecting already approved dependencies use V47; simply changing a display label must not regenerate media.

## Timing and audio rules

- Store boundaries as integer positions on the selected master sample clock using `[start, end)` intervals. Display seconds/timecode as derived values.
- Preserve explicit mappings for resampled or offset stems. Do not round every intermediate crop to displayed milliseconds.
- Never drop leading silence, append padding, normalize duration, or change playback rate in exported source material without an explicit derived transform record.
- A monitoring selection is not a destructive crop. Audio packets created later must reference exact source ranges and their own context handles.
- Validation failures must not be hidden by automatic clamping. Explain which boundary, duration, or mapping requires correction.
- Browser playback and waveform rendering are review aids. Export correctness depends on recorded media mappings and final output validation.

## AI and skill behavior

The “Prepare song” skill reads the exact master revision, available stems, and configured analysis capabilities. It can propose section markers, request a scoped separation/analysis job, and report source problems. Deterministic audio services perform decoding, sample conversion, and timing validation.

The skill cannot approve the performance map, replace the active recording without the user applying that change, or treat separator confidence as a guarantee of clean individual voices. Results are structured proposals or candidate assets. Cost-incurring provider work obeys the displayed scope and existing spending controls. Skill inputs and outputs are inspectable in V45.

## States and recovery

| State or condition | Required behavior |
| --- | --- |
| Empty | Show upload/import actions and preserve navigation to other workspaces. |
| Uploading/processing | Separate transfer, decoding, proxy, and analysis progress; retain completed work after navigation. |
| Playback unavailable | Explain the failed media operation; allow metadata inspection and retry of the failed derived step. |
| Unsupported source | Do not select it as the usable master; preserve the draft project and explain supported input requirements. |
| No separation provider | Offer stem import and full-mix audition; link to connection setup. |
| Separation failed | Keep the original and other outputs; show provider job history and a bounded retry action. |
| Unknown submission outcome | Show reconciliation status and prevent duplicate automatic submission. |
| Stem timing uncertain | Keep the candidate visible; prevent its use as a verified audio source until mapping is resolved. |
| Master superseded | Label previous material by its source revision; preserve it and show affected dependencies. |
| Optional analysis absent | Keep manual section editing available; do not mark Song unusable. |

## Cross-area handoffs

V05 supplies the authoritative master to the entire project. V06 supplies audition sources and proposed lane associations to V09 and the Lip-Sync Lab's audio-packet preparation. V07 supplies navigation and planning markers to lyrics, storyboard, and timeline workspaces.

Downstream views may request source information but must not edit master audio through their own controls. Replacing a source routes back to this area and the shared impact review. The UI carries the selected song interval through handoffs without silently changing playback source.

## Acceptance criteria

| ID | Required behavior | PRD traceability |
| --- | --- | --- |
| SONG-AC01 | A valid upload preserves original bytes and records checksum, sample rate, channels, and sample count. | AUD-01; AC-01 |
| SONG-AC02 | A proxy or analysis conversion retains an explicit mapping to the selected master and cannot replace it silently. | AUD-02; PRD §10 |
| SONG-AC03 | A source replacement creates a new revision and identifies affected maps and production work. | AC-01, AC-11; PRD §13 |
| SONG-AC04 | A stem with a known offset can be auditioned at the corresponding master time without changing source bytes. | AUD-03, AUD-04 |
| SONG-AC05 | An unresolved stem offset is visible and cannot masquerade as verified synchronization. | AUD-03; SYN-01 |
| SONG-AC06 | A failed or unknown separation submission cannot launch an unseen second paid request. | AC-10; PRD §12.3 |
| SONG-AC07 | Repeated section names retain independent intervals and IDs. | AUD-05; MAP-04 |
| SONG-AC08 | Manual section editing works without automatic beat/energy analysis. | AUD-05 P0; AUD-06 P1 |
| SONG-AC09 | Changing zoom, gain, or audition speed cannot alter source duration, annotations, or delivery audio. | AUD-02, AUD-04; EDT-02 |

## Accessibility and responsive behavior

Every timeline boundary has an associated numeric field, and every drag operation has a keyboard alternative. Expose source labels, mute/solo state, processing status, and selected ranges through accessible names. Do not encode stem identity or errors by color alone.

On narrower screens, stack the waveform, selected track, and inspector into focused panes while preserving shared transport and numeric editing. Large-file uploads and analysis must continue when the user changes views. Browser audio permission or device-output problems should explain monitoring limitations without claiming the underlying file is damaged.

## Open design decisions

- Supported WAV encodings, maximum source duration/size, and proxy-generation strategy.
- Initial separation providers and how proposed alignment of imported stems is verified.
- Whether the primary section lane enforces a full nonoverlapping partition or permits explicitly labeled gaps; all behavior must preserve sample boundaries.
- Default displayed time units and keyboard nudge increments, with user-adjustable precision.
