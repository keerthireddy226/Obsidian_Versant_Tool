Gemma_Edstellar · Placement & Practice Engine

# Gemini Models: Every Option, and What We Actually Use

Every Gemini model we found and checked, what we used before, what we use now, why, and whether it actually works — grading accuracy and pronunciation/fluency, both tested for real.

Contents

1. 01. The short answer, up front
2. 02. General-purpose models — the ones relevant to grading
3. 03. "Live" models — a different job entirely
4. 04. Standalone voice tools — also not the right job
5. 05. What we used before: Gemini 2.5 Flash-Lite
6. 06. What we use now, and why it's the right fit
7. 07. The model itself: what it actually is
8. 08. Cost, in real terms
9. 09. Can it handle the traffic? Scale and reliability
10. 10. Did it give accurate results? Content grading
11. 11. Did it check pronunciation and fluency?

## 01. The Short Answer, Up Front

✓

**We use `gemini-3.5-flash-lite` today.** It gives accurate grading results (tested directly), and it checks pronunciation and fluency from real audio (also tested directly, including finding and fixing a real bug in it). Full detail below.

Cost

< 1¢

per full placement test — every graded answer plus every spoken-answer check, combined (Part 08)

Content accuracy

Matched

every real test case graded exactly as expected, incl. a full realistic run scored B2 correctly (Part 10)

Audio accuracy

6/6

correct on repeated silent-audio tests, after a real bug in it was found and fixed (Part 11)

## 02. General-Purpose Models — The Ones Relevant to Grading

Gemini isn't one model — Google sells several "tiers" per generation (like "2.5" or "3.5"): **Pro** (smartest, slowest, priciest), **Flash** (fast and capable), and **Flash-Lite** (cheapest, fastest, plenty good for straightforward tasks). These are the only ones that could plausibly do our grading job — the other two sections cover why the rest don't apply.

| Model | Input / Output per M tok | Status when we tried it |
| --- | --- | --- |
| gemini-2.5-flash-lite | $0.30 / $0.40 | 404 — blocked "No longer available to new users" |
| gemini-2.5-flash | $1.00 / $2.50 | Not tried No shown need — costs more for the same job |
| gemini-2.5-pro | — (not reached) | 404 — blocked "No longer available to new users" |
| gemini-3-pro-preview | — (not reached) | 404 — discontinued entirely, no fallback |
| gemini-3.5-flash | $1.50 / $9.00 | Not tried 5x / 3.6x pricier than Flash-Lite, for the same job |
| gemini-3.1-pro-preview | $2.00–4.00 / $12.00–18.00 | 429 — quota exceeded on the very first call, free tier |
| **gemini-3.5-flash-lite** | $0.30 / $2.50 | ✓ Working **What we use now** |

The Pro-tier price above is Google's published rate, confirmed directly against the current pricing page — we just couldn't reach the model itself to test it, since the free tier blocked the very first call.

## 03. "Live" Models — A Different Job Entirely

These all showed up when we looked, but every single one is built for a *live, continuous, back-and-forth* voice session — like a phone call with an AI. Our app records one complete answer, then grades it afterward. There's no live conversation happening, so none of these fit, regardless of how good they are:

| Model | What it's for | Audio in/out per M tok |
| --- | --- | --- |
| Gemini 3.5 Live Translate Preview | Real-time speech-to-speech translation | $3.50 / $21.00 |
| Gemini 3.8 Live | Fluid, continuous live conversation | $3.00 / $12.00 |
| Gemini 3.8 Live Extended Thinking | Same, with deeper reasoning while speaking | $3.00 / $12.00 |
| Gemini 3.1 Flash Live Preview | Low-latency live dialogue | $3.00 / $12.00 |
| Gemini 3.5 Transcribe Live | Streaming transcription *while someone is still talking* | $3.50 / $21.00 |
| Gemini Robotics-ER 2 | Physical-robot spatial reasoning | n/a — unrelated |

Notice these all cost roughly **10x more** per audio token than what we actually use — that price pays for real-time speed we don't need at all.

## 04. Standalone Voice Tools — Also Not the Right Job

- **Gemini 3.1 Flash TTS** — turns text *into* speech (200+ expressive tags, 70+ languages). We need the opposite: understanding a learner's speech, not generating AI speech.
- **Gemini 3.5 Transcribe** (non-live) — a dedicated speech-to-text model. The closest thing to relevant here, except it auto-filters out filler words like "um"/"uh" — which we deliberately count as a fluency signal. Adopting it would quietly break that feature.
- **Pre-built voice personas** (Zephyr, Puck, Charon, Kore, etc.) — different AI voice styles, only relevant if we were generating speech, which we aren't.

## 05. What We Used Before: Gemini 2.5 Flash-Lite

The code originally used **gemini-2.5-flash-lite** — it was the model actually named in the cost research, and it had the best price we'd found. The very first real API call we made with it didn't return a grade. It returned a flat rejection:

The exact error Google sent back

*"This model models/gemini-2.5-flash-lite is no longer available to new users. Please update your code to use models/gemini-3.5-flash-lite."*

Google didn't delete the model — it still works for accounts that were already using it before some cutoff date. Our API key counts as a brand-new caller to it, and new callers are blocked. **So this was never a quality decision between 2.5 and 3.5** — 2.5 simply refused to respond to us at all.

## 06. What We Use Now: Gemini 3.5 Flash-Lite — and Why It's the Right Fit

Google's own error message named this exact model as the replacement, and we confirmed it works with a real test call before adopting it anywhere. Beyond just "it works," "helpful" here means something specific: **one model reads both the answer's content and the raw sound of a recording, in the same call, on the same bill.** That match to what this app actually needs is what makes it the right fit, not just its price tag:

- **One model does two jobs at once.** Without a model that handles both text and audio, grading content and judging pronunciation/fluency would need two separate services — a text model for grading, plus a dedicated speech API for the audio. That's two integrations to build, two bills to track, and two things that can independently break. Gemini 3.5 Flash-Lite does both from a single API call, which is exactly why it won out over Claude (Part 07 of the Grading Methods report covers that decision).
- **It's cheap.** $0.30 per million input tokens, $2.50 per million output — one flat rate whether the input is text or audio. Part 07 turns that into what it actually costs per test.
- **The task doesn't need more than this.** Grading an answer and giving pronunciation feedback aren't complex reasoning problems — they don't need the smartest, most expensive tier.
- **The alternative (Pro) isn't actually usable right now.** We tried — `gemini-3.1-pro-preview` hit a quota wall on the very first call, on the free plan. There's no way to prove Pro would even be better without paying first, so this isn't a case of picking a "lesser" option over a proven-better one.

## 07. The Model Itself: What Gemini 3.5 Flash-Lite Actually Is

Straight from Google's own model card, not a guess:

Google's own description"fastest, most cost-effective 3.5 model for high-throughput execution"

Input it acceptsText, Image, Video, Audio, PDF

Output it gives backText only

Input limit≈ 1,048,576 tokens

Output limit≈ 65,536 tokens

Structured output supportYes

Not supportedAudio/image generation, Live API

Two things in that spec sheet matter more than the rest:

- **The multimodal input list is exactly why one model can do two jobs.** Text, image, video, audio, and PDF all count as "input" at the same flat $0.30/million-token rate — that's the whole reason grading a written answer and judging a spoken recording can be the same API call, on the same bill.
- **What it can't do is nothing we needed.** It doesn't generate audio or images, and has no Live (real-time voice) API. This app only ever needed the model to listen and score, never to speak or hold a live conversation — so none of that missing capability is actually a gap here (Part 03 covers why the Live family wouldn't have fit anyway).
- **The context window (≈1M tokens in) is nowhere close to being tested.** A rubric, a question, and a learner's answer are a rounding error against that limit — there's no realistic scenario here where a long answer gets silently cut off.
- **Structured output is the actual mechanism behind trustworthy grading.** Rather than parsing freeform text and hoping it comes back well-formed, the code forces a strict `{score, reason}` shape out of every call. That's why a score is something the code can rely on directly, not something it has to guess-parse out of a paragraph.

**Last updated by Google:** July 2026, per the model card. A specific knowledge-cutoff date isn't published for this tier — not something this app leans on anyway, since every call here grades one specific answer against one specific rubric, never asks the model to recall outside facts.

## 08. Cost, In Real Terms

The $0.30 / $2.50 per-million-token rate above doesn't mean much on its own — nobody sends a million tokens in one go. Here's what it actually works out to for what this app sends, using Google's own published conversion rates rather than a guess: text runs about **100 tokens per 60–80 words**, and audio runs a flat **32 tokens per second**, no matter what's being said.

One content-graded answer (~200 words of question + rubric + answer in, ~30 words of score + reason out)≈ $0.0002

One spoken-answer pronunciation/fluency check (~20 seconds of audio in, one-sentence comment out)≈ $0.0003

A full placement test (≈25 answers graded + 8 of them spoken)under 1¢ total

**Worth knowing:** these are illustrative estimates built from Google's published token-conversion rates and realistic answer lengths, not numbers pulled from an actual billing statement — a real call could run a bit higher or lower depending on how long a specific question or answer is. But even doubling every figure here still keeps a full test under two cents.

### How this compares to every other option — verified, not estimated

Checked directly against each provider's own current pricing page rather than assumed:

| Model | Input / Output per M tok | Vs. what we use |
| --- | --- | --- |
| **gemini-3.5-flash-lite** | $0.30 / $2.50 | What we use |
| gemini-3.5-flash | $1.50 / $9.00 | 5x / 3.6x more |
| gemini-3.1-pro-preview | $2.00–4.00 / $12.00–18.00 | 7–13x / 5–7x more |
| GPT-4o-mini-audio-preview | $0.15 (text) / $0.60 (audio) | cheaper on text alone, but pricier on audio |
| GPT-4o-audio-preview | $40.00 / $80.00 | ~130x more |

The GPT-4o-mini row is the one nuance worth calling out: its plain-text rate ($0.15) genuinely beats Gemini's ($0.30). But it charges audio input separately, at $0.60 per million tokens — **double** Gemini's flat rate. Since every spoken answer in this app sends real audio, not just text, that model would end up costing *more* overall for our actual mix of calls, not less. Every other general-purpose option, and both proper Gemini upgrades within the same family, cost strictly more for the identical job.

### Where real cost reduction is still possible — not from a cheaper model, but fewer calls

$0.30 / $2.50 is already the cheapest general-purpose tier that can do this job — there's no cheaper *model* left to switch to. The real remaining lever is calling AI less often. Looking at the actual code today, all 13 AI-graded question types are sent to Gemini, including 7 that have exactly one correct answer once the recording becomes a transcript — **Repeats, Short Answer, Sentence Builds, Conversations, Reading Selective, Passage Comprehension, and Reading (Read Aloud)**. Plain text matching would give the identical result for those, for free, with zero loss in accuracy (this is already documented in Part 06 of the Grading Methods report, but not yet built — the code still routes all 13 types through Gemini today). Making that change would roughly halve the AI calls on a typical test, on top of everything above.

Money was never going to be the limit here. Whether the API can physically keep up with a lot of people at once is a separate question — that's Part 09.

## 09. Can It Handle the Traffic? Scale and Reliability

Two different questions, and cost isn't the interesting one anymore:

### At real scale, cost stays trivial

1 lakh (100,000) people, one 20-question test each≈ $450 total

Same, if the real per-call cost runs 2–3x the Part 07 estimatestill ≈ $1,000–1,500

Practice-test retakesscales linearly — 5 retakes ≈ 5x the above

Even the pessimistic number here isn't a real budget concern for 100,000 people. The actual limit is throughput, not money. (Part 08 covers what the per-call cost is actually built from.)

### Throughput — the real constraint, and it's account-specific

We checked Google's own rate-limit documentation directly rather than assume an answer. It doesn't publish one generic number for "how many requests per minute" — that figure depends on which billing tier an account is on, and is only visible by logging into that account's own [AI Studio rate-limit page](https://aistudio.google.com/rate-limit). We can't check that number for this project from outside the account, so we're not going to invent one here.

Testing right now is happening on the free tier, on purpose — the plan is to confirm grading and audio-check accuracy first, and only move to a paid plan once that's proven. That sequencing is intentional, not a risk being overlooked. Worth knowing for when that move happens: this project's free tier did hit its daily request cap once before, during earlier testing at a volume nowhere close to 100,000 people (Part 07 of the Grading Methods report has that history) — so the request cap is a known, already-seen limit, not a hypothetical one, and moving to paid ahead of any real 100,000-person launch is what removes it.

**Burst matters more than the daily total.** "100,000 people a day" is not one number — 100,000 spread evenly across 24 hours and 100,000 all testing between 10am and noon hit completely different limits. The requests-per-minute ceiling breaks first, long before any daily or monthly total does. Sizing for this needs the expected *peak concurrent* test-takers, not the daily headcount.

### Reliability — what's actually proven, and what's a real tradeoff

- **It fails closed, not corrupted.** If a Gemini call errors out, times out, or the API key is missing, the code returns nothing rather than guessing — a missing pronunciation score is treated as "no signal," never as a fabricated 0 or a fabricated pass. A learner never gets a wrong score because of a network hiccup.
- **Repeat consistency is tested, not assumed.** The same borderline answer run 6 times in a row graded consistently every time (Part 10); the same silent recording run 6 times in a row was correctly caught every time after the fix (Part 11). That's evidence about correctness, not about uptime.
- **One model doing two jobs is also one dependency doing two jobs.** Part 06 calls this a strength — one bill, one integration. It's also a real tradeoff worth naming honestly: if Gemini itself goes down, content grading *and* pronunciation/fluency checking both stop at the same time, together. Splitting across two providers would have avoided that, at the cost of two integrations and two bills instead of one.
- **Uptime itself is Google's responsibility, not something this app controls.** Like any external API dependency, an outage on Google's side is an outage here too — standard for any SaaS-backed feature, not specific to this choice of model.

**Before relying on a number for launch:** check the real RPM/RPD limits for `gemini-3.5-flash-lite` on this project's actual billing tier at `aistudio.google.com/rate-limit`, and size that against the expected peak concurrent test-takers — not the total daily or monthly headcount.

## 10. Did It Give Accurate Results? Content Grading

Tested directly, not assumed. Same real questions used to validate the grading before the switch:

Lazy 8-word email (needed 100+ words)scored 0.05

Complete, genuine emailscored 1.0

Correct short answer ("breakfast")scored 1 — correct

Wrong short answer ("dinner")scored 0, explained why

Same borderline answer, run 6 times in a rowconsistent every time

Also ran a full, realistic placement test: correct, well-paced answers through B1/B2 on every question type, deliberately wrong at C1. The result came back **B2** — cleanly, consistently, across all four skills — exactly matching what was actually answered.

## 11. Did It Check Pronunciation and Fluency? Yes — After a Real Bug Got Fixed

This part is genuinely from real audio, not the transcript. In plain terms:

1. The learner speaks, and a recording is made.
2. That actual recording — the sound itself — gets sent to Gemini, separately from the content-grading step above.
3. Gemini listens to it and rates pronunciation and fluency (0 to 1), plus a one-sentence comment.

The bug we found: it sometimes made up feedback for silence

We fed it the exact same completely silent recording six times in a row. It correctly said "no speech detected" only twice — the other four times, it confidently invented specific pronunciation comments for audio that had nothing in it.

**The cause:** the original prompt told Gemini what the learner was *supposed* to say before asking it to judge the recording — so it leaned toward "they probably said this" instead of actually checking whether anything was said at all.

**The fix:** stop telling it the expected words, and explicitly instruct it to verify real speech is present *before* scoring anything.

Same silent recording, retested 6 times after the fix6/6 correct

Real recordings, retested after the fix0.8–0.9, consistent, correctly scaled

**Update:** once this fix held up under re-testing, pronunciation and fluency were brought into the actual level calculation too — for a mic answer, both now have to clear the same per-level bar as content. Verified live in both directions: silent audio with otherwise-correct content and pace failed correctly; real audio passed correctly. See the Grading Methods report, Part 10, for the full rule.

Pricing and model availability reflect what we found as of September 2026 — Google renames and retires models often (we hit this twice), so re-verify with a live test call before relying on any model name long-term.
