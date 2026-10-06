Gemma_Edstellar · Placement & Practice Engine

# How We Grade Answers, and What It Costs

How a text-only method checks an answer, how Gemini judges the harder ones, why content is still graded from the transcript rather than the recording itself, and what the whole thing costs.

**Updated Sep 17, 2026:** content grading moved from Claude to Gemini today. The rules in Part 10 also changed — spoken answers are now judged on pace as well as content, and a difficulty level the learner never attempted at all no longer counts as an automatic failure.

## 01. Two Ways to Grade an Answer

Every grading method we use falls into one of two families:

- **Match it against a known answer.** No AI involved — just compare text. Works when there's one correct (or a short list of correct) answers.
- **Have AI read it and judge it.** Used only when there's no fixed answer to compare against — an opinion, an email, a free response — and real judgment is needed instead.

Which family applies depends entirely on the question itself, not on whether AI happens to be switched on. A question with one right answer doesn't get more accurate by adding AI — it just gets slower and costs money for the same result.

## 02. Grading Without AI

These methods all do the same basic thing — compare the learner's answer to something we already know is correct — just in different shapes:

- **Exact match** — clean the text (lowercase, strip punctuation and extra spaces), then compare it to the target. Identical = correct. Used for "repeat this sentence exactly."
- **List match** — same cleanup, but compared against a short list of acceptable answers instead of just one, so "yellow," "it's yellow," and "it is yellow" can all count.
- **Multiple choice** — compare the option number picked to the option number that's actually correct. Nothing to calculate.
- **Closeness score** — count how many words differ between the answer and the target text, and turn that into a percentage. A perfect copy scores 100%; a few slips lower the score instead of failing it outright. Used for typing or reading a passage aloud.
- **Word overlap** — pick out the handful of important words from the original (names, places, key facts) ahead of time, then check how many show up in the learner's version. Used for retelling a story in their own words.
- **Word count** — simply checks a minimum length was met.
- **Human rater** — a trained person reads or listens and scores it by hand against a guide. The one method that genuinely understands the answer, but slow and expensive at any real scale.

The one thing every non-AI method shares: none of them actually understand the answer, they only count and compare. A nonsense answer that happens to contain the right words can pass; a genuinely good answer phrased differently can fail. That trade-off is exactly why AI grading exists for the harder cases.

## 03. Grading With AI (Gemini)

This only kicks in when there's no single correct answer to check against — an opinion, an email, a free-form response. Here's the actual mechanism: the model is given three things — the question, a description of what a strong answer needs to include, and the learner's real answer. It's also told how strict to be, scaled to the question's difficulty: a hard (C1/C2) question is told to expect a near-native answer with no real slips, while an easy (A1/A2) question is told that simple mistakes are fine and shouldn't be penalized. It reads all of it and returns a score from 0 to 1, plus one sentence explaining the score. That score is then compared against a pass mark — harder questions require a higher score to count as correct. If the request fails for any reason (a dropped connection, a quota limit), we never guess: the attempt is marked "couldn't be graded" rather than silently marked wrong.

**Tested directly, on real items:** a lazy 8-word email against a 100-word minimum scored 0.05; a complete, genuine email scored 1.0. A correct short answer scored 1; a wrong one scored 0 with an accurate explanation ("dinner is the evening meal, not the morning one"). Run six times in a row on the exact same input, the score came back consistent every time — unlike this model's audio judgment, see Part 05.

**This used to run on Claude.** Same mechanism, same rubric-style prompts — only the model underneath changed, from Claude Sonnet 5 to Gemini, on Sep 17, 2026, to bring grading onto one AI provider instead of paying for two. See Part 07 for why.

## 04. Which One to Use

**The rule:** if a question has one correct answer, use matching, not AI — it gives the exact same result, instantly, for free, with nothing that can go wrong. Save AI for questions that are genuinely open-ended, where judgment is actually required.

## 05. Important: Content Grading Still Only Reads the Transcript

This used to be a hard limit. Now it's a deliberate choice.

Claude genuinely could not accept audio at all — a real API limit, confirmed directly against Anthropic's documentation. Gemini is different: it can accept audio directly, and we *do* send it real recordings — but only for a separate pronunciation/fluency comment (Part 08), never to decide whether an answer is right. We tested Gemini's audio judgment directly and found it unreliable enough that content correctness deliberately stays on text only, even though the capability to do otherwise exists now.

Here is exactly what happens, step by step, when a learner speaks an answer into the microphone:

1. They speak, and a recording is made on their own device.
2. The phone or browser itself converts that speech into text. This is the same built-in speech-to-text every phone already has.
3. That **text** is sent to Gemini for content grading. The same recording is *also* sent separately, but only for the pronunciation/fluency feedback in Part 08 — never for deciding if the answer itself is correct.
4. Content is graded from the transcript exactly as it would be if the learner had typed the answer instead of speaking it.

The reason this is still true even now that the model can hear: we ran the exact same silent recording through Gemini's audio judgment six times in a row, and it invented confident, specific-sounding pronunciation feedback for four of those six — for audio that had nothing in it. Content correctness needs to be dependable, so it stays on the one channel (text) that tested completely consistent. See Part 08 for the full test.

## 06. Grading a Transcript Without AI

Since a spoken answer becomes plain text before anyone grades it, the rule from Part 04 applies to it too — it makes no difference that it started out as speech.

**Types with one correct answer** — once you look at the transcript, most of our spoken types actually have a single right answer: repeating a sentence exactly, giving a short factual answer, reading a passage aloud. Plain matching on the transcript gives the identical result AI would, for free: **Repeats, Short Answer, Sentence Builds, Conversations, Reading Selective, Passage Comprehension, Read Aloud.**

**Types that are genuinely open-ended** — there's no fixed answer to match against, so AI is still the right tool: **Story Retelling, Speaking Situations, Open Questions.**

## 07. What This Costs Now

Gemini 3.5 Flash-Lite (the model this app uses, verified directly against Google's current pricing page): **$0.30 per million input tokens, $2.50 per million output tokens** — one flat input rate whether it's text, audio, image, or video. A token is roughly ¾ of a word. Written and typed answers cost one request (content grading). A **spoken** answer costs two — a separate second request sends the actual recording for the pronunciation/fluency comment in Part 08.

**Why 3.5, not 2.5:** see Part 18 for the full model-tier breakdown and the exact error that forced this.

Content grading — typical input / output~350 / ~60 tokens

Content grading — cost per answer≈ $0.0003

Pronunciation/fluency (spoken answers only) — cost per answer≈ $0.0003

AI-graded answers per 20-question test~10–14

Cost per full test≈ 0.3–0.5 cents

| Volume | Cost / month |
| --- | --- |
| 100 tests | ~$0.30 – 0.50 |
| 1,000 tests | ~$3 – 5 |
| 10,000 tests | ~$30 – 50 |

**Roughly 4-5x cheaper than the Claude-based version of this system** (previously ~1-2 cents/test) — and this is now the entire AI bill for grading, not a second bill on top of a first. Applying Part 06 (dropping AI entirely for the 7 one-answer types) would still cut this further, for zero loss in accuracy.

Testing is currently happening on the free tier, on purpose — the plan is to confirm accuracy first, and move to a paid plan once that's proven. The numbers above are what that paid plan would actually cost.

## 08. Can Other AI Actually Judge Pronunciation and Fluency? — Now Tested, Not Just Researched

**Short answer: yes, it can genuinely listen — but "listen and comment" and "produce a trustworthy pronunciation score" turned out to be two different things,** confirmed directly, not just theorized.

TIER A — WHAT WE ACTUALLY USE**Gemini, general audio-capable model**

Gemini accepts the raw audio file directly — not a transcript — and it's what's wired into this app today for the pronunciation/fluency comment on the results screen. Because it actually receives the sound, it can perceive pace, pauses, stress, and accent, and on real recordings it gives genuinely sensible feedback (e.g. *"good clear pacing, though articulation on a few ending consonants could be sharper"* on an actual test recording).

The reliability problem — found, then fixed.

We fed Gemini the exact same silent recording six times in a row. It correctly said "no audio was provided" only twice; the other four times it confidently invented specific pronunciation feedback for audio containing nothing at all. The cause turned out to be the prompt itself: it was telling Gemini what the learner was *supposed* to say before asking it to judge the recording, which made it anchor on "they probably said this" instead of critically checking whether anything was said at all.

**The fix:** stop telling it the expected text, and explicitly ask it to verify real speech is present *before* judging anything. Re-tested the same way — 6 repeats on silence, several more on real recordings — and it came back correct every single time in both directions, with real scores properly on the stated 0–1 scale.

TIER B — NOT YET ADOPTED**Purpose-built pronunciation-assessment APIs**

**Azure AI Speech (Pronunciation Assessment)** and **SpeechAce** are not chat models at all — they're speech-scoring engines built for exactly this one job. They analyze the actual audio at the phoneme (individual sound) level and return real numeric scores for **accuracy, fluency, completeness, and prosody** — the same kind of engine behind the speaking exercises in apps like Duolingo. Voice detection there is mechanical, not a guess, so it can't make the mistake above.

This is the industry-standard tool for what's actually being asked here — "assess pronunciation and fluency" is a scoring problem these were built to solve, not a side skill of a general chat model.

**Where this leaves us:** Tier A is what's live today, and its reliability problem is now fixed and re-verified. That fix held up well enough that it's now gating the actual level too, not just showing feedback — see Part 10. Tier B remains the more rigorous, mechanically-verified option if this one ever needs replacing.

## 09. Cost Comparison: Every Audio-Capable Option

| Model | Tier | Cost | Notes |
| --- | --- | --- | --- |
| **Gemini 3.5 Flash-Lite** | A | $0.30 / $2.50 per M tok | **What this app uses today** — for both content grading and pronunciation/fluency. Free tier. One flat rate for text or audio input. |
| **GPT-4o-mini-audio-preview** | A | $0.15 (text) / $0.60 per M tok | Cheap, no free tier. Not adopted. |
| **GPT-4o-audio-preview** | A | $40 / $80 per M tok | Far pricier than what's actually in use. Not adopted. |
| **Azure Pronunciation Assessment** (batch) | B | ~$0.18 / audio hour | Real phoneme-level score included at no extra charge in batch mode. Not adopted. |
| **Azure Pronunciation Assessment** (real-time) | B | ~$1.30 / audio hour | Same scoring, live/interactive use. Not adopted. |
| **SpeechAce** | B | custom quote | Purpose-built, used by language-learning apps; pricing isn't published, requires contacting sales. Not adopted. |

**Bottom line:** genuinely certified pronunciation/fluency scoring (Tier B) isn't expensive either — Azure's batch tier would cost about the same as what Gemini costs today. The real choice was never about price. Tier A is already built, shipped, and — since Part 08's fix held up — trusted enough to affect the actual level (Part 10). Tier B remains the correct upgrade if that trust is ever broken again.

## 10. How We Calculate a Learner's Overall Level

Everything above is about grading one answer at a time. This part is different: it's how all those individual right/wrong results get turned into a single CEFR level (A1 through C2).

### The climb

The test starts every question type at B1, the middle of the scale. A correct answer steps that type one level harder; a wrong answer (or a skip) steps it one level easier. After the test, every answer — across every skill — gets pooled together by difficulty level, and the level is assessed by climbing from the easiest level that was actually tested upward, stopping at the first real ceiling.

### The bar gets stricter as you go up

A level only counts as "passed" if accuracy at that level clears its own bar — and the bar is not the same at every level:

| Level | Accuracy needed |
| --- | --- |
| A1 / A2 | 60% |
| B1 | 65% |
| B2 | 70% |
| C1 | 85% |
| C2 | 95% |

A true beginner making basic mistakes is expected and not disqualifying; calling someone "near-native" off a bare pass would hand out the top of the scale too cheaply.

### Five rules that decide where the climb stops

- **Minimum evidence:** a level needs at least 2 questions tested at it before its accuracy counts for anything. One lucky (or unlucky) question can't decide a level by itself.
- **One dip is forgiven, two in a row isn't:** falling just short of a level's bar once is treated as noise from a small sample. Falling short twice in a row, or a flat 0% at any level, stops the climb there for good — nothing tested beyond that point counts.
- **A skipped question counts as wrong, not neutral — but only at a level you actually engaged with.** Leaving most of the test blank can no longer produce a high level just because the few questions actually answered went well. But a level where *every single question was skipped* — zero real attempts, not even one — is now treated as genuinely untested, not failed. Before this fix, an entirely-untouched level read exactly like a real 0%, which could kill the whole climb before it even reached levels the learner did well on. Now it's excluded from the evidence entirely, the same as a level that was simply never served. (Per-skill breakdowns already had a version of this — a skill with zero answers is honestly labeled "not enough answered," never given a fabricated score.)
- **A spoken answer's pace has to be realistic too.** Content still has to be right, but for a mic answer that's no longer sufficient by itself — the speech rate (words ÷ recording length) also has to fall in a plausible human range, roughly 70–220 words per minute. Outside that band, the answer counts as wrong regardless of how correct the words were. This check is plain arithmetic, not AI, so there's no reliability question about it.
- **Pronunciation and fluency now count too — added once Part 08's reliability fix held up under re-testing.** For a mic answer, Gemini's pronunciation and fluency scores each have to clear the *same* per-level bar as content — a C1 question demands near-flawless delivery, an A1 one tolerates real roughness. Verified both directions with a real live test: the same correct answer, spoken over silent audio, failed (0/0 pronunciation, correctly caught); the same answer over a real recording passed (0.85/0.9). A signal that's missing entirely (no audio sent) never counts against the learner — only a signal that's present and bad does.

The final level is the highest point reached before the climb stopped — not simply "the hardest question you happened to get right."

**A structural fact worth knowing:** we checked the actual test configuration directly, and no question type currently has enough questions budgeted to ever reach a C2-difficulty question — the hardest any type can go, even with a perfect run, is C1. C2 isn't reachable by anyone right now, not because of a scoring bug, but because the test itself never serves a C2 question. Reaching it would need at least one type's question budget raised to 4 or more.

This only works because Part 08's fix actually held up.

Pronunciation/fluency gating correctness is only as trustworthy as the audio judgment behind it. If Gemini's real-audio scoring ever starts looking inconsistent again — a confident score on a recording that doesn't deserve it — this rule is the first place to check, since it's now directly deciding right/wrong, not just showing feedback.

## 11. Pronunciation and Fluency Are Not the Same Thing

Say a learner answers: *"I went to the market yesterday."* There are two genuinely different things worth checking, and no single number measures both:

- **Pronunciation** — did they produce the individual sounds correctly? Did "market" and "yesterday" come out with the right sounds, the right /r/, /t/, /d/?
- **Fluency** — did they speak naturally and smoothly? Did they pause constantly, speak far too slowly, or lean on "um"/"uh" throughout?

A learner can be perfectly clear on one and weak on the other — a strong accent with zero hesitation, or flawless individual sounds delivered in a halting, broken rhythm. That's why the methods below split cleanly into "measures pronunciation" and "measures fluency," rather than one tool claiming to do both.

## 12. How Pronunciation Scoring (GOP) Actually Works

The Tier B engines from Part 08 (Azure, SpeechAce) run on a speech-science technique called **Goodness of Pronunciation (GOP)**. Say a learner says "cat" — broadly, three sounds: **/k/ + /æ/ + /t/**. The engine measures how closely the learner's actual /k/ matches the expected /k/, then the same for /æ/, then /t/, and turns that into a phoneme-level, then word-level, score.

1. Learner audio comes in.
2. Speech recognition identifies the individual sounds (phonemes) that were produced.
3. Each sound is compared against the expected native-speaker version.
4. That comparison becomes a phoneme-level, then word-level, pronunciation score.

This is why it's a **measurement, not an opinion** — a genuinely different kind of tool than a general chat model listening and describing what it heard.

**One correction worth making precisely:** it's not really "AI vs. no AI" — Azure and SpeechAce are themselves built on machine-learning acoustic models internally. The real distinction is **general-purpose AI** (a chat model giving its best subjective impression of audio) vs. **purpose-built speech-assessment technology** (phoneme-level acoustic comparison, engineered for exactly this measurement). Both use AI under the hood; only one was built specifically to score pronunciation.

A GOP score is an input, not automatically "the score."

Plugging in Azure or SpeechAce doesn't hand this platform a ready-made Versant-equivalent result. It gives one signal — pronunciation accuracy — that would still need to be combined with content accuracy, fluency signals, and this platform's own pass/fail logic to produce an actual level or readiness result. Treat it as one measurement feeding the scoring system, not the scoring system itself.

## 13. Three Free Fluency Signals

These don't need a paid engine, or any AI at all — they can be computed directly from data already being captured (the audio and the transcript).

### Speech rate

Words per minute, from the transcript and the recording's length.

Example: "I went to the office this morning" (7 words) in 4 seconds7 ÷ 4 × 60

Speech rate≈ 105 WPM

Neither extreme is good: word-by-word delivery signals hesitation, while words run together with no separation signal reduced clarity. The right move is a reasonable band, not "higher is better" — and that band should come from real testing data, not a guess.

### Pause detection

Silence gaps found directly in the raw audio waveform — count, average length, longest gap. This is exactly why the audio itself needs to be kept, not just the transcript: two learners can produce the identical transcript ("I went to the market yesterday because I needed some vegetables") while one said it in one continuous breath and the other paused after nearly every word. A text-only grader sees identical words; only the audio shows the real difference in delivery.

### Filler word count

Counting "um," "uh," "like" directly in the transcript. Crude on its own — some filler words are just normal, natural speech — but a real, recognized fluency signal when combined with the other two.

## 14. ASR Confidence Scores — A Rough Proxy, Not a Pronunciation Score

Most speech-to-text engines — including the one already used here — quietly produce a per-word confidence number alongside the transcript:

"The"0.98

"weather"0.94

"is"0.99

"beautiful"0.71

"today"0.96

It's tempting to read that low 0.71 as "mispronounced" — **but that's not a safe conclusion.** Background noise, a poor microphone, an accent, speaking too quietly, or just an unusual recording moment can all drag confidence down for reasons that have nothing to do with pronunciation. It's a free, already-available proxy — genuinely useful as one signal among several — but a rough one, not a real pronunciation measurement on its own.

## 15. Human Raters

The traditional gold standard, and still what real high-stakes exams (IELTS, official Versant assessments) fall back on. A trained person can hear things automated systems miss — most importantly, telling a strong accent that's still perfectly clear apart from a genuine pronunciation problem, something a simplistic automated score might penalize unfairly.

**The limitation is pure scale.** 10,000 learners × 20 speaking responses is 200,000 recordings — someone has to listen to and score every one, which means real cost, slower results, and consistency challenges between different raters.

## 16. Comparing Every Method

| Method | Pronunciation | Fluency | Cost | Speed |
| --- | --- | --- | --- | --- |
| Dedicated engine (GOP) | Strong | Limited | Paid | Fast |
| Speech rate | — | Yes | Free | Instant |
| Pause detection | — | Yes | Free | Instant |
| Filler counting | — | Yes | Free | Instant |
| ASR confidence | Rough proxy | Limited | Usually free | Fast |
| Human rater | Strong | Strong | Expensive | Slow |

These don't have to compete — they combine. A recording can be run through several of these at once, each producing one signal that feeds into this platform's own scoring logic, rather than picking a single "winner" method.

## 17. How This Would Fit Into the Platform

Three layers, building on what already exists:

1. **Capture** — the microphone recording, already built for Repeats and every other spoken item type.
2. **Analysis** — that same recording run through ASR (transcript + confidence), a pronunciation engine (phonemes), and simple audio analysis (pauses, speech rate, fillers) — three parallel signals from one recording.
3. **Scoring logic** — this platform's own rules combine content accuracy, pronunciation, and fluency signals into a result — a "Speaking: B1" readiness level, say, with plain feedback like "clear pronunciation; try reducing long pauses."

Three practical tiers to build toward, each one usable on its own:

MINIMUM COST**Use what's already captured**

ASR transcript + confidence, plus speech rate/pauses/fillers computed from the existing recording. Real fluency insight, zero new paid service.

MORE ADVANCED**Add a dedicated pronunciation engine**

Azure or SpeechAce alongside the above, for genuine phoneme-level pronunciation scoring rather than the rough ASR-confidence proxy.

HIGH-STAKES**Add human review for the disputed/low-confidence cases**

Route anything the automated scoring is unsure about — a low ASR confidence run, a borderline pronunciation score — to a person for a final check, rather than trusting the automated number outright at the edges.

## 18. Which Gemini Model, and Why This One Specifically

### The different "tiers" of Gemini models

Google doesn't make just one Gemini model. Within each generation (like "2.5" or "3.5"), there are usually three tiers of the same generation:

- **Pro** — the smartest, slowest, most expensive.
- **Flash** — a solid middle ground, fast and capable.
- **Flash-Lite** — the cheapest and fastest, a bit less capable, but plenty good for straightforward tasks like grading.

### What we use, and why

**gemini-3.5-flash-lite** — the cheapest tier of the newest generation we've confirmed actually works. Grading answers and giving pronunciation feedback aren't especially hard reasoning tasks — they don't need the smartest, most expensive model. We tested Lite directly and it gave consistent, sensible results, so paying more for Pro or full Flash wouldn't buy anything we've shown we actually need.

### Other Gemini voice models, and why none of them apply here

Beyond the general-purpose Flash/Pro tiers above, Google also sells several models built specifically around voice — worth naming so it's clear they were considered, not overlooked:

- **Gemini 3.1 Flash TTS** — a text-to-speech model: it turns written text *into* spoken audio, with over 200 expressive tags (`[whispers]`, `[laughs]`, pace controls) across 70+ languages.[1,2] This runs in the opposite direction from what we need — it produces AI speech, we need to understand a *learner's* speech. The one place this app plays audio to a learner (reading a question aloud) already uses the browser's own free, built-in text-to-speech. A paid, expressive voice model would add cost for expressiveness this app has no use for.
- **Gemini 3.8 Live** — a real-time conversational voice agent: low latency, realistic accents, 97 languages, can even switch languages mid-conversation.[3] Built for live, back-and-forth spoken conversations — a voice assistant you talk *with*. This app is one-shot: record an answer, submit it, get graded afterward. There's no live conversation happening, so the entire point of this model doesn't apply.
- **Gemini 3.5 Transcribe** — a dedicated speech-to-text model: a 2.6% average word error rate, automatic filler-word filtering, handles accents and custom vocabulary.[4] This is the one actually closest to something we use — except transcription here currently runs for free, on the learner's own device, via the browser's built-in speech recognition, not any Gemini model at all. It could be a genuine future upgrade for accuracy, but there's a real catch specific to this project: it *auto-filters out* filler words like "um" and "uh" — and we deliberately count those as a real fluency signal (Part 12). Adopting it as-is would quietly break a feature already built, unless that filtering can be turned off.
- **Pre-built voice options** (Zephyr, Puck, Charon, Kore, etc.) — different AI voice personalities used by the TTS/Live models above. Since this app doesn't generate expressive AI speech at all, which voice sounds "bright" or "firm" is irrelevant here.

### Cost of each model we've actually checked

| Model | Input | Output | Status |
| --- | --- | --- | --- |
| Gemini 2.5 Flash-Lite | $0.30 / M tok | $0.40 / M tok | Rejects new callers — can't use it (see below) |
| Gemini 2.5 Flash | $1.00 / M tok | $2.50 / M tok | Not used — more expensive, no shown need for it |
| **Gemini 3.5 Flash-Lite** | $0.30 / M tok | $2.50 / M tok | **What we use now** |

No numbers are given for the Pro tier or the newer non-Lite Flash versions (3.5/3.6/3.7/3.8 Flash) — their current price hasn't been checked and they haven't been tested for this task, so this report won't guess at either.

### Why not 2.5, in full detail

1. The code originally used **gemini-2.5-flash-lite** — the model named in the earlier cost research, and the best price at the time.
2. The very first real call to it didn't return a score — it returned an outright rejection: *"This model models/gemini-2.5-flash-lite is no longer available to new users. Please update your code to use models/gemini-3.5-flash-lite."*
3. Google didn't delete the model — it still works for accounts already using it before some cutoff date. This project's API key is a fresh, new user of it, and Google blocks new users from starting on it. Like a phone plan still active for existing customers but no longer sold to new ones.
4. So this isn't 2.5 being worse, cheaper, or better in some meaningful way — **we simply weren't allowed to use it at all.** Google's own error told us exactly which model to switch to, and it was confirmed working with a real test call before being adopted.

**A related, unresolved decision:** the model name above is hardcoded. Google also offers a rolling alias, `gemini-flash-lite-latest`, that always points to the current Flash-Lite model automatically, so this exact "model got discontinued" problem couldn't happen again. The tradeoff: the model could then change behavior underneath the app without warning, instead of only changing when someone deliberately updates the version. Kept as the fixed version for now, by choice, not oversight.

Sources for the voice-model claims above (published specs, not independently tested by us): [1] [blog.google — Gemini 3.1 Flash TTS](https://blog.google/innovation-and-ai/models-and-research/gemini-models/gemini-3-1-flash-tts/) · [2] [cloud.google.com — Gemini 3.1 Flash TTS on Google Cloud](https://cloud.google.com/blog/products/ai-machine-learning/gemini-3-1-flash-tts-on-google-cloud) [3] [extremetech.com — Google's real-time conversational voice models](https://www.extremetech.com/computing/google-launches-new-voice-ai-models-for-building-real-time-conversational) [4] [blog.google — Gemini 3.5 Transcribe](https://blog.google/innovation-and-ai/models-and-research/gemini-models/gemini-3-5-transcribe/)

Pricing reflects published rates as of September 2026 and will change over time — re-check `ai.google.dev/gemini-api/docs/pricing` and `azure.microsoft.com/pricing/details/speech` before using these figures for a future budget decision.
