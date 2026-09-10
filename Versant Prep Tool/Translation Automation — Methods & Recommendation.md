---
title: Translation Automation — Methods & Recommendation
tags: [discussion, frontend, localization, tooling]
status: open
date: 2026-09-01
related: ["[[Localization — The Non-English B2C Learner Case]]", "[[Translation — Complete Guide (Costs & Methods)]]", "[[FRD — Versant Prep Tool]]"]
---

# Translation Automation — Methods & Recommendation

> [[00 - Index|← Back to Index]]

Answers one specific slice of [[Localization — The Non-English B2C Learner Case]] §6 ("what it would actually take to build this"): if UI text ever gets translated, how would the translation files themselves actually get made? Nothing here is decided or scheduled — same status as the note it extends. For full pricing detail and the self-hosted free option, see [[Translation — Complete Guide (Costs & Methods)]].

**Scope reminder, unchanged from every other note on this topic:** only UI text (buttons, labels, menus, consent copy) is ever a candidate for translation. Exam item text and item audio are never included, under any method below — this is the same permanent boundary [[Localization — The Non-English B2C Learner Case]] §1 describes, not a decision this note revisits.

---

## The two main methods

### Method 1 — a script that auto-generates translation files

Write one English file. A script reads it, sends every string through a translation API (Google Translate in the reference example), and writes out a matching file for every target language, in one run.

**Strengths:** cheap to set up (pay only per-character API cost), fast, full control over the script.
**Weaknesses:** no tracking of what changed since the last run — re-running blindly overwrites everything, including any manual fix a human made to a bad translation. No review workflow built in.

### Method 2 — a translation management platform

A hosted service (Lokalise, Crowdin, Phrase, Locize) connects to the English source file, auto-detects new/changed strings, machine-translates only what's new, and gives a human reviewer a proper interface to approve or fix each one before it ships.

**Strengths:** never blindly overwrites reviewed work, built-in review workflow, scales cleanly as strings keep changing, team-friendly.
**Weaknesses:** costs a recurring subscription, more setup (connecting repo/CI), overkill before strings are actually changing on an ongoing basis.

## Recommendation

**Start with Method 1 if this is ever built.** The FRD currently has non-English languages out of Phase 1 scope entirely — there's no live product with real users yet needing ongoing translation maintenance. A script gets a complete, working translation set cheaply whenever localization is actually decided. **Switch to Method 2 only once localization is live and strings are changing regularly enough that "who reviewed what, and did I just overwrite a fix" becomes a real, recurring problem** — that's the specific pain Method 2 solves, and it isn't a problem that exists yet.

## Other methods, briefly

| Method | When it's worth it |
|---|---|
| **DeepL API** | Same script pattern as Method 1, different engine. Often better quality for European languages — check it actually covers the target language first (~30 languages vs. Google's 100+) |
| **LLM-based translation** (Claude/OpenAI API, given context like "this is a button, keep it short") | Best for a small number of high-visibility strings — the consent screen, key buttons — where natural phrasing matters more than bulk speed/cost |
| **Amazon Translate / Azure Translator** | Only worth it if already using AWS/Azure for other infrastructure — this project is on Cloudflare R2 + EC2 (FRD §7), so no strong reason to pick these |
| **Crowdsourced/human translation** | Once machine translation quality isn't good enough on its own — particularly relevant for consent/legal text specifically |

## If this is ever actually started

1. Confirm English strings are cleanly organized in one source file (`src/locales/en/translation.json` or equivalent) before anything else — see the note on the current actual state below.
2. Write the Method 1 script, listing real target languages — for this project's stated market, that likely starts with **Hindi**, and possibly Tamil/Telugu/Bengali depending on candidate concentration.
3. Run it once — a working translation file per language, with zero hand-typed translation.
4. Have a native speaker specifically spot-check the consent screen and main buttons — the one manual step worth doing regardless of method, since consent text (FR-011/C3) has real stakes if it reads unclearly. Matches [[Localization — The Non-English B2C Learner Case]] §5's point that the consent screen is the highest-stakes item in the whole set.
5. Add a language switcher so learners can actually select their language.
6. Only once live and strings are changing regularly — evaluate moving to Method 2.

## A discrepancy worth flagging

The source material for this note claims UI components "already call `t('buttons.startSession')`" — i.e., that the `react-i18next` migration has already happened. That contradicts the Frontend Architecture note's actual `status: resolved` decision, which documents the current state as plain JS objects (`UI_TEXT.buttons.startSession`), with the `t()`/`locales` pattern named explicitly as a *future* migration path only if localization is ever built — not something already in place. Treat the Frontend Architecture note as the source of truth on current code state; this note's own "step 1" above exists specifically because that migration has *not* happened yet.

## Connects to
- [[Localization — The Non-English B2C Learner Case]] — the note this answers one part of
- [[Translation — Complete Guide (Costs & Methods)]] — full pricing tables and the self-hosted free option
- [[FRD — Versant Prep Tool]] — FR-011/FR-012 (consent), the technology stack in §7
