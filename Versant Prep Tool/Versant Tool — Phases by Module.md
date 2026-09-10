---
tags: [versant-tool, spec, phases, roadmap]
---

# Versant Practice Tool — Phases by Module

> [[00 - Index|← Back to Index]]

No timeline here — just the phases, in order, and the database tables each one actually uses. Each phase is one working piece, small enough to build and check on its own, and only starts once the phase before it actually works.

**This list will grow.** When a new feature gets planned that isn't here yet — another exam type, employer accounts, whatever comes next — it gets its own new phase number when it's actually being built, not before. The numbering isn't final; it's just "in order so far."

---

### PHASE 0 — Foundation
- Project set up — frontend and backend connected
- Database created and connected
- Basic error handling, logging, security

**Database:** all 31 tables get created now, empty. Cheap to do once, expensive to add later.
**Needs:** nothing — this is the starting point.

---

### PHASE 1 — Authentication
- Sign up, log in, log out
- Email verification
- Password reset
- One login system for everyone — a `role` decides what they see next: **learner** (the product itself), **org_admin** (a contact at a client company/university, sees only their own organization), **admin** (your staff — onboards/manages organizations across the platform; sales, delivery, L&D are just a label on the account, not different permissions yet), or **super_admin** (oversees admins). No reviewer role — content is added pre-approved, directly, by the team

**Database:** `users` (now with `role` and `department`), `auth_sessions`, `password_reset_tokens`, `email_verification_tokens`
**Needs:** Phase 0.

---

### PHASE 2 — Onboarding & Consent
- The goal questionnaire — exam choice, target CEFR level, exam date, prep days, daily minutes, past attempts
- The consent screen — required before any recording happens later

**Database:** `participant_profiles`, `consent_records`, `external_results` (for past exam attempts entered here or later from the dashboard)
**Needs:** Phase 1 — a logged-in learner to ask.

---

### PHASE 3 — Placement Assessment
- The placement test itself — graded questions, real timers
- Simple scoring for now (exact match / did they respond in time) — not real AI yet
- A CEFR level result
- The "you may already be at your target level" check

**Database:** `placements`, `item_types`, `items`, `item_audio` (placement questions only), `sessions`, `attempts`, `scores` (placeholder scores)
**Needs:** Phase 2 — a stated goal to compare the result against.

---

### PHASE 4 — Personalized Roadmap
- Turns their level, goal, exam date, and available time into a study plan with milestones

**Database:** `roadmaps`, `roadmap_milestones`
**Needs:** Phase 3 — a placement result to build from.

---

### PHASE 5 — Practice Engine
- The 3 core exercise types: Repeats, Sentence Builds, Dictation
- Exam mode — real timers, no replay, no going back, matches real exam rules

**Database:** full `item_types`/`items`/`item_audio` (all 3 exercise types now), more `sessions` and `attempts` (practice sessions, not just placement)
**Needs:** Phase 4 — the roadmap says what to practice.

---

### PHASE 6 — Progress Tracking
- Session history
- Streaks
- Completion badges (first session done, tried all 3 exercise types)

**Database:** `session_log`, `activity_log`, `progress_stats` (completion fields), `achievements`, `user_achievements` (completion-based ones only)
**Needs:** Phase 5 — real sessions to track.

---

### PHASE 7 — Dashboard
- Brings it together in one screen: current level, goal, plan progress, streak, badges, what to practice next

**Database:** no new tables — just reads everything Phases 3–6 already created.
**Needs:** Phase 6 — real data to display.

---

### PHASE 8 — Real AI Scoring & Feedback
- Connect a real speech and writing scoring service
- Replaces the placeholder scoring used since Phase 3
- Real per-answer feedback and a real readiness level
- Badges/levels that depend on real skill improvement turn on here

**Database:** `scores` (now real), `features`, `readiness`, `skill_stats`, `review_queue` (low-confidence attempts), remaining `achievements`/`user_achievements` (skill-based ones)
**Needs:** Phase 5 — real practice attempts to score.

---

### PHASE 9 — Coach Mode
- A voice feature — the learner talks, the Coach responds and corrects
- Corrections always come from real scoring, never guessed by the AI

**Database:** `coach_turns`
**Needs:** Phase 8 — real scoring to correct against.

---

### PHASE 10 — Data Retention & Deletion
- Auto-delete old voice recordings on a schedule
- Let a learner delete their account or download their own data
- A log of who accessed a recording, and when

**Database:** `access_logs` — plus the deletion job reads `attempts.audio_expires_at`, already sitting there since Phase 3/5
**Needs:** Phase 5 — there needs to be real audio to manage.

---

### PHASE 11 — Security & Launch
- Testing the important paths — login, timing, scoring
- Monitoring and backups
- Deploy to a real server

**Database:** no new tables — this phase is about running the existing ones safely.
**Needs:** everything above.

---

## Reserved — tables and roles that already exist, but have no phase yet

These were built into the database on day one (Phase 0) because adding them later, after real data exists, is much harder than adding them now. They stay empty/unused until a real phase is planned for the feature that needs them — none of that is being built yet.

- `exams`, `exam_parts` — for when there's more than one exam product to prepare for, not just Versant.
- `cohorts`, plus the `org_admin`, `admin`, and `super_admin` roles and the `tenant_invitations` table (decided 2026-08-28) — for when a real company or university is actually ready to be onboarded. The roles and the invite mechanism exist in the database now, and the full login/onboarding flow is designed (see [[Login, Roles, Permissions & Onboarding]]), but there's no organization-onboarding screen, no admin dashboard, and no permission controls built yet. That's real, separate work for the day an actual organization is waiting.

When one of these becomes real work, it gets its own new phase number, inserted where it actually belongs in the order above — not tacked onto the end.

---

## Connects to
- [[Login, Roles, Permissions & Onboarding]] — the full spec for the roles/permissions this phase list only sketches (Phase 1)
- [[BRD — Versant Prep Tool]] / [[FRD — Versant Prep Tool]] — underlying locked requirements
