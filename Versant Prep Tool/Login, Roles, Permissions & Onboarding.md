---
tags: [versant-tool, spec, roles, permissions, onboarding]
---

# Login, Roles, Permissions & Onboarding

> [[00 - Index|← Back to Index]]

**Updated:** 2026-08-28. What each login can actually see and do — dashboards, modules, permissions, and the exact steps that create every kind of account. Everyone logs in through the same single screen; what follows is what changes after that.

> **Status:** this is the functional spec, not built software. Learner login/practice/dashboard is being built now (see [[Versant Tool — Phases by Module]]). Super Admin, Admin, and Org Admin screens described here are designed but not built — they get their own real phase the day an actual admin or organization is waiting.

---

## 1. Super Admin Login

### Login & security
Same shared login screen and endpoint as everyone else. Password checked with bcrypt; a session is created the same way for every role. Forgot-password and the very first Super Admin's email verification work exactly like a learner's — a one-time, hashed, expiring link, never a password anyone else can see.

### Super Admin Dashboard
| | |
|---|---|
| **Platform statistics** | Total learners, total cohort tenants (organizations), total admins, sessions completed today, signups this week, active-now count. |
| **Overall activity** | A live feed of platform-wide activity — new signups, new organizations, admins created, content flagged for review. |

### What a Super Admin can do
| | |
|---|---|
| **Manage admins** | Create (invite), view, edit (name, department label), and activate/deactivate any Admin account. "Deactivate" reuses the same soft-delete field a learner account already has (`deleted_at`) — blocks login without destroying their history. |
| **Assign permissions** | Turn individual permissions on/off for any Admin — see the Role & Permission Matrix in §7 for the full list. |
| **View/manage organizations & learners** | Sees and can act on **every** cohort tenant and every learner on the platform — not limited to any one Admin's assigned organizations. |
| **Manage reviewers** | Grants the `content_review` and/or `score_review` permission to any Admin. There's no separate "Reviewer" account type — reviewing is one of the permissions an Admin can hold. |
| **System-level audit** | Can see the access log (who viewed/downloaded/deleted a recording, and when) across the whole platform. |

### Super Admin vs. Admin — the actual difference
An Admin can only ever see and manage the organizations *assigned* to them (`tenants.managed_by`). A Super Admin sees everything, can create/deactivate other Admins, and is the only one who can change what an Admin's permissions are. An Admin cannot grant themselves — or anyone — a new permission.

---

## 2. Internal Admin Login
Your staff — Sales, Delivery, L&D. "Department" is a label on the account; **what an Admin can actually do is controlled by their `permissions`**, granted individually by a Super Admin.

### Admin Dashboard
Their assigned organizations, a quick view of each one's learner count and activity, and pending invitations not yet accepted.

### What an Admin can do (each gated by a permission)
| | |
|---|---|
| **Manage organizations** | Create, view, edit, and activate/deactivate the cohort tenants assigned to them. Company and university are the same kind of record with a different label (`category`) — not two different systems. |
| **Create org + assign Org Admin** | When creating an organization, also enters the contact person's email who becomes that organization's Org Admin. |
| **Invite individual learners** | Can manually send a direct signup invite to someone joining as an individual (non-organization) learner — for cases handled outside normal public self-signup. |
| **Bulk upload learners (CSV)** | Uploads a spreadsheet of learner emails for one organization. The system creates each learner's account directly and emails each one a "set your password" link — the same mechanism used to invite an Admin, just run once per row instead of once. |
| **View/manage learners** | Only learners belonging to organizations assigned to this Admin. |
| **Content / score review** | Only if the `content_review` / `score_review` permission has been granted to them. |
| **View reports** | Only for their assigned organizations. |

### Example permission presets
These are examples of how a Super Admin might configure different departments — not hardcoded roles. Any combination is possible; these are just realistic starting points.

| Permission | Sales (example) | Delivery (example) | L&D (example) |
|---|---|---|---|
| Manage organizations | Yes | Yes | No |
| Invite / bulk-upload learners | Yes | Yes | No |
| View learner information | No | Yes | Yes |
| Content / score review | No | No | Yes |
| View reports | Yes | Yes | Yes |

---

## 3. Organization Admin Login `Phase 2 — planned, not built`
A real contact *at* the client company or university. Scoped to their own organization only.

| | |
|---|---|
| **Organization Dashboard** | Their organization's stats only — never platform-wide data. |
| **View learners** | Everyone in their organization. |
| **Add / invite learners** | One at a time, or... |
| **Bulk upload (CSV)** | ...the same spreadsheet-upload mechanism an Admin uses, scoped to their own organization. |
| **Manage batches** | Create/name sub-groups inside their own organization — e.g. "2026 Intake" — and assign learners to one. (This is the `batches` table.) |
| **Aggregate activity & reports** | Practice frequency, completion rates, readiness distribution — across their organization or one batch. |
| **Organization settings** | Their org's own display name, contact details. |

> **Hard rule, not a preference:** an Org Admin never sees an individual learner's personal streaks, levels, or badges — those stay private to the learner, even from their own employer or university. They see readiness and progress (the information relevant to a hiring or academic decision), never the gamification layer.

---

## 4. Learner Login
One login for everyone using the product — a self-paying individual, a company's employee, or a university's student. After login, every learner sees the same modules:

| Module | What they can do there |
|---|---|
| **Dashboard** | Current CEFR level, target level, roadmap progress, streak, days until their exam, recommended next practice. |
| **Diagnostic** | The placement test — taken once, up front, before normal practice; produces their starting CEFR level. |
| **Practice** | Untimed-pressure, learner-chosen practice across the 3 core exercise types, drawn from their weak areas. |
| **Mock Test / Exam Mode** | A full, silently-timed session matching real exam conditions — no replay, no going back. This is the only place a real score comes from. |
| **Coach Mode** | Conversational voice practice with spoken corrections, sourced only from real scoring. Never produces a score itself. |
| **Progress** | Skill-by-skill breakdown (speaking, listening, writing) over time. |
| **History** | Every past session and attempt, with its result. |
| **Readiness** | Their current readiness band, and — if their placement already matched their goal — the "you may already be ready" message. |
| **Achievements** | Streaks and badges. Private to them always, never visible to an Org Admin. |
| **Profile** | Name, exam goal, target level, exam date, daily practice preference — editable. |
| **Settings** | Consent history, notification preferences, delete account, export my data. |

---

## 5. B2C Signup Flow
```
Public signup
   → account created
   → individual_free tenant assigned automatically
   → email verification
   → consent (before any recording is ever possible)
   → microphone / headset check
   → learner dashboard
```
**Free vs. paid:** both plans get all 3 exercise types and both modes — the free plan is never missing a feature. What differs is volume: `individual_free` has a daily cap on scored attempts and Coach Mode minutes; `individual_paid` raises or removes that cap. A learner is never told "upgrade to unlock X" — only "you've used today's free practice, come back tomorrow or upgrade."

---

## 6. B2B Organization Onboarding Flow
```
Super Admin or Admin
   → Create Company / University
   → organization created as a cohort tenant (category: company / university)
   → assign that organization's Org Admin
   → invite learners (one at a time, or bulk CSV upload)
   → learner accepts invitation
   → learner sets their own password
   → consent
   → learner dashboard
```
Company and university are **organization types** — a label (`category`) on one kind of tenant (`cohort`) — never two different authentication systems. A university's learner and a company's learner sign in exactly the same way an individual learner does.

---

## 7. Role & Permission Matrix

| Action | Super Admin | Admin | Org Admin | Learner |
|---|---|---|---|---|
| Manage admins | Yes | No | No | No |
| Manage organizations | Yes, all | Configurable, assigned only | Own org settings only | No |
| Manage learners | Yes, all | Configurable, assigned orgs | Own org only | No |
| Invite learners | Yes | Configurable | Own org only | No |
| Bulk upload learners | Yes | Configurable | Own org only | No |
| View learner information | Yes, all | Configurable, assigned orgs | Own org, readiness only — never gamification | Own data only |
| Content review | Yes | Configurable | No | No |
| Score review | Yes | Configurable | No | Can flag own score for review |
| View reports | Yes, platform-wide | Configurable, assigned orgs | Own org only | Own progress only |
| Practice | — | — | — | Yes |
| Take mock tests | — | — | — | Yes |
| Coach Mode | — | — | — | Yes |
| View progress / readiness | Yes, all | Configurable, assigned orgs | Own org's learners | Own only |
| Manage platform settings | Yes | Configurable, rare | No | No |

*Configurable = gated by that Admin's `permissions`, set individually by a Super Admin. — = not applicable to that role.*

---

## 8. Login & Navigation Flow

**Super Admin login**
```
Login screen → credentials checked → role = super_admin
   → Super Admin Console (platform stats, admin list, all organizations)
```

**Internal Admin login**
```
Login screen → credentials checked → role = admin
   → Admin Console (only their assigned organizations, gated by their permissions)
```

**Organization Admin login**
```
Login screen → credentials checked → role = org_admin
   → Organization Dashboard (their one organization only)
```

**B2C learner signup / login**
```
Public signup → individual_free tenant → verify email → consent → mic check
   → Login screen (from now on) → role = learner, no organization
   → Learner Dashboard
```

**B2B learner invitation / login**
```
Receives invite (shared link, or direct email from bulk upload)
   → signup screen, organization attached automatically → set password → consent
   → Login screen (from now on) → role = learner, tenant = their organization
   → Learner Dashboard
```

---

## 9. Recommended Account Structure
**User → Role → Tenant/Organization → Batch (if any) → Permissions (if Admin).** Every account is a `users` row; what it can do is entirely a function of those fields, not a different table per role.

| Example | role | tenant_id → | batch_id | permissions |
|---|---|---|---|---|
| Individual learner (free) | learner | tenant, `type='individual_free'` | — | — |
| Individual learner (paid) | learner | tenant, `type='individual_paid'` | — | — |
| Company learner | learner | tenant, `type='cohort'`, `category='company'` | optional | — |
| University learner | learner | tenant, `type='cohort'`, `category='university'` | optional | — |
| Org Admin | org_admin | same tenant as their organization's learners | — | — |
| Internal Admin | admin | none (NULL) | — | their granted permissions |
| Super Admin | super_admin | none (NULL) | — | all, implicitly |

---

## Connects to
- [[Versant Tool — Phases by Module]] — which build phase this spec belongs to
- [[BRD — Versant Prep Tool]] / [[FRD — Versant Prep Tool]] — the underlying locked requirements
