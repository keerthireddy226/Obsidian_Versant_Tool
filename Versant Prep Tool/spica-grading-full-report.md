Spica by Edstellar — Internal Notes

# How the adaptive engine picks, grades, and levels your answers

A plain-language reference covering the three things we walked through: how the next question gets chosen, how each answer is actually graded, and how a CEFR level gets assigned from all of it.

1. Question pattern 2. Grading 3. Levels Open items

01.

## Question pattern

Every session starts at **B1** — the middle of the CEFR scale — for every question type, so the very first question doesn't already assume you're a beginner or advanced.

### Per-question stepping

Clamped at the ends of the scale — a wrong answer at A1 stays at A1, a correct answer at C2 stays at C2. Each question *type* (Read Aloud, Repeat Sentence, …) steps independently — your Reading difficulty and your Speaking difficulty move separately, not as one shared number.

### Budget per type

Each type only asks a small, fixed number of questions for the whole test — so a rough patch in one type can't spiral into an endless run of easy questions. Once a type's budget is used, the engine simply moves to the next type in the sequence, regardless of how that type went.

| Question type | Budget |
| --- | --- |
| Reading (Read Aloud) | 3 |
| Dictation | 3 |
| Reading Comprehension | 3 |
| Sentence Completion | 3 |
| Repeat Sentence | 2 |
| Short Answer | 2 |
| Sentence Builds | 2 |
| Open Questions | 1 |
| Email Writing | 1 |

### Which exact question

Within a type and level, the specific question is picked with a light random element: it prefers items you've never seen before over ones you've already attempted, and among equally-good candidates it picks randomly rather than always serving the same one first.

02.

## Grading

Grading happens the instant you submit an answer — never later in the background — since the engine needs to know right-or-wrong before it can pick your next question.

Exact / rule-based

Multiple choice, dictation, sentence completion, typing. Compared directly against the stored correct answer(s) — deterministic, no AI involved.

AI rubric-graded

Open-ended writing and every spoken type. Graded from the **transcript only**, against a rubric describing what "answering it well" means for that task — scaled to the item's own CEFR level.

### When there's no single correct answer

For genuinely open questions ("tell me about …"), there's no stored expected answer at all. The grading model is told explicitly that the learner should give their own genuine answer, then scores 0–1 on how well the response actually *addresses the question asked* — an unrelated or random answer scores low precisely because it fails that relevance check, not because it misses a hidden keyword.

### Fluency and pronunciation

A separate pass listens to the actual recording and scores pronunciation and fluency 0–1 — pace, clarity, hesitation. This is a *manner* signal, judged independently of what was said.

**How it combines with content correctness:**

- If the content is wrong, fluency and pronunciation are never even checked — the answer is simply wrong.
- Fluency/pronunciation can only pull an already-*correct* answer back down (poor pace or pronunciation) — they can never rescue a wrong one.

Open question The pronunciation prompt asks how "native-like" a recording sounds, with no explicit instruction to stay accent-neutral. That leaves a real, unverified risk: a strong regional accent could plausibly score lower on pronunciation even with perfectly correct, intelligible words. Worth testing directly with varied-accent recordings before leaning on that score too heavily.

### Three grading outcomes

| Status | What it means | Counts as, for stepping |
| --- | --- | --- |
| scored | A real verdict came back, right or wrong. | correct / wrong |
| pending | Skipped, or an ambiguous case flagged for human review. | wrong (skip only) |
| failed | The grading service itself broke (bad key, network error, quota) — not the learner's fault. | no change — same level |

Recently fixed "Failed" used to be treated exactly like a wrong answer, quietly stepping the next question down a level for a problem that was never the learner's fault. It now holds the same level instead — verified against a real grading-service failure, not just a hypothetical.

03.

## Levels

The six CEFR bands, in order: **A1 → A2 → B1 → B2 → C1 → C2**. A level isn't a flat percent-correct — it's the highest level a learner can sustain, and the bar to "pass" a level rises the higher it gets.

| Level | Required accuracy | Why |
| --- | --- | --- |
| A1 / A2 | 60% | Basic clarity is enough; simple errors expected. |
| B1 | 65% | Clear, correct content expected; minor errors fine. |
| B2 | 70% | Same, held slightly stricter. |
| C1 | 85% | Precise and complete; near-native expected. |
| C2 | 95% | Native-level standard — nearly flawless. |

Your **current level** and **goal level** shown on the Roadmap come from your placement result; each skill (Listening/Speaking/Reading/Writing) is leveled independently, using only the questions that were actually graded for that skill.

—.

## Open items to revisit

- Accent fairness in pronunciation scoring — flagged above, not yet tested against real varied-accent recordings.
- Whether repeated skips of one question type should also stop counting against the level (currently: skip still steps down, only service failures were changed to hold steady).

Spica by Edstellar Generated from the placement/grading engine as it stands today
