---
tags: [versant-tool, reference, brd, product-spec]
---

# BRD — Versant Prep Tool (Business Requirements Document)

> [[00 - Index|← Back to Index]]

**Version 0.5, draft, not approved.** Owner TBD. Companion doc: [[FRD — Versant Prep Tool]]. This note condenses the source BRD; see the original PDF for full appendix detail (v0.3 and earlier).

**Changes since v0.3:** product direction expanded to include a gamification/engagement layer and a dual B2C + B2B go-to-market, per [[Gamification & Dual-Market Expansion — Proposed Direction]]. New **Objective O7** (engagement without corrupting the readiness signal). **Risk R11** broadened from Coach mode specifically to any engagement feature. **Assumption A1** narrowed to the B2B motion specifically; new **Assumption A7** added for the B2C motion, unvalidated. New stakeholder: individual B2C learner. Scope (§7) extended to include the engagement layer and self-serve B2C signup in Phase 1. New **Risk R14** (gamification undermining B2B trust). New **Open Decisions D10–D13** for the mechanics still unresolved (badge visibility to cohort admins, B2C free-tier depth, whether gamification touches Exam-mode results screens, one vs. two onboarding flows). Commercials (§11) updated to name the dual pricing-model question explicitly. New Open Decisions D10-D13 were subsequently resolved in the same v0.4 cycle (see §12) and are now implemented as real requirements in [[FRD — Versant Prep Tool]] v0.5 §3.9-3.10 (FR-800 and FR-820 series).

**Changes in v0.5:** scope (§7) extended with the onboarding goal questionnaire, the Personalized Roadmap, and the full Admin/Super Admin/Org Admin role hierarchy (reserved/Phase 2), per [[Versant Tool — Phases by Module]] and [[Login, Roles, Permissions & Onboarding]]. Phasing (§10) clarified: build does not wait on A1, since A7 is independent of it; Coach Mode moved to a post-launch fast-follow. New Open Decisions D14-D16.

> **How to read this document:** it describes a product whose **core market premise has not yet been validated** (Assumption A1 — the only assumption still blocking Phase 0 exit). Factual statements about the Versant test are drawn from Pearson documents dated 2021-2022 and were re-verified directly against Pearson's live sources on 2026-08-25 (Assumption A2, now closed). Claims sourced from secondary research that couldn't be verified are logged in Appendix B and must not be used as a basis for commitment.

---

## 1. Business context

**What Versant is:** a fully automated English assessment by Pearson, ~30 minutes, ~70 items across 6 task types. Reports an Overall score plus four skill scores (Speaking, Listening, Reading, Writing), each weighted equally at 25%, on Pearson's Global Scale of English (10-90), with a corresponding CEFR level. Full task structure: [[Versant Test — Official Task Structure]].

**How it's used:** employer-administered hiring screen, not a portable credential. Candidates cannot self-book — a Test Identification Number is issued by a test administrator, results go to the purchasing organisation via Pearson's ScoreKeeper portal. Pearson does not set a pass mark; the purchasing organisation sets its own cut score.

**Who sits it:** predominantly candidates in recruitment pipelines for BPO, call-centre, and outsourced customer service roles, concentrated in India and the Philippines (scale unverified — see Appendix B).

**What this means for the candidate:** a high-stakes, short-notice, employer-controlled gate — one attempt, a result they may never see in full, failure blocks employment.

**What this means commercially:** the buyer of the test is the employer — the party motivated to improve outcomes may be the employer, a staffing agency, a training academy, or the candidate, and these have very different willingness/ability to pay. Resolving this is **Assumption A1**.

## 2. Problem statement

**For the candidate:** existing preparation is either a fixed-form paid practice test that returns a score with no diagnosis, or unstructured free content. Neither answers *am I ready?* or *what specifically should I fix?* The test measures automaticity under time pressure, which reading explanations cannot improve — it requires repeated timed production with corrective feedback.

**For an organisation preparing candidates:** no instrument exists to identify which candidates are ready, which are close and worth training, and which are not viable — other than paying to put everyone through the real test and reading the failures afterward.

## 3. Objectives and success criteria

| ID | Objective | Success criterion |
|---|---|---|
| O1 | Give a learner an accurate readiness signal | Readiness band agrees with actual pass/fail outcome (vs. the client's cut score) for ≥75% of a validation cohort of ≥100 learners |
| O2 | Give feedback a learner can act on | ≥60% of learners who complete 3+ practice sessions show measurable improvement on their weakest task |
| O3 | Faithfully reproduce test conditions | Item formats, word-length ranges, vocabulary bands and timing windows match published Pearson specifications, verified by review against Appendix A |
| O4 | Score speech fairly across accents | Content-scoring disagreement with human transcription ≤10% word error on a held-out set spanning ≥4 L1 backgrounds |
| O5 | Establish a defensible commercial line | At least one paying design-partner cohort by end of Phase 2 |
| O6 | Coach mode feels conversational, not laggy | Median agent turn latency under 1s; speech onset detection accurate within 100ms of hand-marked onset on a test set |
| O7 | Engagement/gamification layer increases practice frequency without corrupting the readiness signal | Streak/badge engagement metrics and readiness-band accuracy (O1) tracked as two separate series; ship only features that move practice frequency **and** show no negative correlation with O1's validation accuracy |

**O1 and O4 are the objectives product credibility rests on** — if not met, the product should not ship regardless of the state of the rest. **O7 is explicitly not allowed to trade against O1** — see Risk R11.

## 4. Stakeholders

| Stakeholder | Interest |
|---|---|
| Learner / candidate | Passing the screen; knowing what to practise |
| Individual B2C learner (student / job-seeker, self-pay) | A product that's worth paying for on its own merits — engaging enough to return to daily, not just a hiring-mandated chore. New stakeholder as of v0.4; not present when the product was B2B-only |
| Cohort administrator (academy trainer, L&D or TA coordinator) | Cohort readiness visibility; pass-rate improvement |
| Purchasing organisation | Reduced hiring cost per qualified candidate |
| Edstellar sales / account management | A sellable proposition into an identified buyer |
| Edstellar delivery / L&D | Integration with existing training offerings |
| Engineering | Buildability; scoring accuracy; unit cost |
| Legal / compliance | Trademark exposure; voice data as sensitive personal data |

*(This confirms Edstellar — an existing training/L&D company — is building this as a diversification, not a startup from scratch.)*

## 5. What the "AI" actually is

Written for non-technical readers, since "AI tutor" hides four different technologies doing four different jobs with very different importance:

**The two that are the product:**
- **Speech recognition** — determines which words were produced and exactly when, constrained against the known target text (not guessing freely) — this is what keeps scoring fair for strong accents.
- **Pronunciation and fluency measurement** — from word/sound timings, derives which sounds were wrong, where speech broke up, speaking rate, latency. A measurement model. Outputs numbers, not sentences.

**The two that are supporting cast:**
- **A language model** — used in exactly three places: drafting practice items (human-reviewed before release), turning measurement output into readable feedback, and providing Coach mode's voice.
- **Speech synthesis** — generates item audio and Coach mode's voice.

**What the language model does not do:** it does not decide anything, does not judge whether a learner got an item right, does not assess pronunciation, does not rephrase what the measurement model already determined. A language model asked to judge pronunciation from a transcript would produce confident, fluent, *incorrect* judgements — and teach the learner something false. **The scorer decides; the language model narrates.** This is the reasoning behind FRD constraint C11 and requirement FR-704.

**Where there is deliberately no AI at all:**

| Function | How it actually works |
|---|---|
| Choosing what to practise next | Database query: weakest task, most frequent error patterns |
| Enforcing response windows | A server clock |
| Readiness band | A lookup table against configured thresholds |
| Item difficulty ordering | Stored difficulty estimates from observed performance |

**What could become genuinely proprietary:** in Phase 1 the measurement models are bought/borrowed. With enough learner recordings and enough paired real test outcomes, they could eventually be retrained — better tuned to Indian and Filipino accents, calibrated against actual Versant results rather than a published CEFR mapping. That depends on the calibration data chain: A1 (a reachable buyer) → client cohorts → A4 (paired real outcomes).

## 6. Assumptions and dependencies

| ID | Assumption | Status | Evidence required |
|---|---|---|---|
| A1 | **(B2B motion)** There is a reachable employer/agency/academy buyer for Versant preparation within or adjacent to Edstellar's existing client relationships | **UNVALIDATED — highest risk in this document** | CRM query for accounts administering Versant/English screening; 8-10 structured conversations with existing clients; ≥2 unprompted mentions of Versant or an equivalent screen. External signal found 2026-08-25 (TDS Global Solutions actively sells Versant screening to BPOs) — external evidence only, does not substitute for internal CRM/client-conversation evidence. Narrowed to "B2B motion" specifically as of v0.4, now that A7 covers the B2C motion separately |
| A2 | Test format, item counts and timings remain as documented in 2021-2022 | **VERIFIED 2026-08-25** | Re-fetched both source PDFs directly from Pearson's live URLs and extracted their real text (not model-summarized — a first-pass model summary of the validation PDF was caught fabricating a different, wrong table and discarded). Confirmed word-for-word: task structure (16/8/12/18/14/2 items, Parts A-F), all five response windows (15s/6s, 15s/8s, 25s, 25s, 30s+90s), word-count ranges (3-15, 3-14, 45-65), and the exact GSE→CEFR Table 7 (20-23 <A1 ... 79-80 C2) all match Appendix A precisely. Both source documents are unchanged since their original dates (April 2021 v1.0 guide; 2022 validation summary) despite being fetched fresh today. |
| A3 | No incumbent AI-based Versant preparation product holds the market | **FALSE — resolved 2026-08-25** | Direct search and hands-on evaluation found ≥14 Versant-specific sites/apps (VersantPrep, Mocko, VERSANT Bridge, CA Monk, MockVersant, VersantPro, MyEnglishPractice.jp, etc.) plus Pearson's own channel. Every one is an individual-learner, self-pay product; none serve an employer-facing B2B buyer with a compliance pipeline. Per Risk R6, this resolves as **evaluate and reposition, not stop** — see [[Market Gap & Positioning]] |
| A4 | Real Versant scores can eventually be obtained for calibration | Unvalidated | Depends on A1 — without client cohorts, calibration relies on slower, noisier self-reported scores from individual users |
| A5 | Published Versant→CEFR mapping is a valid anchor for a readiness band | Partially supported | Pearson's own validation summary provides the mapping (see [[Versant Test — Official Task Structure]]); its applicability to this product's independent measures is untested |
| A6 | Learners will accept a readiness band rather than a numeric score | Unvalidated | Test in Phase 1 with real users |
| A7 | **(B2C motion, new v0.4)** Individual candidates/students will pay for this directly (self-serve), independent of any employer relationship | **UNVALIDATED — new, no evidence gathered yet** | Requires its own validation track, separate from A1: pricing sensitivity testing against the individual competitors in [[AI Speaking Apps — Landscape]] and [[Versant Sites — Quick Reference Directory]] (the B2C competitive bar is far more crowded than the B2B one — see [[Market Gap & Positioning]]); a real free-tier design that doesn't repeat TalkPal's "free in name only" mistake |

**Dependency:** Phase 3 calibration is blocked on A1 and A4. **A7 is independent of A1** — the B2C motion can proceed or fail without affecting the B2B motion's validation, and vice versa.

## 7. Scope

### In scope — Phase 1
- Learner-facing web application, mobile-responsive
- Three of the six task types: **Repeats, Sentence Builds, Dictation**
- **Exam mode** — silent, deterministic, timed item delivery, no assistance, server-enforced, matching published response windows. The only environment scores are produced in.
- **Coach mode** — a conversational voice agent that drills bank items, corrects pronunciation aloud, and debriefs a completed session. Nothing here is scored.
- A hard boundary between the two modes (Constraint C9)
- Automated scoring: content accuracy, fluency, pronunciation, mechanical written accuracy
- Per-task diagnostic feedback
- Readiness band, expressed as a band with per-task detail — **not** a numeric Versant score
- Original item bank generated against published specifications, human-reviewed
- Consent, retention and deletion handling for voice data
- Multi-tenant data model (no cohort UI yet) — now doubling as the B2C/B2B split: an individual B2C learner is simply a learner on the default tenant, no separate build needed
- **(New, v0.4)** A non-scored engagement/gamification layer (streaks, accuracy-gated leveling, achievement badges) — lives entirely outside Exam mode, per O7 and the extended R11. Learner-private only (D10); confined to Coach mode and the dashboard, never the Exam-mode results screen (D12)
- **(New, v0.4)** Self-serve B2C signup path — one shared flow branching by entry point (D13), with a real, volume-capped (not feature-gated) free tier (D11)
- **(New, v0.5)** Onboarding **goal questionnaire** — exam choice, target CEFR level, exam date, prep days, daily minutes, past attempts — collected before the placement test
- **(New, v0.5)** The **"you may already be at your target level"** check against the stated goal
- **(New, v0.5)** **Personalized Roadmap** — rule-based study plan with milestones, from placement result + goal + exam date
- **(New, v0.5)** **Super Admin / Admin / Org Admin role hierarchy**, per [[Login, Roles, Permissions & Onboarding]] — reserved, not built. No separate reviewer role; content/score review are Admin permissions

Open Decisions D10-D13 are now resolved — see §12 and [[FRD — Versant Prep Tool]] v0.5 §3.9-3.10 for the corresponding requirements.

Within Phase 1, **Exam mode ships before Coach mode** — Exam mode is the product, Coach mode makes it habitable. Building the agent first risks an engaging practice companion that doesn't move scores (Risk R11).

### Explicitly out of scope — Phase 1
Reading skill coverage (the two tasks that carry Reading — Sentence Completion, Passage Reconstruction — are deferred, so Phase 1 covers three of four skills and readiness output must state this limitation); cohort administrator dashboards/reporting; numeric predicted Versant score; other Versant product variants; native mobile applications; integration with client HR/ATS systems.

### Deferred — later phases

| Item | Phase | Reason for deferral |
|---|---|---|
| Sentence Completion | 2 | Technically simple, but requires curated answer sets with collocation preferences for 18 items per test — a content-production problem |
| Conversations | 2 | Requires semantic scoring of open-ended short spoken answers |
| Passage Reconstruction | 3 | Hardest scorer: semantic content coverage plus mechanical accuracy |
| Cohort admin and reporting | 2 | Required as soon as an organisational buyer is confirmed (A1) |
| Numeric score prediction | 3 | Blocked on calibration data (A4) |

## 8. Constraints

| ID | Constraint |
|---|---|
| C1 | No reproduction of Pearson test items, audio, or copyrighted rubric text. All items original, generated against published specifications. |
| C2 | No claim of affiliation, endorsement, or official status. "Official," "endorsed," and Pearson logos are not permitted. Legal review required before any public-facing copy. |
| C3 | Voice recordings are personal data and may be biometric data. Explicit opt-in consent, stated retention limits, deletion on request are mandatory — informed by GDPR, India's DPDPA 2023, and the Philippines Data Privacy Act (see Appendix C). |
| C4 | Item vocabulary must respect published frequency bands: spoken items to the 5,000 most frequent Switchboard Corpus words; written items to the 1,600 most frequent Longman Corpus Network words. |
| C5 | Item topics restricted to the published workplace domain list (announcements, business trips, complaints, customer service, telephone/email, inventory, scheduling, marketing/sales). |
| C6 | Items must be context-independent — no dependence on world knowledge or prior work experience. |
| C7 | Third-party ASR or scoring services require a data processing agreement with provider-side audio retention disabled. |
| C8 | Target infrastructure cost per full mock session to be set as a hard budget in the FRD; scoring architecture chosen to meet it. |
| C9 | **Exam mode and Coach mode are separated absolutely.** Exam mode offers no assistance, no repeats, no encouragement, no agent presence. Nothing produced in Coach mode contributes to a score. A practice environment kinder than the real test teaches the learner nothing about the real test. |
| C10 | A headset with a boom microphone is required — a hard requirement, not a recommendation. Matches the real test's stated requirement, removes the need for far-field processing/echo cancellation, materially improves scoring accuracy. |
| C11 | The Coach mode agent shall not improvise language corrections. Any pronunciation or accuracy correction it speaks must originate from scorer output, not the language model's own judgement. |
| C12 | Scored measurement is derived only from full-utterance constrained alignment. Streaming partial recognition may drive interface feedback but shall never contribute to a score. |

## 9. Risks

| ID | Risk | Severity | Mitigation | Owner |
|---|---|---|---|---|
| R1 | A1 is false: no reachable buyer | Critical | Validate before build spend. Fallback: reframe as a general business-English assessment sold into existing L&D relationships, with Versant as one item-format module | Product / Sales |
| R2 | Readiness signal is inaccurate (O1 fails) | Critical | Conservative banding; state uncertainty; withhold numeric claims until calibrated; collect paired outcomes from first cohorts | Data / Product |
| R3 | Speech scoring penalises accents (O4 fails) | High | Constrained recognition against known target text; confidence thresholds; human-review path for disputed items; test across ≥4 L1 backgrounds | Engineering |
| R4 | Trademark or content dispute | High | C1 and C2; legal review of naming and marketing copy before launch | Legal |
| R5 | Voice data compliance failure | High | C3, C7; privacy design reviewed before first external user | Legal / Engineering |
| R6 | An incumbent product already owns the niche (A3 false) | Medium — **CLOSED 2026-08-25** | Verified: real incumbents exist, but every one is an individual-learner, self-pay product; none serve an employer-facing B2B buyer with a compliance pipeline. Resolution: **reposition around B2B/compliance-rigorous delivery, not stop** | Product |
| R7 | Test specification has changed since 2022 (A2 false) | Medium — **CLOSED 2026-08-25** | Re-verified directly against Pearson's live source PDFs (see A2). No drift found — the 2021/2022 documents are still the current ones served at the same URLs. Re-check again immediately before item generation begins regardless, since this can change without notice | Product |
| R8 | Free content is good enough for learners | Medium | Differentiate on diagnosis and timed repetition, not content volume | Product |
| R9 | Unit cost per session exceeds price point | Medium | Cost model built before scoring vendor selection; prototype on hosted API, plan migration path to self-hosted | Engineering |
| R10 | Coach mode cost dominates unit economics | High | Hard per-learner daily cap on agent minutes; default to structured drill over open conversation; measure cost per learner from week one | Engineering / Product |
| R11 | **(Broadened v0.4)** Engagement features — Coach mode *and* the gamification layer (streaks, badges, leveling) — raise usage but not scores | High | Coach mode delivers bank items under real timing, not improvised conversation; track score movement separately from all engagement metrics (O7); cut features that lift only the latter. Leveling must be accuracy-gated, not time-on-task-gated — the specific mistake found in [[TalkPal — UX Engagement Analysis]] | Product |
| R12 | Onset detection failure — a missed onset ends the item and produces a wrong score | High | Tune asymmetrically toward early triggering; verify against the recording in the batch pass; allow the learner to flag the item | Engineering |
| R13 | Mode bleed — assistance/retries/agent presence leaking into Exam mode via well-intentioned UX decisions | Medium | C9 stated as a constraint; mode separation asserted as a testable requirement, not a design convention | Product / Engineering |
| R14 | **(New, v0.4)** Gamification undermines B2B trust — a skeptical employer buyer sees streaks/leaderboards/celebratory copy as gimmicky or, worse, as surveillance of their candidates | Medium | Keep badge/streak visibility learner-private by default (Open Decision D10); B2B-facing surfaces (cohort dashboards, certificates) stay in the sober, diagnostic register the BRD already uses elsewhere — VersantPro's fake "students practicing now" counter is the cautionary example (see [[What To Take — Design Lessons]]) | Product |

## 10. Phasing

[[Versant Tool — Phases by Module]] is the granular engineering build order (Phases 0-11) underlying Phase 1 below.

- **Phase 0 — Validate and specify** (target 2-3 weeks): confirm calibration dataset licensing; legal read on naming/claims. A2 and A3 both closed 2026-08-25. **A1 no longer gates engineering start** (v0.5) — Assumption A7 (B2C) is independent of A1, so Foundation/build work starts immediately; A1 evidence-gathering runs in parallel and gates Phase 2's B2B/cohort work instead.
- **Phase 1 — Learner MVP:** three tasks, scoring engine (placeholder to start, real vendor mid-build — FRD FR-245), diagnostics, goal questionnaire, Personalized Roadmap, readiness band, consent handling, non-scored engagement layer. Coach Mode is a **post-launch fast-follow** (v0.5), not in the launch scope. **Exit:** O3 and O4 met, ≥20 real learners completed a full session.
- **Phase 2 — Cohort layer and scope extension:** Sentence Completion and Conversations; Org Admin/Admin/Super Admin screens (design complete — [[Login, Roles, Permissions & Onboarding]]); cohort administrator reporting; first paying design-partner cohort. **Exit:** O5 met, ≥100 learners with recorded outcomes, A1 evidenced.
- **Phase 3 — Calibration and completion:** Passage Reconstruction; calibration against paired real Versant outcomes; numeric score prediction only if accuracy supports it. **Exit:** O1 met.

**16-Week Plan (2026-08-27):** Phase 1 runs as 3 parallel tracks over 16 weeks (3 people); AI vendor decision is a hard week-3 deadline, no second round budgeted (hardens D3/FRD TD-01).

## 11. Commercials

Deliberately left open. Pricing model (per-seat, per-cohort licence, or subscription) depends entirely on which buyer A1 identifies — setting it before that is guesswork. Cost inputs (per-session infrastructure cost) established in Phase 0 so a price floor is known.

**As of v0.4:** this is now explicitly a **dual pricing question**, not a single one — an individual B2C subscription tier (A7) and a B2B per-seat/per-cohort licence (A1) most likely coexist rather than one replacing the other. [[VERSANT Bridge — Competitor Deep-Dive]] is the closest working reference for this exact dual structure — individual subscriptions and corporate certificates sold side by side. Still fully open: whether the B2C tier includes a real free plan (see A7's evidence requirement) and how the B2B tier's certificate/report value is priced relative to it.

## 12. Open decisions

| ID | Decision | Needed by |
|---|---|---|
| D1 | Confirm or reframe A1 | Phase 0 exit — gates everything |
| D2 | Product name (constrained by C2) | Before any public copy |
| D3 | Speech scoring: hosted API vs self-hosted stack | **Direction set:** start hosted, behind a swappable interface (FRD TD-01). Specific vendor open. |
| D4 | Voice retention period and default | **Resolved:** 90 days default from capture, configurable per tenant (FR-601) |
| D5 | Whether Phase 1 is offered free to gather calibration data | Phase 1 planning |
| D6 | Number of readiness bands and their labels | Phase 1 design |
| D7 | Realtime speech-to-speech provider for Coach mode | Before Coach mode build |
| D8 | Agent minute cap per learner per day, and whether Coach mode is gated behind a paid tier | Before Coach mode build |
| D9 | Whether headsets are enforced by blocking the session or by warning only (C10) | Phase 1 design |
| D10 | **Resolved.** Badges/streaks are strictly private to the learner — no cohort administrator view ever displays individual gamification data. A cohort administrator instead sees aggregate, non-comparative practice-frequency stats (Phase 2). | FRD FR-825/FR-826 |
| D11 | **Resolved.** A real B2C free tier exists, capped by volume (daily/weekly scored-attempt and Coach-minute limits) not by feature/task-type gating — avoids TalkPal's "one mode only" mistake. | FRD FR-802-FR-805 |
| D12 | **Resolved.** Gamification never touches the Exam-mode session-completion/readiness screen — that stays strictly diagnostic. Confined to Coach mode and a separate dashboard/history view. | FRD FR-828 |
| D13 | **Resolved.** One shared signup flow, branching by entry point (public link vs. cohort invitation token) — not two separate onboarding builds. | FRD FR-801 |
| D14 | **Resolved, v0.5.** Account model is one `users` table with `role` ∈ {learner, org_admin, admin, super_admin}. No separate reviewer account type — `content_review`/`score_review` are permissions an Admin can hold. Admin capabilities are individually permission-gated by a Super Admin, not fixed per department label. Full spec: [[Login, Roles, Permissions & Onboarding]]. | FRD §3.12 (FR-950 series) |
| D15 | **Resolved, v0.5.** The AI speech-scoring vendor decision (D3/TD-01) is scheduled for week 3 of the Phase 1 build, with no second evaluation round budgeted — a hard commitment, not just a direction. | 16-Week Plan, week 3 |
| D16 | **Resolved, v0.5.** No content-reviewer UI is built in Phase 1. Approved items are inserted directly into the item bank by the team, pre-approved; FR-403/404's reviewer fields remain in the schema, populated manually, for the day a real review workflow is built. | FRD FR-403 (Phase 1 note) |

## Appendix A — Verified test facts

See [[Versant Test — Official Task Structure]] for the full extracted task structure, timing, scoring approach, and GSE↔CEFR table, sourced from Pearson's *Versant 4 Skills Essential Test: Official Guide for Test-Takers* (2021) and *Test Description and Validation Summary* (2022). Must be re-verified per Assumption A2.

## Appendix B — Unverified claims register

Claims encountered in secondary research that could **not** be verified and must not be relied upon:

| Claim | Status |
|---|---|
| An AI-driven Versant prep platform exists covering all six parts at ~$5/month, with "thousands" of users | Partially confirmed 2026-08-25 — the general shape is now true (multiple real platforms exist, one covers 9 parts of one variant, several are free/low-cost), but the specific figures remain unverified for any single product |
| Pearson delivers ~3 million Versant tests annually | Unverified |
| Philippine 4-Skills mean score ≈63; local cut scores mid-50s to 60s | Unverified |
| GSE 59 ≈ Versant 58, and CEFR B2 = Versant 58-68 | **Rejected** — contradicts primary Pearson documents (GSE 59/90 = Versant 57/80; B2 = 57-67). Use Appendix A. |
| Pearson official practice test priced at $15 | Unverified |
| Versant product variants and their durations (Placement 50 min, Professional 60 min, Speaking & Listening 20 min, Writing 35 min) | Plausible, unverified |

## Appendix C — Regulatory frameworks

Expands constraints C3 and C7. Background for legal review, not a substitute for it.

| Framework | Region | Why relevant |
|---|---|---|
| GDPR | EU/EEA | Applies to any learner located in the EU/EEA regardless of company location; voice recordings may be "special category" biometric data |
| DPDPA 2023 | India | Directly relevant — India is a named data-residency market and a concentration of the target candidate population |
| Data Privacy Act of 2012 (RA 10173) | Philippines | Directly relevant — the other named residency market; National Privacy Commission guidance treats biometric data as sensitive personal information |

**Common principles across all three:** consent must be specific and affirmative (FR-011); purpose limitation — data can't be reused for a new purpose like model training without separate consent (FR-603); storage/retention limitation (FR-601, full matrix in FRD §6); right to erasure, propagating across every system (FR-602); processor accountability — any third party needs a contractual DPA (FR-605, C7); access control and auditability (FR-606).

**Where they diverge:** GDPR is restrictive-by-default on cross-border transfer (approved mechanism required); DPDPA is permissive-by-default (transfers allowed except to blacklisted countries); Philippines requires case-by-case adequate safeguards. Maximum penalties range from up to 4% of global annual revenue (GDPR) to ~$30M (DPDPA) to RA 10173's National Privacy Commission-enforced penalties. None of the "extra obligations at scale" (DPO requirements, Significant Data Fiduciary designation) are Phase 1 blockers, but should be revisited with counsel once real user volume exists.
