---
tags: [versant-tool, research, competitor, versant-specific, walkthrough]
---

# Versant Prep Sites — Hands-On Walkthrough

> [[00 - Index|← Back to Index]]

Hands-on walkthrough of 4 sites claiming to be "Versant prep": what each actually is, whether it's genuinely useful, and what it means for the product spec. (Original source included screenshots of each flow step; summarized here as text.)

---

## 1. Skillioma — a generic quiz wearing a "Versant" label

**Flow:** a lead-capture form (name/email/phone, no payment) behind a 10-minute countdown, before any question. Question 1: plain reading comprehension, typed answer. Question 2: a picture-description speaking task that **strictly requires a real uploaded audio/image file** before "Next" works — this blocked further progress entirely when the upload didn't succeed. Final result reached by submitting anyway: **0/5 (0%), "Failed,"** with a generic canned message ("Struggles with understanding basic English words...") unrelated to what was actually answered. Only 5 of the "10 questions" promised were ever scored.

**Is it helpful for real Versant prep? No.** Reading comprehension and picture-description are generic formats — no verbatim Repeats, no Sentence Builds, no Dictation, no Versant-accurate timing. The "Versant" name exists for search visibility, feeding Skillioma's real business (corporate training courses).

**What it means for the product:**
- Never ship a generic "Failed, here's generic advice" result — specific, rubric-accurate feedback is the whole value proposition.
- If a recording is required to advance, it needs a working in-browser recorder that reliably produces a valid submission — never a raw file-upload fallback a candidate can get stuck on.

## 2. VersantPrep — the closest real competitor found

Built by one independent developer (Jayraj Gujariya). Homepage shows the actual Parts A–J breakdown with per-question timing. Every "Practice Now" leads to a free signup wall (confirmed: email/password only, no card). After signup, a per-section demo runs before you start — instructions plus a worked example ("Part A – Sentence Completion... Answer quickly — you have 25 seconds per question"). The dashboard after a practice attempt shows real GSE/CEFR scores per skill and an "AI Performance Diagnostic" tied to the specific section, plus accurate Versant-specific detail ("Listen carefully to instructions and dictation files. You can't repeat them"). **One flaw:** the "Key Strengths" box once praised "excellent pronunciation and speech flow" right next to a literal 0/90 score — templated positive feedback that wasn't suppressed for a zero result.

**Is it helpful for real Versant prep? Yes — more than anything else found.** Accurate task naming, accurate timing, coaching copy that reflects genuine knowledge of how Versant's audio rules work.

**What it means for the product:**
- This is the closest functional benchmark. Its demo-before-you-start pattern (instructions + worked example + "Got it, start section") is worth adopting directly for Coach mode — this became **FR-034** in the FRD.
- The templated-praise bug is the exact failure mode to guard against: any canned feedback string needs a check that it never contradicts the score sitting next to it.
- It's live, free, India-targeted, aimed at the same MNC cutoffs (Amazon/TCS/Deloitte/Wipro) — but serves individual job-seekers, not employers. The B2B/compliance angle is the actual differentiation, not "we exist and it doesn't."

## 3. CA Monk — better task structure than expected, gated by coins

Logged-in homepage shows a coin balance. Inside, the "Versant Test" hub breaks the full test into 9 categories, 17 minutes, with a mic-check step:

| CA Monk category | Real Versant task it maps to |
|---|---|
| Read Aloud | Reading |
| Listen, Then Speak | Repeats / Conversations |
| One Word Answer | Short Answer Questions |
| Read and Select | Sentence Completion (multiple-choice style) |
| Fill in the blanks | Sentence Completion |
| Listen and Type | **Dictation** |
| Sentence Construction | **Sentence Builds** |
| Writing Sample | Email Writing |
| Simple Conversation | Conversations |

Starting the full test costs 100 coins; the visible balance was 50 — the actual question screens were never reached.

**Is it helpful for real Versant prep? Probably, but unverified.** The category structure is the most Versant-accurate name-mapping of the four sites found — an independent side-project arriving at nearly the same task breakdown is a useful sanity check that this task-family structure is simply the correct way to model a Versant simulator.

**What it means for the product:**
- The coin-gating pattern is worth deliberately avoiding — a currency neither researcher could afford blocks exactly the users most likely to need free practice.
- Its pre-flight checklist (mic check, "don't refresh/close," "submit only when sure") is good hygiene worth replicating in Exam mode.

## 4. TDS Global Solutions — not a prep tool, but real market validation

Homepage is a B2B pitch, partnered with Pearson. Lists exact task types per test product — Versant 4 Skills Test (30 min, 70 questions, 6 task types: Repeats, Sentence Builds, Conversations, Sentence Completion, Dictation, Passage Reconstruction), Versant Speaking Test (15 min, 63 questions), Versant Writing Test (35 min, 42 questions) — this independently confirms the official task structure used across all this research (see [[Versant Test — Official Task Structure]]). The entire self-serve "product" is a call-booking calendar — no test, no login, no practice content anywhere, by design.

**Is it helpful for real Versant prep? Not applicable.** It isn't a practice tool — it's how the real test actually gets delivered to candidates, only after an employer has already licensed it.

**What it means for the product:**
- Market validation, not a competitor: confirms the actual buyer (BPO/call-center employers) already pays for Versant-adjacent services today.
- Its remote-proctoring feature list (image/video/browser monitoring) previews the rigor employers expect from the *actual exam* — useful context for calibrating how much lighter a Coach-mode practice tool can be versus a future Exam-mode simulation.

---

## Side by side

| Site | Real Versant format? | Genuinely free to reach practice? | Where the walkthrough stopped |
|---|---|---|---|
| **VersantPrep** | Yes — accurate mechanics, timing, coaching copy (one templated-text bug) | Yes — core practice fully free | Completed a real attempt, reached the scored dashboard |
| **CA Monk** | Likely — best category-to-task mapping | Partially — free login, test itself costs coins neither researcher had | Blocked at the coin-payment step |
| **Skillioma** | No — generic quiz, canned failure feedback | Yes — just a lead-capture form | Completed to a final (failing) score report |
| **TDS Global Solutions** | N/A — not a practice product | N/A | Confirmed the whole "product" is a call-booking page |
