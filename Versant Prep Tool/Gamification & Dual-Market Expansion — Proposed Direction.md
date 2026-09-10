---
tags: [versant-tool, proposal, gamification, b2c, b2b, product-direction]
---

# Gamification & Dual-Market Expansion — Proposed Direction

> [[00 - Index|← Back to Index]]

**Status: fully locked in.** The direction is in [[BRD — Versant Prep Tool]] v0.4 (Objective O7, broadened Risk R11, new Risk R14, narrowed Assumption A1 + new Assumption A7, new B2C stakeholder, updated scope/commercials) **and** Open Decisions D10-D13 are resolved and implemented as real requirements in [[FRD — Versant Prep Tool]] v0.5 §3.9-3.10 (FR-800 and FR-820 series). This note remains the working-detail companion — the mechanics table below and the TalkPal-flaw fixes are the reasoning those requirements point back to.

---

## 1. The tension this creates

Two things in the locked spec push hard against "make it fun":

- **Constraint C9:** Exam mode and Coach mode are separated absolutely — no assistance, no encouragement, no agent presence in Exam mode. "A practice environment kinder than the real test teaches the learner nothing about the real test."
- **Risk R11:** "Coach mode raises engagement but not scores" — conversational practice can build diffuse fluency while the test rewards specific trained behaviours. The mitigation is to track score movement separately from engagement metrics and cut features that lift only the latter.

**Resolution: gamification lives entirely in the non-scored engagement layer** — Coach mode, the dashboard/history shell, notifications — and never touches an Exam mode item or the readiness band. This isn't a workaround, it's the same principle C9 already established (Coach mode makes practice habitable; Exam mode stays a faithful test replica). Gamification is just more of what Coach mode already exists to do.

**R11 should be read as extending to gamification generally**, not just the agent: any fun/engagement feature needs its own metric (usage, streak length, badges earned) tracked *separately* from the readiness band and score trend (O1, O2). If a feature moves the former without moving the latter, cut it — same test [[TalkPal — UX Engagement Analysis]] itself fails, since TalkPal's own Level is time-on-task, not accuracy-based (flagged as a design flaw in that note, §5 and §9).

## 2. What to take from TalkPal — with its own flaw fixed

[[TalkPal — UX Engagement Analysis]] and [[What To Take — Design Lessons]] already catalogued what works and what doesn't. Applied here:

| Mechanic | TalkPal's version | Fix for this product |
|---|---|---|
| Streaks | Ongoing + longest streak, no freeze mechanic | Same, but add a streak-freeze/grace day — TalkPal's own gap, and "fun" shouldn't mean punishing a missed day |
| Level/XP | **Time-on-task based** — flagged as the clearest gamification flaw found in the whole competitive review | **Must be accuracy/mastery-based.** Level advances on measured skill movement (readiness band or per-skill estimate improving), not minutes logged. This is the one place a TalkPal pattern must be inverted, not copied — copying it as-is would recreate exactly the R11 failure mode |
| Badges/achievements | 2 taxonomies (streak, mode), mostly decorative completionism | Tie badges to things that predict real readiness: completed a full mock session, attempted all 3 task types, logged N Coach drills on your weakest task, first "provisional" readiness band cleared |
| Daily content rotation (Explore) | Best-rated retention mechanic found (4.5/5) — keeps a multi-day practice window from feeling like the same syllabus twice | A Coach-mode equivalent: rotate which bank items surface for daily drill, without inventing new content — stays inside constraints C4/C5 (approved item bank, published vocabulary bands) since it's a selection/scheduling change, not a content change |
| Toast celebrations | Fires on level-up, even mid-exercise, regardless of whether it reflects real progress | Fire only on genuine events: readiness band moved up, a new personal-best pronunciation/fluency score, weakest-task error rate dropped — anchors "fun" to real signal instead of decorating noise |
| Persona choice (Emma/Kai/Nina/Niko) | Light personalization, persists across modes | Low-risk to copy for Coach mode's voice/tutor identity — affects tone only, never scoring (already consistent with C11 — the agent's *voice* is cosmetic, its *corrections* still come only from scorer output) |

**Not copying, on purpose:** notifications defaulting to ON (TalkPal ships both Email and App notifications switched on by default — a dark-pattern-adjacent choice flagged in that note's Friction Points; this product should default them off or make the choice explicit at signup, especially given the consent-forward posture already required by FR-011/C3).

## 3. Dual B2C + B2B — what actually changes

The good news: the data model in [[FRD — Versant Prep Tool]] §4 already anticipates this. `tenant` and `cohort` exist from day one specifically so "Phase 1 learners belong to a default tenant" without a later migration — an individual B2C learner *is* just a learner on the default tenant; a B2B employer/academy *is* a `tenant` with an active `cohort`. No re-architecture needed, this is additive.

**What does need to change:**

- **BRD Assumption A1** currently frames the open question as *which* B2B buyer (employer / staffing agency / training academy). Dual-market reframes it as **two parallel go-to-market motions**, not a single either/or: a self-serve individual funnel (B2C, gamification-forward, low-friction signup) *and* the existing B2B/cohort motion. A1's validation work (CRM query, client conversations) still matters for the B2B side; the B2C side needs its own, separate validation (would an individual candidate actually pay for this, and how much) — worth tracking as a genuinely new open question, not folded into A1.
- **New stakeholder:** individual self-pay learner (student / job-seeker), alongside the existing table in BRD §4. Their interest is closer to what every competitor in [[AI Speaking Apps — Landscape]] and [[Versant Sites — Quick Reference Directory]] already serves — which means the competitive bar is higher on the B2C side (you're now compared directly to VersantPrep, VERSANT Bridge, Mocko) than on the B2B side (where [[Market Gap & Positioning]] found near-zero competition).
- **Pricing (BRD §11, currently "deliberately left open"):** now plausibly needs *two* models simultaneously — an individual subscription tier (à la VERSANT Bridge's ¥0/1,480/2,480/3,480 ladder) and a per-seat/per-cohort B2B licence. [[VERSANT Bridge — Competitor Deep-Dive]] is the closest working reference for exactly this dual structure — it already runs individual subscriptions *and* corporate certificates side by side.
- **The B2B differentiator doesn't disappear** — it gets sharper. [[Market Gap & Positioning]] established that certificates and batch visibility are what no competitor offers; those become the thing that makes the B2B tier worth more than the B2C tier, not a separate product.
- **Free tier design:** since the product now competes on the B2C side too, [[What To Take — Design Lessons]]'s warnings apply directly — don't ship a TalkPal-style free tier that's free in name only (one bot, nothing else). Gliglish is cited as the one app in this whole research proving a *genuinely* sustainable free tier is possible — worth studying if a real free tier is part of the B2C motion.

## 4. Candidate new requirements (proposals — not FRD-numbered yet)

If/when this gets folded into the FRD, these would need their own FR series (naming below just for discussion, not final IDs):

- **Streak tracking** — practice-day streak with a freeze/grace mechanic, surfaced on the dashboard, never inside an Exam mode session.
- **Achievement/badge system** — tied to real milestones (see table above), stored per-learner, never contributing to `readiness` (FRD §4 entity) or any score.
- **Accuracy-gated leveling** — a level/XP number that advances only when a per-skill estimate or readiness band improves, not on elapsed time or items attempted. Needs its own small state machine, separate from `readiness`.
- **Coach-mode content rotation** — a scheduling layer over the existing item bank (FR-401's approved items) that varies which bank items get selected for daily drill, distinct from FR-703's weakest-task/weakest-phone targeting — the two can compose (mostly weak-area drill, occasionally rotated variety).
- **Notification preferences** — explicit opt-in at signup, not default-on, consistent with the existing consent posture (FR-011).
- **Tenant type / pricing tier field** — likely already coverable by `tenant.type` (FRD §4) with values like `individual` / `cohort`, feeding which pricing model and which UI (gamification-forward vs. cohort-dashboard-forward) a learner sees.

## 5. Open questions — now resolved

Mirroring the BRD's own Open Decisions style — these needed a decision before this could be locked into the actual spec. All four are now decided:

| Question | Decision | Requirement |
|---|---|---|
| Are badges/streaks ever visible to a cohort administrator (B2B) or strictly private to the learner? | **Strictly private to the learner, always.** A cohort admin instead sees aggregate, non-comparative practice-frequency stats — not individual badges/streaks. Avoids the VersantPro "fake activity counter" trust failure ([[What To Take — Design Lessons]]) | FR-825, FR-826 |
| Does a B2C free tier exist, and how thin can it be before it repeats TalkPal's mistake? | **Real free tier, volume-capped not feature-gated** — all 3 task types stay available; daily/weekly scored-attempt and Coach-minute caps apply instead. Opposite of TalkPal's one-chatbot-only free tier | FR-802-FR-805 |
| Does gamification apply to Exam-mode-adjacent screens (e.g. a post-mock-session results page), or strictly Coach mode and the dashboard? | **Never on the Exam-mode results screen.** That stays strictly diagnostic (FR-300/301). Gamification confined to Coach mode and a separate dashboard/history view | FR-828 |
| Two pricing models — does that mean two separate signup/onboarding flows, or one flow that branches? | **One shared flow**, branching by entry point: public link → `individual_free` tenant; cohort invitation token → `cohort` tenant. No duplicate onboarding build | FR-801 |

---

## Connects to
- [[BRD — Versant Prep Tool]] — Assumption A1, Constraint C9, Risk R11, §11 Commercials — all touched by this direction
- [[FRD — Versant Prep Tool]] — §4 data model (`tenant`/`cohort` already supports this), §3.6 Coach mode agent (where gamification lives)
- [[TalkPal — UX Engagement Analysis]] — source of the gamification mechanics and their flaws
- [[Market Gap & Positioning]] — why the B2B certificate/batch-visibility angle stays the sharper differentiator even as B2C opens up
- [[What To Take — Design Lessons]] — the do's/don'ts this proposal keeps citing
