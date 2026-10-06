Spica · identity verification

# Face Check & Passkey, Explained Simply

What the camera actually checks, what happens when it fails, and exactly where this still can and can't stop someone from cheating.

On this page

1. How face check works
2. Match, uncertain, or mismatch
3. The passkey backup
4. The "sticker" — one device only
5. Registering from a phone instead
6. One login at a time (proposed)
7. What this can't solve
8. Built vs. not built

## 01. How face check works

Three small, free models, each doing one narrow job. No single model tries to do everything.

YuNet Step 1 · find the face

Looks at the picture and draws a box around any face in it. Doesn't know who it is yet — just "there's a face here."

MiniFASNet Step 2 · is it a real, live face

Checks texture and lighting to tell a real face apart from a photo or screen held up to the camera.

AuraFace Step 3 · whose face is it

Turns the face into 512 numbers describing its geometry, then compares those numbers to the ones saved at enrollment.

All three run on the server, free, with no per-check cost and nothing sent to an outside company. Each step has to pass before the next one runs — a photo held up to the camera never even reaches the identity check, because step 2 already caught it.

## 02. Match, uncertain, or mismatch

AuraFace doesn't say yes or no — it gives a similarity score, 0 to 1. Three ranges decide what happens.

**Match** — score ≥ 0.6 — let in

**Uncertain** — 0.45–0.6 — blocked, counts as a try

**Mismatch** — below 0.45 — blocked, counts as a try

Only a clear match lets someone through automatically. "Uncertain" never quietly passes — a close-but-not-quite score is treated the same as a clear mismatch, so a lookalike can't slip in on a borderline score.

Two kinds of failure are tracked separately, with different limits: a real mismatch (wrong person) allows **2** tries before offering the backup option; a camera/lighting problem (no face found, too bright, too many faces) allows **3**, since that's usually not the learner's fault.

## 03. The passkey backup

When face check genuinely can't confirm someone, this is the one thing that can — not an email code, not a text message.

A passkey is a pair of keys your device creates together: a **private key** that never leaves the device, and a **public key** that gets sent to Spica's server. Nothing secret ever travels over the network — not at setup, not at use.

Server sends a random, one-time puzzle → Device asks for fingerprint / Face ID / PIN → Device signs the puzzle with its private key → Server checks the signature matches

Why this beats an OTP: an OTP is just a number — easy to read aloud or forward to someone else, proving only that someone has the code, never that they're the right person. A passkey's private key physically can't be copied out and handed to anyone, so there's nothing to forward in the first place.

### Why it's necessary at all

If face check fails for a genuine learner — bad lighting, an old webcam, a lookalike sibling — they still need a way in. Admin approval was the original plan, but a human can't review a camera-failure case faster than it happens, and doesn't scale past a handful of learners. Passkey gives an instant, self-serve way back in without lowering the bar.

## 04. What I built: one-device login for the passkey

A passkey saved to a Google or Apple account can technically work from any device signed into that account. This closes that gap — the passkey only works on the one computer/browser it was set up on.

### Where it actually comes from

Entirely our own code — nothing to do with Google, Apple, or the passkey standard itself. First time anything passkey-related runs in a browser, this checks for a saved tag and creates one if missing:

```
function getDeviceTag() {
  let tag = localStorage.getItem("spica_device_tag");
  if (!tag) {
    tag = crypto.randomUUID();
    localStorage.setItem("spica_device_tag", tag);
  }
  return tag;
}
```

Lives in `frontend/src/lib/deviceTag.ts`. `crypto.randomUUID()` is a random-ID generator built into every browser — no library, no external call. Once created, the same tag is reused forever on that browser, sent along with every passkey register/verify call.

### How it works

1. First "Set up passkey" click writes a random code onto the browser — a sticker, stored only there, never uploaded, never synced → 2. That same code is saved on the server too, linked to the passkey → 3. Clicking "Verify with Passkey" later sends back *two* things: the real passkey proof, and the sticker → 4. Server checks both — proof valid but sticker missing or wrong → rejected

### What this means in practice

| Scenario | Result |
| --- | --- |
| Same browser, logged out and back in, days later | Sticker still there — works, button shows up |
| Different browser on the *same* computer | No sticker there — treated as a new device, button doesn't show |
| Someone else's computer, same Google account | No sticker — passkey rejected even with real account access |
| Browser data cleared, or a private window | Sticker gone — needs setup again on that browser |

Face check itself — the camera part — is untouched by any of this. It still works from any device, anytime, no sticker involved. This only affects the backup option used when face check fails.

## 05. A stronger option: register from a phone

On a computer with no fingerprint reader, Google Password Manager is the only option available — weaker than true device binding. A phone does better.

Opening Spica directly in the phone's *own* browser — not scanning a QR code from a laptop, that's a different, weaker path — lets the phone offer its real fingerprint or Face ID as the passkey. That's genuine device-bound security: the key lives in the phone's own secure hardware, nothing synced, nothing shareable.

### One thing needed to actually test this

The dev servers currently only answer to `localhost` — a phone on the same Wi-Fi can't reach them yet. Opening that up (LAN access) is a small, safe change: it only lets devices already on the same network in, nothing from the public internet. In a real deployment on a real domain, this isn't a concern at all — any phone just works.

## 06. One login at a time — built

A different problem: two people could be logged into the same account from two places at once. Now blocked.

Logging in now checks whether the account is already active somewhere else. If a session is still open elsewhere, the new login is rejected with **"Already logged in on another device — log out there first."** No second device can get in while the first stays open.

### What this does and doesn't do

It stops *concurrent* sharing — two people in at the same moment. It does **not** check who is logging in: if someone else has the password, they can still log out the first device and log in as "them." It also doesn't stop a sequential hand-off — log in, hand the device over, someone else continues. Face check still carries the actual identity check; this only closes the simultaneous-access gap.

## 07. What this can't solve

Worth being honest about, since no technical system — including paid commercial proctoring tools — solves these either.

### A genuine learner willingly hands over their password and unlocked phone

If the real learner actively cooperates — gives someone their password and their already-unlocked device — the system can't tell that apart from the learner using it themselves. The phone correctly belongs to them, so it correctly approves. The one thing that still catches this: the friend still has to pass the live face check, since that runs separately from the device.

### The wrong person enrolls in the first place

If an impostor is the one who originally signs up and enrolls their own face, the system faithfully protects *their* identity from then on — it has no way to know that's the wrong person. Fixing this needs a check outside the app entirely, like verifying against an ID or an institution's roster at signup.

Neither of these is a gap in this build specifically — they're the same two limits every remote identity-verification system has, technical or not.

## 08. Built vs. not built

A quick status check, so nothing here is assumed finished that isn't.

| Idea | Status | Note |
| --- | --- | --- |
| Face check (detect → liveness → match) | Built | Live, in use on placement & practice |
| Passkey fallback, one device only | Built | Needs a device with a real lock screen or a security key |
| Admin manual approval | Removed | Didn't scale to real volume; passkey replaced it |
| Single active login (block second device) | Built | Stops concurrent sharing, not impersonation |
| Repeat face check mid-test | Not built | Would raise the cost of a willing hand-off |
| ID-verified enrollment | Not built | Only real fix for a fake signup from day one |

Internal reference · Spica face verification & passkey fallback
