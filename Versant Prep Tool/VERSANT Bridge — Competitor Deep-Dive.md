---
tags: [versant-tool, research, competitor, versant-specific, versant-bridge]
---

# VERSANT Bridge — Full Competitor Deep-Dive

> [[00 - Index|← Back to Index]]

**Why this matters:** the first platform found in this whole research process that is actually **built specifically for the Versant test** — not a general conversation app. Directly affects the market-gap assumption behind the product spec (see [[BRD — Versant Prep Tool]] Assumption A3, resolved false citing this competitor).

---

## What it is, who makes it

A web app (PC/phone/tablet) built only for the **Versant by Pearson English Speaking and Listening Test**, marketed as "Japan's first dedicated Versant prep app." Made by **Kurakumo Inc.**, a Japanese edtech company.

**Not their only product** — they run the same playbook across 3 English speaking tests:
- **DET Bridge** — Duolingo English Test (their first product)
- **VERSANT Bridge** — this one, launched October 1, 2025
- **ESAT-J Bridge** — launched July 14, 2026, for Tokyo's middle-school English speaking entrance exam

This is a company with a **repeatable product template** — "AI speech-scoring prep app for [specific English speaking test]" — already proven across 3 different tests. That's a bigger signal than one competitor: the category (dedicated single-test AI prep apps) is a validated business model, run by a team who could plausibly build a 4th one if they saw demand.

Legal positioning: explicitly **not affiliated with, endorsed by, or supervised by Pearson**.

## What's on the page

Structure: Hero → 3-point feature summary → "Why It Works" → 2 new features (Pronunciation Training, Timed Writing) → "4 Reasons It Works" → motivation/dashboard → score-tracking → multi-device → pricing → buy-your-test-ticket → FAQ.

**"4 Reasons It Works":** (1) huge catalog of Versant-style items/tests, (2) designed by perfect-score (80/80) Versant coaches, (3) unlimited practice for members, (4) AI speech coach.

**Content catalog**, organized by the test's own Part A–F structure:

| Section | Item count |
|---|---|
| Part A | 240 |
| Part B | 480 |
| Part C | 180 |
| Part D | 180 |
| Part E | 60 |
| Part F | 60 |
| **Total** | **3,000+ items, plus 30+ full mock tests** |

**Newer features (flagged "NEW"):** Pronunciation Training (phoneme-level AI scoring, drill specific sounds); Timed Writing (timed-sprint drills, AI evaluation).

**Scoring:** AI scores on the same 4 dimensions Versant itself uses — pronunciation, fluency, grammar, vocabulary. Dashboard tracks sessions, study time, weekly averages.

## Real task-level detail found (Part F)

Their separate Japanese-language blog publishes real strategy guides per section. Part F, translated:
- **Format:** opinion/reasoning prompt ("Which matters more: A or B?" / "Do you agree or disagree?")
- **Structure:** 2 questions, **40 seconds per response**, audio prompt plays only once
- **Scored on:** content appropriateness, grammar/vocabulary range, pronunciation clarity, fluency (no unnatural pauses)

**Their taught method:** the **PREP framework** (Point → Reason → Example → Point) plus 7 English-sentence-construction rules adapted from a Japanese-market writing guide. This confirms Part F is very likely Versant's **"Open Questions"** task — free-response, opinion-based, single-playback, strictly timed. A genuinely different animal from Repeats/Sentence Builds/Dictation (the current product's Phase 1 scope) — closer to "content and reasoning" than word-for-word accuracy.

## Pricing & business model

| Plan | Price | What you get |
|---|---|---|
| Free | ¥0/mo | Limited items, 5 pronunciation scores/day |
| Light | ¥1,480/mo (~$10) | All items, 2 mock tests/mo, 10 scores/day |
| Standard | ¥2,480/mo (~$17) | All items, 5 mock tests/mo, 10 AI corrections/day, 50 scores/day |
| Premium | ¥3,480/mo (~$24) | All items, 10 mock tests/mo, 100 corrections/day, 100 scores/day |

**Notable business-model choices:**
- No credit card for the free tier, no minimum term, cancel anytime by downgrading to Free.
- **Resells the actual Versant test ticket inside the app**, at a 5% member discount — a "practice → book the real test" funnel that keeps the user in-product through the whole journey.
- **Offers corporate completion certificates on request** — meaning they already have (or want) B2B/employer customers, not just individual self-payers. Directly relevant since your buyer is also employers.
- Payment currently JPY-only, credit card only — a real limitation outside Japan.

**Credibility signals:** "designed by perfect-score (80/80) Versant coaches"; a disclosed, hedged performance claim — "2,000 learners reporting real score gains" (+10, +20 point examples) with an explicit disclaimer that results may vary and are not a guarantee.

## What this means for the product

**Reframes the market-gap assumption:** it's not "no one builds Versant-specific prep tools" — someone does, it's a real business (3 tests deep, 2,000+ users claimed), with a credible tested pricing model. The honest framing: **the gap isn't the product category — it's the geography and the buyer.** Kurakumo targets individual working adults in Japan paying in yen; the target market is **employers screening BPO candidates in India/Philippines** — B2B, different region, different buyer motivation, which they show no sign of touching. See [[Market Gap & Positioning]].

**Worth copying:**
- Organize content by the test's own official sections, not generic skill labels — builds trust with a skeptical, exam-focused buyer.
- The PREP + explicit-rules method for open-response tasks — reusable teaching framework for a future phase (Open Questions, Conversations).
- Bundling the real test ticket into the app — worth considering even as a referral link.
- A completion-certificate option for corporate customers — directly applicable, since the buyer is the employer, not the candidate.
- Honest, hedged performance claims.

**Worth noting as a gap even in their product:**
- They don't clearly name the 6 official task types on the marketing page (just "Part A–F," item counts) — being explicit that you drill "your Repeats, your Sentence Builds, your Dictation specifically" is more legible to a buyer who already knows Versant's structure.
- Nothing suggests they do server-authoritative timing or address multi-jurisdiction data compliance — a genuine differentiator if selling to a compliance-sensitive B2B buyer.

## Sources
- VERSANT Bridge landing page (`lp.versant-bridge.com/en/`)
- VERSANT Bridge Part F strategy guide (Japanese, `versant-bridge.kurakumo.jp`)
- Kurakumo Inc. ESAT-J Bridge press release and company site
