---
tags: [versant-tool, index, research]
---

# Versant Prep Tool — Research & Requirements Hub

> Competitive/UX research plus the locked product spec (BRD + FRD) for a Versant speaking-test preparation tool aimed at **employers** (BPO/call-center hiring in India/Philippines), not individual learners.

## What is this?

Research into (1) how AI speaking-practice apps handle engagement, feedback, and gamification in general, and (2) who already builds prep tools specifically for the **Versant by Pearson English Speaking and Listening Test** — plus the actual Business and Functional Requirements Documents this research fed into.

---

## The Product

- [[BRD — Versant Prep Tool]] — business context, objectives, stakeholders, assumptions, scope, constraints, risks, phasing
- [[FRD — Versant Prep Tool]] — system design, modes, task specs, scoring pipeline, Coach mode agent, data model, privacy, tech stack
- [[Gamification & Dual-Market Expansion — Proposed Direction]] — proposal (not yet in the BRD/FRD): adding gamification and targeting both B2C individual learners and B2B employers
- [[Versant Tool — Phases by Module]] — the actual build order, phase by phase, with which database tables each phase uses
- [[Login, Roles, Permissions & Onboarding]] — what each login (Super Admin/Admin/Org Admin/Learner) can see and do, plus every account-creation flow
- [[Localization — The Non-English B2C Learner Case]] — open discussion: does a low-level, non-English-speaking B2C learner change when UI localization becomes a real requirement
- [[Translation Automation — Methods & Recommendation]] — if localization is ever built, how the translation files would actually get made (script vs. platform)
- [[Translation — Complete Guide (Costs & Methods)]] — full pricing across every translation method, plus a free self-hosted option

## General AI Speaking Apps (Background Research)

- [[TalkPal — UX Engagement Analysis]] — full hands-on deep-dive of TalkPal (Premium account), all 9 modes rated
- [[AI Speaking Apps — Landscape]] — 15-app comparison table (Langua, Speak, Praktika, ELSA, Mintza, SpeakShark, etc.)

## Versant-Specific Competitors (Background Research)

- [[VERSANT Bridge — Competitor Deep-Dive]] — the closest true competitor: a Japanese company running the same "dedicated prep app" playbook across 3 English tests
- [[Versant Prep Sites — Walkthrough]] — hands-on walkthrough of 4 sites (Skillioma, VersantPrep, CA Monk, TDS Global Solutions)
- [[Versant Sites — Quick Reference Directory]] — one-line facts on all ~14 Versant-related sites found
- [[Complete Guide — English & Versant Platforms]] — master synthesis combining every general app + every Versant-specific site into one reference

## Reference & Strategy

- [[Versant Test — Official Task Structure]] — the real test's task types, timing, and scoring dimensions, sourced from Pearson's own documents (BRD Appendix A)
- [[What To Take — Design Lessons]] — consolidated do's/don'ts pulled from every competitor reviewed
- [[Market Gap & Positioning]] — why the B2B/employer angle is the open gap no competitor has built for

---

## How the research connects to the spec

The BRD and FRD were written *after* this research and cite it directly:
- BRD **Assumption A3** ("no incumbent AI-based Versant product exists") is marked **resolved false** on 2026-08-25, naming VersantPrep, Mocko, VERSANT Bridge, CA Monk, MockVersant, VersantPro, and MyEnglishPractice.jp — see [[Market Gap & Positioning]].
- BRD **Risk R6** ("incumbent owns the niche") is closed the same day with the resolution "reposition around B2B/compliance-rigorous delivery, not stop."
- FRD **FR-034** (instructions-plus-example screen before a new task type) is cited as "the strongest single idea found across a wide competitor review" — sourced from VersantPrep, see [[What To Take — Design Lessons]].
- FRD **FR-035** (lock interaction on browser-tab blur, keep the server timer running) — sourced from Mocko's tab-switch lockout.
- FRD **FR-127** (track backspace/delete count as a feedback signal, never a score) — sourced from MockVersant's hesitation-signal idea.

---

## Graph Key

Each node is a competitor, app, or research topic. Links represent "this informs that" — e.g. a competitor's flaw links to the lesson it produced in [[What To Take — Design Lessons]], and specific lessons link to the FRD requirement they became.
