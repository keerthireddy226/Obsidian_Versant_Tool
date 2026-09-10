---
title: Localization — The Non-English B2C Learner Case
tags: [discussion, frontend, localization, b2c, open]
status: open
date: 2026-09-01
related: ["[[BRD — Versant Prep Tool]]", "[[FRD — Versant Prep Tool]]", "[[Gamification & Dual-Market Expansion — Proposed Direction]]"]
---

# Localization — The Non-English B2C Learner Case

**The question this note answers:** imagine someone in France signs up to use this app on their own (not through an employer). They're a true beginner in English — level A1. Would the app make sense to them? What would need to be in French, and what absolutely has to stay in English?

Nothing here is decided yet. This is just working out the idea clearly before anyone decides whether to build it.

## The short answer, upfront

**Stays in English, always, no matter what:**
- The test itself — the questions, the audio, the answers
- Everything about how scoring works
- The current plan and rules for the whole project

**Would need to be built, only if this ever becomes a real feature (not planned yet):**
- A place to save which language each learner prefers
- French (or any other language) versions of the buttons, menus, and instructions
- A smarter way to translate messages that include numbers, like "you started 2 seconds late"
- Someone to check that the translated legal/consent text still means the same thing

## 1. The parts that can never be translated

Think about *why* someone is using this app: to prove they can understand and speak English well, fast, under pressure. So anything that's actually part of that test has to stay in English, or the test stops measuring the thing it's supposed to measure.

| What it is | Why it can't change |
|---|---|
| The sentences you hear and have to repeat | That's the actual test |
| What you typed, and the "correct answer" shown next to it | You can't compare an English answer to a French one |
| What the AI coach says out loud to correct your pronunciation | It's teaching you to say English sounds correctly — correcting that in French makes no sense |
| The legal notice saying "we are not Pearson/Versant" | This is a legal statement — if it's ever translated, a real translator needs to check it, not just anyone who speaks the language |

**One tricky trap to avoid:** someone might think, "let's just show a French caption under the English audio, to help a bit." That would actually break the test. The whole point of that question is: *can you understand this just from listening?* Giving them anything to read — even in French — defeats that, the same way giving them the English words to read would.

## 2. The parts that would definitely be translated

These are just the buttons, menus, and instructions — the stuff around the test, not the test itself:

- Menu items like "Dashboard," "Practice," "History"
- Buttons like "Start," "Submit," "Try Again"
- The one-time "here's how this works" screens shown the first time you try something new
- The consent screen (what we record, why, how to delete it)
- The sign-up questions ("what's your goal score," "when's your exam")
- Badge names, like "7-Day Streak"

All of these are just fixed pieces of text. Translating them is genuinely simple — like swapping out a label. Every one has one version in English and one version in French, and the app just shows whichever one matches the learner's chosen language.

## 3. The tricky middle part — messages built on the spot

Some messages aren't fixed text — they're built fresh each time, using real numbers about what you just did. For example:

> "You started speaking 4.8 seconds into a 6-second window — try starting sooner."

The "4.8" and "6" are different every single time. You can't just write one French version of this sentence and reuse it, because the numbers keep changing. To translate this properly, you'd need to translate the *sentence pattern* ("You started speaking ___ seconds into a ___-second window") and then plug the numbers in — for every language. That's a real piece of engineering, not a quick copy-paste job like Part 2 above.

The same problem applies to the personalized study plan text (like "focus on Speaking this week") — it's also built fresh for each person.

## 4. A part that's easy to forget: things the learner typed themselves

Some information on the app isn't the app's text at all — it's the learner's own words. Things like:
- Their name
- What score they're aiming for (someone might type "Versant 65+" or "IELTS 7.0")
- Notes like "about 6 months ago" for when they last took a test

This should just be shown back exactly as they typed it — never auto-translated. It's not app content, it's their own input, in whatever language they chose to type it in.

## 5. Why the consent screen matters most of all

Before we're allowed to record someone's voice, they have to properly understand and agree to it. That's not just a nice idea — it's a real legal requirement (in Europe it's called GDPR; in India, a similar law called the DPDPA).

Here's the problem: if someone can barely read English, and the consent screen is in English, did they *really* understand what they agreed to? Probably not. So out of everything in Part 2, the consent screen is the one that matters most to get right — it's not just about convenience, it's about whether the agreement is even valid.

## 6. What it would actually take to build this

Nobody has decided to build this. But if we did, here's honestly what it would involve. The "how would we actually generate the translations" piece has its own dedicated notes: [[Translation Automation — Methods & Recommendation]] (the short version) and [[Translation — Complete Guide (Costs & Methods)]] (full pricing detail and a genuinely free self-hosted option).

| What needs to happen | Why |
|---|---|
| A setting to save each learner's preferred language | So the app knows what to show them |
| French (etc.) versions of every button/menu/label | The simple translations from Part 2 |
| A way to build translated sentences with numbers plugged in | For the messages from Part 3 |
| Translated names/descriptions for badges | Small addition, same idea as Part 2 |
| A process to make sure a translated consent form is legally solid | Not just translated — properly checked |
| A lawyer's review of the translated consent and legal text | Same reason — this text has to be exactly right |
| A place in Settings where someone can change their language | So it's not locked in forever at signup |
| A decision about dates, numbers, and prices | A French user might want French text but still expect dates written differently, or prices in Euros — that's a separate question from translation |
| A decision about the public website too | Everything above is *after* signing in. Would the sign-up page and pricing page also need translating? Not addressed here |

## 7. Language is a personal choice, not based on where someone lives

It's tempting to think "this person is in France, so show them French." But that's not quite right — someone in France might actually prefer using the app in English, and someone in India might prefer Hindi. The app already handles a similar situation correctly elsewhere (it asks people to say what their first language is, rather than guessing). The same idea should apply here: let people choose their own language, maybe guess a starting default from their browser, but always let them change it.

## What's still undecided

Whether any of this actually gets built. Nothing here commits to it.

The reason this topic came up at all: originally, this app was only for people already applying to call-center jobs — people who'd already need to know *some* English to be in that situation. Now that individual people can sign up on their own, a total beginner really could show up. That's a genuinely different situation than before, and it's worth someone deciding, at some point, whether that's common enough to justify doing this work — or whether it's fine to wait and see first.

## Related notes
- [[BRD — Versant Prep Tool]] — the legal/consent rules (Appendix C) this note builds on
- [[FRD — Versant Prep Tool]] — the specific rules about consent, first-time instructions, and feedback messages this note refers to
- [[Gamification & Dual-Market Expansion — Proposed Direction]] — the decision that let individual people sign up on their own, which is what makes this scenario possible
