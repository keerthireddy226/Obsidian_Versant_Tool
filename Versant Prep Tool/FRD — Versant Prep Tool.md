---
tags: [versant-tool, reference, frd, product-spec]
---

# FRD — Versant Prep Tool (Functional Requirements Document)

> [[00 - Index|← Back to Index]]

**Version 0.6, draft, not approved. Phase 1 scope only** (Repeats, Sentence Builds, Dictation; learner-facing). Companion doc: [[BRD — Versant Prep Tool]] (at v0.5). Requirement IDs are stable; where a requirement implements a BRD objective or constraint it's cited as (O*n*) or (C*n*).

**Changes in v0.6:** §1 Actors rewritten around the real role model (Org Admin/Admin/Super Admin, no separate reviewer role). New §3.11 Personalized roadmap (FR-900s) and §3.12 Roles/organizations/permissions (FR-950s). New FR-040s (goal questionnaire), FR-245 (placeholder scoring), FR-316 (already-at-target check). FR-403/404 amended with a Phase 1 no-reviewer-UI note. Data model extended with `roadmap`, `roadmap_milestone`, `batch`, `tenant_invitation`, `participant_profile`; `tenant` gets `category`/`managed_by`.

**Changes in v0.5 (gamification lock-in):** BRD Open Decisions D10-D13 are now resolved (see [[BRD — Versant Prep Tool]] §12 and [[Gamification & Dual-Market Expansion — Proposed Direction]] for the reasoning). New **§3.9 Plans, tenancy, and free tier (FR-800 series)** and **§3.10 Engagement and gamification (FR-820 series)** added below, implementing BRD Objective O7 while respecting the extended Risk R11 and new Risk R14. Data model (§4) extended with `streak`, `level`, and `badge` entities, and `tenant.type`/`learner.engagement_notifications_opt_in` fields.

**Changes since v0.2 (combined v0.3/v0.4), following direct competitor research** — see [[00 - Index]] "How the research connects to the spec" and [[What To Take — Design Lessons]] for the source of each:
- **FR-034** — one-time instructions-plus-example screen before a learner's first attempt at a task type, sourced from VersantPrep, "the strongest single idea found across a wide competitor review"
- **FR-101 / FR-121** clarified to explicitly rule out an on-screen transcript during audio playback (closes an R13 mode-bleed gap), sourced from Mintza's no-transcript design
- **FR-035** — lock learner interaction (without pausing the server-side countdown) if the browser tab loses focus during a timed item, sourced from Mocko's tab-switch lockout
- **FR-127** — record backspace/delete keystroke count during Dictation as a retained feature value, not a score, sourced from MockVersant's hesitation-signal idea
- A rule added ruling out multiple-choice/forced-selection for any Phase 1 task, tying directly to the test's construct (BRD Appendix A), sourced from Mocko's zero-multiple-choice design

---

## 1. Actors

| Actor | Phase | Description |
|---|---|---|
| Learner | 1 | Takes diagnostics and practice sessions, receives feedback. `role = learner` |
| Org Admin | 2 | A contact at a client company/university, scoped to their own organization. `role = org_admin`. Reserved, not built |
| Admin | 2 | Internal staff, permission-gated, scoped to assigned organizations. `content_review`/`score_review` are permissions this actor can hold. `role = admin`. Reserved, not built |
| Super Admin | 2 | Manages Admin accounts and permissions; platform-wide. `role = super_admin`. Reserved, not built |

No reviewer UI in Phase 1 — items are inserted pre-approved by the team (FR-403 Phase 1 note). Full role/permission requirements: §3.12.

## 2. System overview

Ten modules: (1) Onboarding and consent, (2) Session engine — assembles/delivers timed items, server-authoritative timing, (3) Capture — browser audio + typed input, (4) Scoring — content, manner-of-speaking, mechanical written accuracy, (5) Feedback and readiness, (6) Item bank — storage, generation pipeline, review workflow, audio assets, (7) Learner history — attempts and progress, (8) Coach mode agent — conversational voice drilling and debrief, unscored, **(9) Plans and tenancy — free/paid/cohort plan limits (new v0.5)**, **(10) Engagement and gamification — streaks, levels, badges, non-scored (new v0.5)**.

### 2.1 The two modes

Mutually exclusive, over one shared capture layer.

**Exam mode** reproduces the real test: pre-generated audio played once, fixed response windows, no assistance, no agent. All scoring happens here.

**Coach mode** is a conversational voice agent — drills the learner on bank items, corrects pronunciation aloud, debriefs completed sessions. Nothing here is scored.

The separation is a constraint (BRD C9), not a preference — made testable by FR-030 and FR-700.

### 2.2 Audio front end — deliberately absent

Because a headset with boom microphone is required (BRD C10), the system is near-field with a known input device. **Out of scope by design:** wake-word detection, microphone-array beamforming, far-field noise robustness, acoustic echo cancellation. Session start is screen-triggered.

### 2.3 Two paths over one capture

| Path | Purpose | Method | Feeds a score? |
|---|---|---|---|
| Streaming | Interface responsiveness — show the learner they're being heard | Incremental recognition, partial hypotheses | **No** (C12) |
| Batch | Measurement | Full-utterance constrained alignment against known target text | Yes |

Never compromise one to serve the other.

### 2.4 Where realtime latency actually matters

**Exam mode does not require realtime end-of-speech detection** — the response window is fixed by spec, so the system records the entire window and derives pause boundaries offline in the batch pass. The only realtime requirement is **speech onset detection**, to enforce the start deadline (6s Repeats, 8s Sentence Builds) — a substantially easier problem than end-of-turn prediction. Hard endpointing (predicting turn completion for natural handover) is required **only in Coach mode**.

## 3. Functional requirements (condensed)

### 3.1 Onboarding and consent
Account creation via email/password or cohort invitation token (FR-010). Consent screen before any audio capture — a separate affirmative action, not bundled into terms (FR-011, C3); consent version/timestamp/IP recorded (FR-012); speaking tasks refuse to start without active consent (FR-013). Mic/playback check before first session (FR-014), background-noise warning (FR-015), headset-use detection via audible tone (FR-017, C10, D9), sample-rate/format verification for phone-level analysis (FR-018). Persistent, unambiguous non-affiliation-with-Pearson statement (FR-016, C2).

**Goal questionnaire (FR-040 series, new v0.6).** Runs after account creation, before the placement test (§3.2), so its answers exist in time to (a) drive the "already at target" check (FR-316) and (b) seed the Personalized Roadmap (§3.11).

| ID | Requirement |
|---|---|
| FR-040 | The system shall collect, before the first placement session: target exam (fixed to Versant in Phase 1), target CEFR level, target exam date, number of days available to prepare, target daily practice minutes, and any past exam attempt. |
| FR-041 | A reported past exam attempt shall be stored as an `external_result` row (§4), not a separate free-text field. |
| FR-042 | Goal-questionnaire answers shall be editable later from the learner's Profile (per the Profile module — [[Login, Roles, Permissions & Onboarding]] §4), not just at onboarding, since a target exam date or available time commonly changes. |
| FR-043 | The questionnaire shall not block account creation or consent (FR-011) — it is a required step before the *placement test* specifically, not before login. |

### 3.2 Session engine
Two session types: diagnostic (fixed composition, first session) and practice (FR-020). All response windows server-enforced — client timers are display-only (FR-021). No back-navigation or re-recording (FR-022); unfinished response auto-saved and advanced on window expiry (FR-023); early submission allowed (FR-024). Speech onset detection: no-onset within the start deadline terminates the item as no-response (FR-025). Countdown displayed (FR-026). Item non-repetition within a configurable interval, default 30 days (FR-027). Items ordered by ascending difficulty (FR-028). Network interruption preserves completed responses, marks interrupted items invalid rather than scored as failures (FR-029). Session mode (`exam`/`coach`) declared at creation, immutable (FR-030). Exam mode never invokes the Coach agent, displays agent affordances, offers replays/hints/encouragement (FR-031, C9). Coach mode responses never produce score records or feed readiness (FR-032). Active mode always unambiguously displayed (FR-033).

**FR-034** — first attempt at a task type gets a one-time instructions-plus-worked-example screen; never repeats, never appears inside a scored item, is onboarding only (not an exception to C9).

**FR-035** — losing browser-tab focus during a timed item locks learner interaction until focus returns; the server-side countdown continues unaffected, lost time is not restored.

### 3.3 Task-specific requirements

All specs derive from BRD Appendix A, re-verified per A2. **No task in Phase 1 shall be multiple-choice or forced-selection** — every item requires a full spoken or typed response, matching the real test's automatic-production construct.

**Repeats (FR-100s):** items 3-15 words from the approved bank (FR-100). Audio plays once, no replay, **no on-screen transcript before/during playback or capture** (FR-101). 15s window, onset within 6s (FR-102). Content scored on presence/sequence of expected words (FR-103); each discrepancy classified as omission/substitution/insertion (FR-104). Manner of speaking per FR-220/230 (FR-105).

**Sentence Builds (FR-110s):** exactly 3 phrases as separate audio segments, randomised, never the correct order (FR-110). 15s window, onset within 8s (FR-111). Each item carries a full **answer set** of every grammatical rearrangement, not one target string (FR-112); content scored against the closest answer-set member (FR-113); added/omitted words detected and reported (FR-114); manner per FR-220/230 (FR-115).

**Dictation (FR-120s):** items 3-14 words (FR-120). Audio plays once, no replay, **no on-screen transcript** (FR-121). 25s window for typed input (FR-122). Content scored on presence/sequence (FR-123); mechanical accuracy (spelling, capitalisation, punctuation) scored separately (FR-124); both retained independently — content feeds the Listening estimate, mechanical accuracy the Writing estimate (FR-125); word-level diff view in feedback (FR-126).

**FR-127** — backspace/delete keystroke count during the response window recorded as a `feature` value, retained for feedback/analysis. A signal, not a score — never contributes to FR-123/FR-124.

### 3.4 Speech scoring
Scoring uses **constrained recognition against known target text** (biased decoding/forced alignment), never unconstrained free-form transcription — unconstrained ASR errors on accented speech would otherwise be charged to the learner (FR-200, O4, R3). Onset detection tuned asymmetrically toward early triggering — a false onset costs nothing, a missed onset produces a wrong score (FR-201, R12). Every realtime onset decision re-verified against the recording in the batch pass; a missed-but-actually-on-time onset is marked invalid, not a no-response (FR-202). Full response window recorded, pause boundaries derived offline; realtime end-of-speech prediction not a dependency of Exam mode (FR-203, §2.4). A concurrent streaming path may drive UI feedback but never persists as or contributes to a score (FR-204, C12). Immediate capture acknowledgement + explicit scoring-in-progress state (FR-205 — perceived responsiveness depends more on prompt acknowledgement than actual scoring speed). Per-word acoustic confidence and time boundaries for every response (FR-210). Mean confidence below threshold → marked **low confidence**, excluded from readiness, learner informed (FR-211). Learner can flag a scored item wrong, queuing it for review with extended audio retention (FR-212). Fluency features from alignment: response latency, articulation rate, phonation-to-time ratio, silent-pause count/duration (default 250ms threshold), mean length of run, filled-pause count (FR-220). Pronunciation scored at phone level, per-phone + aggregate (FR-230); most frequent phone-level errors tracked across history (FR-231). All scoring asynchronous/queued — session progression never blocks on it (FR-240). Every scoring model versioned and recorded against each score (FR-241).

**FR-245 (new v0.6)** — Phase 1 build shall begin scoring behind a placeholder implementation of the TD-01 scoring interface: content scored by exact/close word match against the target text (or answer set), manner-of-speaking scored as pass/fail on response-in-window, no fluency/pronunciation sub-scores. Swapped for the real vendor without changing the interface. Tagged `model_version = placeholder` (FR-241).

### 3.5 Feedback and readiness
Per-task diagnostic breakdown on completion, not one aggregate figure (FR-300). Spoken-item feedback: word-level accuracy, time-to-begin-speaking vs. deadline, pause map, most-mispronounced phones (FR-301). Feedback phrased as actionable instruction, not a bare score (FR-302, O2). System produces a **readiness band**, not a numeric Versant score (FR-310, BRD §7). Band derived from per-skill estimates: Speaking from Repeats/Sentence Builds, Listening from Dictation content, Writing from Dictation mechanical accuracy (FR-311). Explicit statement that Reading is not assessed and the band covers three of four skills (FR-312). Every readiness output carries the FR-016 disclaimer (FR-313). Band thresholds are configuration, not code (FR-314). Low-confidence bands (few attempts, excluded items) presented as provisional with reason stated (FR-315).

**FR-316 (new v0.6)** — On producing a learner's first placement-derived readiness band, the system shall compare it against the target CEFR level from FR-040. If the band already meets or exceeds the target, the learner shall be told plainly, not routed into a Personalized Roadmap (§3.11).

*(Phase 2, data-model only)* target cut score comparison (FR-320).

### 3.6 Coach mode agent (FR-700s)
Conversational voice agent — makes practice habitable, delivers correction in speech, produces no scores. **Three sanctioned functions, nothing else in scope:** (1) drill partner — call-and-response on Repeats/Sentence Builds, adaptive to recorded weaknesses, immediate spoken correction; (2) debrief coach — spoken explanation of a completed Exam session (pauses, recurring phone errors, late starts); (3) warm-up — brief unstructured pre-session conversation, explicitly framed as unscored.

Runs only in a `coach`-declared session (FR-700). Drill delivers **bank items only**, never improvised sentences — improvised prompts have no difficulty estimate, vocabulary-band guarantee, or answer set (FR-701, C4/C5, R11). Drill items run under real response windows/start deadlines, so trained behaviour matches tested behaviour (FR-702). Agent selects drill items from the learner's weakest task and most frequent phone errors (FR-703, FR-231/502). Spoken corrections generated **only from scorer output** — the agent never asserts a mispronunciation or models correct pronunciation from its own judgement (FR-704, C11). Where scorer output is unavailable/low-confidence, the agent declines to correct rather than guess, and says so (FR-705). Barge-in supported — learner speech interrupts agent playback (FR-706). Agent never claims a Coach result is a score, band, or performance prediction (FR-707). Agent doesn't answer out-of-domain questions, gives a short redirection (FR-708). Configurable per-learner daily agent-minute cap — **placeholder default: 20 min/day (paid tier)**, pending Phase 0 unit-cost modeling per C8; on reaching it, Coach mode unavailable until reset while Exam mode remains fully available (FR-709, R10, D8). Agent minutes and cost per learner tracked (FR-710). Same consent/retention/deletion rules as Exam mode audio apply (FR-711, §6). Coach mode is optional — every Exam mode function works without it (FR-712). Agent unavailability doesn't affect Exam mode (FR-713).

### 3.7 Item bank and authoring
All items original — no Pearson content ingested, stored, or reproduced (FR-400, C1). Generation pipeline applies hard automated filters before human review: word count in the task's published range, vocabulary in the applicable frequency band, topic from the approved workplace list, no world-knowledge/work-experience dependence (FR-401, C4-C6). Spoken vocabulary constrained to the spoken frequency band, written to the written band; word lists recorded as a versioned asset (FR-402). No item releases without recorded human review/approval (FR-403). Review workflow captures reviewer identity, decision, rejection rationale (FR-404). Items carry a difficulty estimate, refined from observed performance (FR-405). Item audio pre-generated and cached — no request-time synthesis (FR-406). Audio generated across multiple voices, including non-native accented voices at conversational pace, mirroring the real test's delivery variety (FR-407). Each item records generation provenance: pipeline version, prompt version, filters passed, reviewer (FR-408). Retired items excluded from new sessions without altering historical scores (FR-409).

**Phase 1 note (v0.6, D16):** no reviewer UI. Items inserted directly with `status='approved'` pre-set, reviewer recorded manually.

### 3.8 Learner history
Every attempt retained with per-item scores, feature values, model versions (FR-500). Progress over time displayed per task and skill estimate (FR-501). Next practice focus recommended from weakest task and frequent error patterns (FR-502). Learner can export their own results (FR-503).

### 3.9 Plans, tenancy, and free tier (FR-800 series) — new v0.5

Implements BRD Assumption A7 (B2C motion) and resolves Open Decisions D11 and D13. Extends the `tenant` entity (§4) rather than replacing it.

| ID | Requirement |
|---|---|
| FR-800 | `tenant.type` shall include at minimum `individual_free`, `individual_paid`, and `cohort`. |
| FR-801 | Signup shall remain a single flow (FR-010), branching by entry point: a public/direct signup link creates an `individual_free` tenant by default; a cohort invitation token associates the learner with an existing `cohort` tenant. No separate onboarding implementation shall exist for B2C vs. B2B. **(Resolves D13.)** |
| FR-802 | All three Phase 1 task types (Repeats, Sentence Builds, Dictation) shall be available in Exam mode on every plan, including `individual_free`. The free plan shall never be feature-gated by task type or mode. **(Resolves D11 — volume-capped, not feature-gated.)** |
| FR-803 | The `individual_free` plan shall enforce a configurable cap on scored Exam-mode attempts. **Placeholder default: 5 scored attempts/day**, pooled across all 3 Phase 1 task types — anchored to VERSANT Bridge's free tier (5 pronunciation scores/day), the closest real comparable. Pending Phase 0 unit-cost modeling per C8. |
| FR-804 | The `individual_free` plan shall enforce a configurable cap on Coach-mode minutes. **Placeholder default: 5 min/day** — a quarter of the FR-709 paid-tier default, enough to genuinely sample Coach mode without giving away its full value. Pending Phase 0 unit-cost modeling per C8. |
| FR-805 | On reaching a free-plan cap, the system shall inform the learner clearly which cap was reached and when it resets. It shall never block access to the learner's own previously earned history or readiness records (FR-500). |
| FR-806 | `tenant.type` shall drive dashboard framing only (e.g. gamification-forward for `individual_free`/`individual_paid`, cohort-context-aware for `cohort`) — never a different account model, consent flow, or scoring path. |

### 3.10 Engagement and gamification (FR-820 series) — new v0.5

Implements BRD Objective O7. Governed by the extended Risk R11 (engagement must never substitute for measured skill movement) and Risk R14 (gamification must not undermine B2B trust). Resolves Open Decisions D10 and D12.

| ID | Requirement |
|---|---|
| FR-820 | The system shall track a per-learner daily practice streak (a day with at least one Exam-mode session or Coach-mode drill), computed independently of the `score` and `readiness` entities. |
| FR-821 | The streak shall support a configurable number of "freeze"/grace days per period, during which a missed day does not break the streak. **Placeholder default: 2 freeze days per calendar month.** |
| FR-822 | The system shall compute a learner "level" that advances **only** when a per-skill estimate or the readiness band improves (FR-310/FR-311). A level shall never advance on elapsed time, session count, or items attempted alone — the specific failure mode identified in [[TalkPal — UX Engagement Analysis]] and guarded against by Risk R11. |
| FR-823 | The system shall award achievement badges tied to measurable milestones only. Badge definitions shall be configuration, not code, mirroring FR-314's treatment of band thresholds. **Placeholder starting list (9 badges):** (1) First Session — completed first Exam-mode session; (2) Full Coverage — attempted all 3 Phase 1 task types at least once; (3) First Mock — completed a full mock session; (4-6) Hot Streak 7 / 15 / 30 — streak-length milestones; (7) Leveling Up — first level advance per FR-822; (8) Off Provisional — first readiness band no longer provisional (FR-315); (9) Coach Regular — 10 Coach-mode drills completed on the weakest task (FR-703). None are time-based or decorative — each ties to a genuine completion, consistency, or skill-movement event. |
| FR-824 | Streaks, levels, and badges shall never be computed from, or contribute to, any `score` or `readiness` record. This extends the Exam/Coach separation principle (C9, C12) to the engagement layer. |
| FR-825 | Streaks, levels, and badges shall be visible only to the learner who earned them. No cohort administrator view (Phase 2) shall display any individual learner's streaks, levels, or badges. **(Resolves D10.)** |
| FR-826 | A cohort administrator view (Phase 2) may display aggregate, non-comparative practice-frequency statistics for a cohort (e.g. percentage of learners active in the last 7 days, average sessions per learner). This is a distinct reporting feature from FR-825 and contains no individual-learner gamification data. |
| FR-827 | A celebratory notification (e.g. "your readiness band improved," "new personal best pronunciation score") shall fire only on a genuine, measured event — band movement, a new per-task best score, or a badge unlock per FR-823. It shall never fire on session completion, time spent, or item count alone. |
| FR-828 | No gamification content (badges, streak counters, level-up notifications, celebratory copy) shall appear on the Exam-mode session-completion / readiness screen (FR-300-FR-315). Gamification surfaces are confined to Coach mode entry points and a separate learner dashboard/history view. **(Resolves D12.)** |
| FR-829 | Email and in-app engagement notifications (streak reminders, practice nudges) shall default to **off** at signup and require explicit opt-in — consistent with the consent-forward posture of FR-011/C3, and reversing the default-on pattern documented in [[TalkPal — UX Engagement Analysis]]. |

### 3.11 Personalized roadmap (FR-900 series) — new v0.6

Rule-based, not AI (BRD §5).

| ID | Requirement |
|---|---|
| FR-900 | On completion of the first placement session (and the FR-316 "already at target" check finding a genuine gap), the system shall generate a roadmap: an ordered set of milestones from the learner's current per-skill estimates (FR-311) toward their stated target level (FR-040), scheduled against their stated exam date and available daily minutes. |
| FR-901 | Roadmap generation shall be deterministic rule logic (thresholds/weighting as configuration, mirroring FR-314's treatment of band thresholds) — not a language-model call. Consistent with BRD §5: a language model narrates, it does not decide. |
| FR-902 | Each milestone shall name a concrete target (e.g. "clear provisional status on Repeats," "reduce Dictation mechanical error rate below X%") derived from an actual weak-task/error-pattern signal (FR-502), never a generic "practice more" milestone. |
| FR-903 | The roadmap shall recompute when a stated goal changes (FR-042) or when a skill estimate materially improves (the same trigger FR-822's leveling uses) — not just once at onboarding. |
| FR-904 | Roadmap progress shall be readable from the Dashboard module ([[Login, Roles, Permissions & Onboarding]] §4) without needing to open a separate screen — milestone count, current milestone, percent complete. |
| FR-905 | The roadmap is diagnostic scaffolding, not a score — it shall never feed `readiness` (FR-310) or any `score` record, and shall never appear on the Exam-mode session-completion screen (extends FR-828's principle to this feature). |

### 3.12 Roles, organizations & permissions (FR-950 series) — new v0.6

Formalizes [[Login, Roles, Permissions & Onboarding]]. Reserved for Phase 2.

| ID | Requirement |
|---|---|
| FR-950 | All accounts, regardless of role, shall be rows in one `users` table. Behavior shall be a function of `role`, `tenant_id`, and `permissions` — never a separate table or code path per role. |
| FR-951 | `role` shall take exactly one of `learner`, `org_admin`, `admin`, `super_admin`. There is no `reviewer` role — `content_review` and `score_review` are entries in an Admin's `permissions`, grantable individually. |
| FR-952 | Only a `super_admin` may create, deactivate, or change the `permissions` of an `admin` account. An `admin` cannot grant itself, or any other account, a new permission. |
| FR-953 | An `admin` account shall see and act on only the tenants listed in its `tenants.managed_by` assignment. A `super_admin` sees and acts on every tenant unconditionally. |
| FR-954 | Deactivating an `admin` or `org_admin` account shall reuse the existing soft-delete field (`deleted_at`) already defined for learner accounts (§4) — blocks login without destroying history. No parallel deactivation mechanism shall be built. |
| FR-955 | `tenant.type = 'cohort'` shall carry a `category` value of `company` or `university` — a label only. No behavior, permission, or screen shall differ by `category`; company and university learners/orgs are the same code path. |
| FR-956 | An `admin` with the org-management permission may create a `cohort` tenant and, in the same action, designate the email address that becomes that tenant's `org_admin` via a `tenant_invitation` (FR-960). |
| FR-957 | An `org_admin` may create named `batch` sub-groups (FR-961) within their own tenant and assign learners to one; this is scoping only, not a permissions concept — an `org_admin` is always scoped to exactly one tenant regardless of batches. |
| FR-958 | An `admin` (if permitted) or an `org_admin` may bulk-create learner accounts from a CSV of email addresses, scoped to one tenant. Each row triggers the same "set your password" invitation mechanism as a single invite (FR-960) — run once per row, not a distinct flow. |
| FR-959 | **Hard rule.** No `org_admin` view shall ever display an individual learner's streaks, levels, or badges (extends FR-825 explicitly to this actor). An `org_admin` sees only aggregate, non-comparative readiness/progress data for their tenant or a batch within it — never the gamification layer, regardless of any future permission grant. |
| FR-960 | A `tenant_invitation` (learner, `org_admin`, or `admin`) shall be a one-time, hashed, expiring link, following the same mechanism as FR-011's consent-adjacent security posture — never a password visible to the inviter. |
| FR-961 | A `batch` shall belong to exactly one `cohort` tenant and exist purely for grouping/reporting (e.g. "2026 Intake") — it has no permission or scoring semantics of its own. |

Elsewhere in this document, `learner` refers to a `users` row with `role = 'learner'`, not a separate table.

## 4. Data model (essential entities)

| Entity | Key fields | Notes |
|---|---|---|
| `tenant` | id, name, type, category, managed_by | **v0.5:** `type` takes at minimum `individual_free`, `individual_paid`, `cohort` (FR-800). **v0.6:** `category` (`company`/`university`, FR-955) and `managed_by` (which `admin` account it's assigned to, FR-953) added, `cohort`-type tenants only. Present day one; Phase 1 learners belong to a default tenant, enabling Phase 2's cohort layer without migration |
| `users` | id, role, tenant_id, email, permissions, department, deleted_at, consent_version, consent_at, l1_language (optional), engagement_notifications_opt_in | **v0.6:** renamed conceptually from `learner` per FR-950 — one table for every role. `role` ∈ learner/org_admin/admin/super_admin (FR-951). `permissions`, `department` populated only for `admin` rows. `deleted_at` is the shared soft-delete field (FR-954). L1 used only for aggregate scoring-fairness analysis; `engagement_notifications_opt_in` defaults false (FR-829); learner-only fields are simply null for non-learner roles |
| `cohort` | id, tenant_id, name, target_cut_score | Dormant in Phase 1 for readiness reporting; from v0.5 also the aggregation boundary for FR-826's non-individual practice-frequency stats |
| `batch` | id, cohort_tenant_id, name | New v0.6 (FR-957/FR-961). Sub-group within one cohort tenant, grouping/reporting only, no permission semantics |
| `tenant_invitation` | id, tenant_id, email, role, token_hash, expires_at, accepted_at | New v0.6 (FR-956/FR-958/FR-960). One-time, hashed, expiring — same mechanism for a learner, `org_admin`, or `admin` invite |
| `participant_profile` | id, learner_id (users.id), target_exam, target_level, target_exam_date, prep_days, daily_minutes | New v0.6 (FR-040 series). The goal questionnaire's answers; editable post-onboarding (FR-042) |
| `roadmap` | id, learner_id, target_level, target_exam_date, generated_at, status | New v0.6 (FR-900 series) |
| `roadmap_milestone` | id, roadmap_id, description, target_signal, order, completed_at | New v0.6 (FR-902/FR-904). `target_signal` references the specific weak-task/error-pattern that produced it |
| `streak` | learner_id, current_length, longest_length, freeze_days_remaining, last_active_date | New v0.5 (FR-820/FR-821). Computed independently of `score`/`readiness` (FR-824) |
| `level` | learner_id, current_level, last_advanced_at, last_advanced_reason (skill_estimate \| readiness_band) | New v0.5 (FR-822). `last_advanced_reason` exists specifically to make FR-822's accuracy-gating auditable — never `time` or `session_count` |
| `badge` | id, learner_id, badge_type, earned_at | New v0.5 (FR-823). `badge_type` values are configuration, not code |
| `item` | id, task_type, target_text, answer_set, word_count, difficulty, topic, vocab_band_checked, status, provenance | `answer_set` populated for Sentence Builds only |
| `item_audio` | id, item_id, voice_id, accent, uri, duration_ms | Pre-generated |
| `session` | id, learner_id, type, started_at, completed_at, composition | |
| `attempt` | id, session_id, item_id, window_start_at, submitted_at, response_uri/text, invalid_reason | `window_start_at` server-authoritative |
| `score` | id, attempt_id, content_score, manner_scores, mechanical_scores, confidence, model_version, low_confidence_flag | |
| `feature` | attempt_id, name, value | Fluency/pronunciation features, retained for recalibration |
| `readiness` | id, learner_id, session_id, band, per_skill_estimates, provisional_flag, threshold_version | |
| `review_queue` | id, attempt_id, reason, status, resolution | Learner-flagged and low-confidence items |
| `external_result` | id, learner_id, reported_overall, reported_skills, source, verified_flag | Self-reported real Versant results — the seed of the calibration set (BRD A4). Built in Phase 1 even unused, since calibration data can only be collected prospectively |

## 5. Non-functional requirements

| ID | Requirement |
|---|---|
| NFR-010/011/012 | Spoken scoring ≤5s p95; typed scoring ≤1s p95; item audio starts ≤500ms p95 |
| NFR-013 | 200 concurrent sessions at Phase 1 target, defined scaling path to 2,000 |
| NFR-014 | Infrastructure cost per full mock session within a Phase 0 budget; cost modelling assumes ~6-8 min captured speech per 30-min session |
| NFR-015 | 99.5% availability target during Phase 1 |
| NFR-016 | Graceful scoring degradation — if scoring is unavailable, sessions complete and responses queue for later scoring |
| NFR-017 | Audio sample rate/format sufficient for phone-level analysis, recorded as a versioned decision |
| NFR-018 | Scoring fairness monitored by aggregate score distribution across declared L1 groups, reviewed per model version |
| NFR-020 | Speech onset detected within 150ms of true onset at p95 (scoring-correctness requirement — the start deadline depends on it) |
| NFR-021 | Batch-pass pause boundaries within 100ms of hand-marked boundaries at p95 |
| NFR-022/023 | Coach agent turn latency <1s median, <1.8s p95; barge-in interrupts within 200ms |
| NFR-024 | Coach mode cost per minute tracked separately, reported against the FR-709 cap |
| NFR-025 | Capture acknowledgement visible within 100ms of recording start |

## 6. Privacy and data handling

Encrypted at rest/in transit (FR-600). Default raw-audio retention 90 days from capture, configurable per tenant; features/scores may be retained longer (FR-601). Learner-initiated deletion of all recordings/personal data within a stated period, with confirmation (FR-602). Raw audio never used for model training without separate opt-in, recorded independently of base consent (FR-603). Storage region configurable per tenant, for India/Philippines residency (FR-604). Third-party speech services require a DPA with provider-side retention disabled (C7, FR-605). Recording access role-restricted and logged (FR-606). Consent record versioned; text changes require re-consent (FR-607).

**Retention matrix (highlights):** account identity data → account lifetime, deleted on account deletion. Consent record → retained even after deletion, in anonymized form (a deliberate exception — it's itself a compliance record). Raw voice recordings (Exam and Coach mode alike — no separate laxer policy for Coach) → 90 days, hard-deleted from R2 on expiry. Disputed-item recordings → retained through the review window regardless of standard expiry. Extracted features → may outlive raw audio, lower privacy risk as numeric derivatives. Scores/readiness → account lifetime. Self-reported external results → account lifetime unless the learner requests removal.

**Deletion is cross-system, not a single delete statement:** a complete flow removes/anonymizes the learner's PostgreSQL rows, any raw audio still within its retention window in R2, and confirms no residual copy exists with the speech-scoring vendor per the DPA (a contractual guarantee, not something the product can verify by itself).

**Retention enforcement, mechanically:** a `status`/`expires_at` field on stored recordings plus a scheduled script that periodically finds and deletes anything past its window and logs the result — satisfies NFR-016 without a dedicated queue at Phase 1 scale.

## 7. Technology stack (Phase 1)

**Day 1 — core practice flow:**

| Layer | Choice | Reasoning |
|---|---|---|
| Frontend | React + TypeScript + Vite + Tailwind | Component model suits an interactive, timer/capture-driven session UI |
| Backend | Node.js + Express + Zod | Express over Fastify for ecosystem/familiarity — performance gap irrelevant well beyond Phase 1 load; Zod validates shape and generates TS types |
| Database | PostgreSQL | Matches the relational data model in §4 |
| Authentication | Custom-built (bcrypt, server-side sessions, email verification, time-limited reset tokens, login rate limiting) | Avoids an external dependency/cost vs. a managed provider (e.g. Clerk). Carries more execution risk — a mistake in any sub-component (e.g. a non-expiring reset token) becomes a security incident, not a bug |
| File storage | Cloudflare R2 | Zero egress fees — the product's core behaviour is repeatedly serving audio, exactly what egress fees penalize |
| Hosting | AWS EC2 + nginx + Certbot + pm2 | Cost/control over managed platforms (Railway/Render); trade-off is more manual setup |
| Background/retry | `status` column (pending/scored/failed) + periodic retry script | Satisfies NFR-016 without a dedicated queue; also the retention-deletion mechanism |
| Deployment | Plain Node process under pm2, no containers | Acceptable at single-server scale |

**Phase 2 additions:** text-to-speech (vendor open, TD-05), speech scoring/assessment (vendor open, TD-01/02), LLM (personalization layer only — feedback phrasing, roadmap generation, Coach narration; bounded to narration, never judgment, per BRD §5 "What the language model does not do").

**Deliberately deferred, not decided against:** Redis/BullMQ queue (revisit if retry volume outpaces the periodic script, or sub-minute retry latency is required); Docker (revisit on a second server/staging environment); managed hosting instead of EC2 (a direct choice, not a stopgap — no scale trigger); load balancer/multi-instance backend (revisit as load approaches single-instance capacity).

## 8. Out of scope for Phase 1

Restated from the BRD: Conversations; Sentence Completion; Passage Reconstruction; Reading skill coverage; Org Admin / Admin / Super Admin dashboards and reporting (§3.12 — designed, not built); numeric predicted Versant score; other Versant product variants; native mobile apps; HR/ATS integration; languages other than English; live human tutoring. Also by design (§2.2): wake-word detection, mic-array beamforming, far-field robustness, echo cancellation. Also (§3.6): any Coach function beyond drill/debrief/warm-up — open-ended conversation on improvised material, general-purpose tutoring, agent-generated corrections not derived from scorer output.

## 9. Open technical decisions

| ID | Decision | Needed by |
|---|---|---|
| TD-01 | Speech scoring: hosted pronunciation-assessment API (speed) vs. self-hosted ASR + forced aligner (control, unit cost). Recommendation: prototype hosted, design a replaceable interface. **Timeline resolved v0.6 (D15):** vendor picked week 3, no second round budgeted; FR-245 placeholder used until then | Start of Phase 1 |
| TD-02 | Forced alignment component (classical HMM vs. ASR-based) — precision materially affects fluency/pronunciation features | Start of Phase 1 |
| TD-03 | Frequency-band word lists — which public corpora proxy the Switchboard/Longman bands | Before item generation |
| TD-04 | Answer-set generation for Sentence Builds — automated enumeration + human review, vs. fully manual | Before item generation |
| TD-05 | TTS provider/voice inventory — must support accented non-native voices at conversational pace, commercial-redistribution licensing | Before item generation |
| TD-06 | Band thresholds — anchored to published Versant→CEFR mapping, internal norm percentiles, or both | Phase 1 design |
| TD-07 | Number/labelling of readiness bands (see BRD D6) | Phase 1 design |
| TD-08 | Coach mode implementation: realtime speech-to-speech API (lower assembly cost, weaker intermediate transcripts, acceptable since unscored) vs. self-assembled ASR→LLM→TTS pipeline. Recommendation: speech-to-speech API | Before Coach mode build |
| TD-09 | Onset detector/endpointer — off-the-shelf VAD may suffice for Exam onset; Coach turn-completion is harder, may bundle with TD-08 | Start of Phase 1 |
| TD-10 | Whether the streaming interface path (FR-204) is worth building in Phase 1, vs. capture acknowledgement (FR-205) alone | Phase 1 design |

## Appendix — Phase 1 session compositions

**Diagnostic session** (first session, ~12 min): Repeats ×10, Sentence Builds ×5, Dictation ×8 — reduced counts deliberately, since this places the learner rather than simulating the exam.

**Full mock session** (matches the real test for the three implemented tasks): Repeats ×16, Sentence Builds ×8, Dictation ×14 — real timing, no replay. Must state it omits Conversations, Sentence Completion, and Passage Reconstruction, and therefore doesn't cover Reading.

**Practice session:** learner-selected single task or mix, 5-20 items, drawn preferentially from weak areas (FR-502). Runs in `exam` mode: scored, silent, no agent.

**Coach session:** `coach` mode, no fixed composition, drill items per FR-701/703 under real timing, spoken correction from scorer output, subject to the FR-709 minute cap. No scores, no readiness output.

**Debrief:** `coach` mode, initiated from a completed Exam session — agent explains that session's diagnostics aloud. Read-only with respect to scoring.
