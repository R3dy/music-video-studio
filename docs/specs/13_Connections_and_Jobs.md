# Connections & Jobs — Area Specification

| Field | Value |
| --- | --- |
| Version / status | 0.1 / Draft |
| Date | November 6, 2026 |
| Views | V40–V43; four reusable view types |
| Primary requirements | PRD sections 11–12 and 17; SHT-01, SHT-04–06; AC-07, AC-09–10, AC-14, AC-16–17 |
| Related specifications | [Master UI specification](00_UI_Master_Spec.md), [Product requirements](Music_Video_Studio_PRD.md) |

## Purpose and scope

Let users connect owned inference services, inspect their capabilities, and understand the progress and cost of bounded production work. The workspace supports commercial APIs, user-hosted services, optional local workers, and manual media exchange through a common project experience. It is not a credit store, billing portal, or model marketplace.

Connection setup is available before whole-song map approval. This does not authorize creative generation before the map is approved. An existing queued or running job remains inspectable even when a connection fails or project inputs later change. The authoritative execution record lives outside the browser session.

## View inventory

Routes are proposed UI addresses. Providers and model identifiers are selected during implementation and benchmarking.

| ID | View | Presentation | Proposed deep link |
| --- | --- | --- | --- |
| V40 | Connection inventory | Workspace list with capability summaries | `/projects/:projectId/connections` |
| V41 | Provider configuration and testing | Focused form and diagnostic results | `/projects/:projectId/connections/:connectionId` |
| V42 | Job queue and execution details | Queue with selectable detail pane | `/projects/:projectId/jobs?job=:jobId` |
| V43 | Spending and runtime limits | Usage summary and policy editor | `/projects/:projectId/usage` |

## V40 — Connection inventory

List configured connections with a user-defined name, connection type, adapter, supported tasks, last successful check, and availability status. Distinguish “configured,” “reachable,” and “validated for this task”; a reachable text-model endpoint is not automatically a working video generator. Show task roles such as alignment, image generation, video generation, lip sync, and enhancement without assuming one connection can do all of them.

**Primary actions:** add a supported connection and choose a default connection for a task. **Secondary actions:** open settings, run a declared connection check, disable future use, or inspect dependent jobs. Connection removal should not erase historical job records. If queued work depends on a disabled connection, mark it blocked with a resolution path; do not silently reroute it to another provider.

This view reads connection metadata, adapter capabilities, task assignments, health checks, and running-job associations. It writes connection availability and task defaults, not secrets directly. Task defaults apply to future planning. Existing job snapshots retain their selected adapter and model; changing defaults cannot alter an in-flight request.

Manual exchange is presented as a supported workflow choice. Its request-package export and output import reuse [Shot Studio](09_Shot_Studio.md); it must not appear as a fake live provider with invented job progress. Local workers show their own reachability rather than implying that a cloud server can reach the user's `localhost`.

## V41 — Provider configuration and testing

The form is driven by the selected adapter. Show only applicable endpoint, authentication, task, model, and execution controls. Secrets use write-only fields with masked configured-state indicators; they are never echoed into prompts or diagnostic output. An endpoint is a validated connection setting, not arbitrary text passed through to an LLM.

A capability section shows accepted media, duration ranges/increments, output frame rates and resolutions, reference controls, audio conditioning, face selection, simultaneous-performer support, asynchronous status, cancellation, and pricing availability. Mark each value as adapter-declared, verified, or unknown. A provider that only accepts audio/video must not be described as consuming external phoneme timestamps.

**Primary actions:** save a connection revision and test it. Default checks are non-generating reachability/authentication/capability checks where supported. A billable sample test is a separately described, bounded job with its inputs, scope, and estimated or unknown cost visible; pressing “Test connection” must not unexpectedly buy media. Testing is not a bypass around the map gate for production footage.

**Secondary actions:** rotate a credential, select a model, inspect sanitized diagnostic results, and return to inventory. Test results identify the configuration revision they tested. A later change invalidates that result for the new configuration. Save validates required fields and compatible task selection; it preserves a recoverable draft after network failure. Unsupported features remain unavailable with a clear explanation, rather than being sent as ignored parameters.

Connection configuration, secret references, and adapter metadata are proposed implementation records supporting the PRD's connection system. Historical jobs retain stable references and safe display metadata; credentials remain separately secured and excluded from exports.

## V42 — Job queue and execution details

The queue filters by task, provider, shot, and execution state. Each row identifies its bounded request, input revision, start/submission time, progress when known, estimated/actual cost, and related output. Selecting a row opens details within the same view: input snapshot, sanitized settings, provider job ID, attempt history, output validation, and actionable error information.

Distinguish `draft`, `queued`, `submitted`, `running`, `succeeded`, `failed`, `cancel_requested`, `canceled`, and `unknown`. Do not fabricate percentage completion when the provider only reports “running.” A succeeded request with unusable media remains a succeeded provider execution with a failed output-validation result; its spend is still recorded.

**Primary actions depend on state:** submit an eligible bounded draft, inspect output, reconcile an unknown job, or request cancellation. **Secondary actions:** open the source shot, inspect the skill run, and prepare an explicit new attempt. Retrying uses a new attempt record with a defined scope and authorization. It never overwrites earlier output or silently starts other shots.

A timeout after submission triggers reconciliation through a provider ID or request token where possible. If the application cannot establish whether work started, retain `unknown` and block automatic duplicate submission. Refreshing or reopening the browser only reloads status. Exactly-once behavior cannot be promised for arbitrary providers; the required behavior is duplicate prevention, honest uncertainty, and explicit recovery.

Cancellation is a request until confirmed. Show unsupported, too-late, or failed cancellation distinctly. A canceled job may still have a charge, and cancellation never implies a refund. A completed output arriving after cancellation must remain traceable without being silently selected into the timeline.

## V43 — Spending and runtime limits

Show actual reported spend, estimates, active commitments, unknown-cost jobs, and runtime in separate fields. Group by project, task, and connection as supported. Preserve currency and units; do not add incompatible provider credits or currencies into a misleading total. Self-hosted runtime is not automatically a zero-dollar cost estimate.

The policy editor defines the project defaults used by request preflight: maximum authorized request cost where knowable, project spending limit, runtime limits, and explicitly permitted retry count/scope. Relevant inherited settings appear in [Shot Studio](09_Shot_Studio.md) V27 and [Lip-Sync Lab](10_Lip_Sync_Lab.md) V32. Optional batch controls remain P1 and cannot submit a batch until its required audition review has passed.

**Primary actions:** save limits and inspect jobs contributing to usage. **Secondary actions:** filter a time period, inspect a provider's reported cost detail, and resolve unknown estimates. Lowering a limit prevents additional incompatible submissions; it does not pretend already running provider work has stopped. The interface explains any in-flight exposure separately.

For an enforceable application cap, admission must account for concurrent queued/running commitments. If cost cannot be bounded, show that a strict monetary guarantee is unavailable and require explicit authorization for that specific unknown-cost request or block it under the selected policy. Never display unknown as zero or promise control over provider-side charges already incurred. Policy changes write a versioned configuration and affect later admission decisions; they do not rewrite historical authorization.

## Orchestration and AI behavior

Skills may request a declared capability and receive a validated connection selection. They must not invent model features, read raw credentials, increase limits, switch providers, or authorize their own retries. A natural-language request to generate one take becomes one bounded request, with any multi-step processing shown before execution.

Admission checks include applicable map approval, exact input revisions, provider capability, available media, budget/runtime policy, and duplicate-submission state. The same checks apply to UI, API, and skill paths. Failed admission creates an explanation, not a billable provider request.

## State and recovery behavior

| State | Required behavior |
| --- | --- |
| Empty | Explain supported connection types and allow manual exchange; do not require purchasing app credits. |
| Checking | Retain entered settings and show which check is running. No fake generation progress. |
| Authentication failure | Preserve nonsecret settings, sanitize the error, and provide credential update without logging the secret. |
| Worker offline | Keep queued work blocked; reconnect and reconcile existing jobs before resubmission. |
| Unsupported capability | State the missing feature and link to an eligible connection or manual route. |
| Unknown submission | Preserve request identity; reconcile first; do not automatically duplicate the chargeable request. |
| Limit reached | Block additional incompatible work and identify commitments; distinguish this from provider rate limiting. |
| Stale input | Keep the historical job and output; show incompatibility with current project input and link to impact review. |

## Data and handoffs

`GenerationJob` owns execution history, input snapshots, attempts, outputs, and cost/runtime. `SkillRun` describes the orchestration that requested work. `TakeVersion` and `AssetRevision` hold returned media; queue success does not confer artifact approval. A provider ID is never used as the production's shot ID.

Connection and budget records are supporting implementation refinements. Their display metadata may appear in a portable package, but usable credentials and sensitive transport parameters must not. Open source objects using stable project/shot/take/job IDs and preserve return context when entering another area.

## Acceptance criteria

| ID | Required behavior | PRD trace |
| --- | --- | --- |
| JOBS-AC01 | Configuration is possible before map approval, while creative-generation requests remain gated through every execution path. | AC-05, section 12 |
| JOBS-AC02 | Capability controls accurately disable unsupported multi-face, timing, duration, and output options. | AC-09, AC-14 |
| JOBS-AC03 | Testing cannot incur a hidden media-generation charge. | SHT-05, AC-07 |
| JOBS-AC04 | Submission timeout retains an unknown/reconciling job and cannot silently submit a duplicate. | AC-10 |
| JOBS-AC05 | Reloading the app reconnects to job state without restarting execution. | AC-17 |
| JOBS-AC06 | Cancel requested and cancel confirmed are distinct; neither claims a refund. | AC-16 |
| JOBS-AC07 | Unknown costs remain explicit, and concurrent commitments are considered before admitting new work. | Section 12.3 |
| JOBS-AC08 | Failed output validation retains provider cost and history and does not select the output for the edit. | AC-14, SHT-06 |
| JOBS-AC09 | Changing defaults or credentials does not silently mutate the provider/model of an existing request. | SHT-04, section 12 |

## Accessibility, responsiveness, and open decisions

Use text labels for status and announce meaningful asynchronous changes without repeatedly interrupting screen readers. Queue rows must support keyboard selection; details remain accessible as a focused pane on narrow screens. Cost units, unknown values, blocked reasons, and cancellation distinctions must stay visible in condensed layouts.

Open decisions are the initial adapter set, local-worker transport, capability verification strategy, supported currencies/units, and conservative treatment of cost estimates. Provider-side execution and billing limitations must remain visible after those choices are made.
