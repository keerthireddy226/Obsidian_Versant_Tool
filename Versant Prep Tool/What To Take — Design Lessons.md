---
tags: [versant-tool, reference, synthesis, design-lessons]
---

# What To Take — Design Lessons

> [[00 - Index|← Back to Index]]

Consolidated lessons pulled from every competitor reviewed (general AI speaking apps + Versant-specific sites), with the site each came from. See [[Complete Guide — English & Versant Platforms]] for the fullest per-site write-up this was distilled from, and the "How the research connects to the spec" section of [[00 - Index]] for which of these became actual FRD requirements.

---

## Ideas worth copying

**Show a quick example before every new question type.** *[[Versant Prep Sites — Walkthrough|VersantPrep]]* — before a new task type, shows rules in plain English plus one worked example, then "Got it, start." Cheap to build, meaningfully reduces confusion. → Became **FR-034** in the FRD, cited as "the strongest single idea found across a wide competitor review."

**Lock the timer on your own server, not the browser.** *Mocko* — controls the countdown from their own server, so a learner can't cheat by pausing the browser or altering the system clock. Confirms the product spec's server-authoritative timing (FRD FR-021) is the right call.

**Stop cheating by switching tabs.** *Mocko* — if a learner clicks to a different tab during a timed question, Mocko locks them out and keeps the timer running anyway, so leaving costs time twice. → Became **FR-035**.

**Process recordings in-browser instead of uploading.** *MyEnglishPractice.jp* — nothing recorded/typed on their basic tools is sent to a server at all. Good for privacy, one less thing that can break.

**Never use multiple-choice for a serious test.** *Mocko* — zero multiple-choice questions anywhere; every item demands a real typed or spoken answer, matching how Versant actually works and making it much harder to cheat or guess through. → Reflected in the FRD's rule that no Phase 1 task shall be implemented as multiple-choice.

**Meet people where they're already job-hunting.** *VersantPro* — runs a WhatsApp group sending real job openings (Amazon, Teleperformance, TCS) alongside practice questions, putting the product in front of people at the exact moment they're job-hunting.

**Show real hiring numbers, not just an abstract score.** *VersantPrep* — "companies like Amazon and TCS usually want 65 or higher," not just "your score is 68." Makes the score mean something real.

**Track hesitation signals like backspace count.** *MockVersant* — quietly counts backspace presses during typed answers, a small extra signal of uncertainty. → Became **FR-127** (recorded as a feature value, explicitly never a score).

**A clear pre-test checklist builds trust.** *CA Monk* — mic check, "don't refresh," "submit only when sure," before the real test begins.

**Bundle a path to the real test booking, and offer a corporate certificate.** *VERSANT Bridge* — resells discounted real Versant test tickets in-app, and offers corporate completion certificates on request — directly relevant since the buyer here is the employer, not the individual candidate.

**Be openly honest about scoring limitations.** *Mocko* — explicitly admits it doesn't grade pronunciation yet, only checks what was typed/said as text. Better than quietly overselling.

**Zero-signup-to-first-question.** *MockVersant* — click a section, see one instructions screen, land directly on "Question 1 of 20." Gold standard for removing friction — worth aiming for in at least a "try one question free" flow.

## Mistakes worth avoiding

**Never show positive feedback that contradicts the actual score.** *VersantPrep* — a flat zero score once still displayed "Excellent pronunciation, your speech is clear and natural!" Any canned feedback string needs a guard clause checked against the real number before it's shown.

**Never let a required input create a permanent dead end.** *Skillioma* — a required audio-file upload that didn't work left the walkthrough stuck forever, with no way to skip or move on.

**Don't gate your own free tier behind something users can't reach.** *CA Monk* — the test costs in-app "coins" neither researcher had enough of, so the real questions were never seen.

**Don't fake popularity or reviews.** *VersantPro* — a "students practicing right now" counter that increases on its own on every page refresh, plus reviews with suspiciously exact before/after scores.

**Never claim to be "official."** *MockVersant* — literally says "OFFICIAL MOCK PLATFORM" at the top, which isn't true; only Pearson can say that. Directly reflected in the product's constraint against any claim of Pearson affiliation or endorsement.

**Don't let users adjust their own scoring strictness.** *Praktika* — offers soft/medium/strict correction. Wrong for a test-prep tool whose scoring must match the real rubric exactly, not a dial the user controls.

**Don't let failures happen silently.** Recurring mic bugs (*Univerbal*) and unclear wrong-answer states (*TalkPal*) — a silent failure during a real scored attempt is much worse than during casual practice.

**Never let feedback ignore what the person actually answered.** *Skillioma* — generic, copy-paste "Failed" result unrelated to the actual response. The clearest bad example of feedback quality found across all research.

---

## The gap every single competitor shares

Across roughly 29 sites and apps reviewed (general + Versant-specific), **every one is built for one person practicing alone.** None have: employer batch dashboards to see how a whole cohort of candidates scored, serious handling of many candidates' personal data at scale, a B2B pricing model, or a trustworthy certificate/report a company can use for a hiring decision. See [[Market Gap & Positioning]] for what this means for the product's actual positioning.
