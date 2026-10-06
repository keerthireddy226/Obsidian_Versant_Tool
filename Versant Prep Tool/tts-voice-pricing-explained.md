Spica · spoken-prompt audio

# Voice & Accent Pricing, Explained

What's already built, what it costs, and the real numbers for this project's actual content.

On this page

1. What's already built
2. How the pricing actually works
3. How many voices and accents exist
4. The real numbers for this project
5. What happens if you add more voices
6. One thing it can't do

## 01. What's already built

Not a research idea — this is live in the app today.

`backend/src/voice/googleTts.ts` calls **Google Cloud Text-to-Speech**, using two Neural2 voices: `en-US-Neural2-D` (male) and `en-US-Neural2-F` (female). A learner picks one in their profile, and every spoken item in Modules/Placement gets generated in that voice ahead of time, not live per request.

### Generated once, reused forever

Audio is synthesized once per item per voice and saved as an mp3 (`scripts/generateItemAudio.ts`). It's only re-run when new content is added or existing text changes — never regenerated per student, per play, or per attempt. This is the single most important fact for the cost math below.

Licensing: this is Google's paid cloud API, not an open-source model — no license to worry about, just Google's standard Cloud Terms of Service, same as Gemini.

## 02. How the pricing actually works

Not a subscription. Not a one-time free month. A monthly, resetting, shared allowance.

1,000,000. free characters, every month

$16. per extra 1M characters

1. shared pool for the whole project

Three things worth being precise about, since each one is easy to get wrong:

### It resets every month, not just the first

The 1,000,000-character allowance is free *every single month*, forever — not a one-time trial. Stay under it in month 20 and month 20 costs $0, same as month 1.

### It's one shared pool, not per-voice

There's a single 1,000,000/month allowance for the whole Google Cloud project. Using 2 voices or 16 voices doesn't change the size of that pool — more voices just mean the same text gets synthesized more times, which uses up the one shared pool faster.

### It only counts characters actually sent to the API

Since audio is generated once and cached (see above), this cost is really a one-time content-library charge, not something that scales with how many students use the app or how many times they play a clip.

### $16 is a rate, not a flat fee

It's the price per 1,000,000 characters — not a charge that hits all at once. Going over by 10,000 characters bills exactly **10,000 × $0.000016 = $0.16**, not $16. The full $16 only applies if usage goes a full extra million characters past the free limit.

## 03. How many voices and accents exist

Far more available than what's currently in use.

380+. voices total

75+. languages & variants

4. quality tiers

2. voices currently used

English accents available: American, British, Australian, Indian — each with multiple male/female voices. Four quality tiers exist: Standard (cheapest), WaveNet (older neural), **Neural2** (what's used now — natural, mid-priced), and Studio (premium, broadcast-grade, priced higher). Neural2 is genuinely enough for instructional/practice audio — Studio is aimed at professional media production, not needed here.

Adding more accents or voices to the dropdown is free to do — the character-based price is identical no matter which voice or accent is selected.

## 04. The real numbers for this project

Pulled straight from the database, not a guess.

355. approved spoken items today

~28,000. characters of actual spoken text

| Setup | Total characters (one-time) | Cost |
| --- | --- | --- |
| Current: 2 voices (male + female) | ~56,000 | Free |
| 4 accents × 4 voices (16 variants) | ~450,000 | Free |
| 10 total voice/accent combinations | ~280,000 | Free |
| 10 accents × 10 voices (100 variants) | ~2,800,000 | ~$28.80 |

Every row above is a one-time charge for generating the library, in whichever month it's run — not a recurring monthly bill.

## 05. What happens if you add more voices

Two separate levers, easy to mix up.

### More voices/accents for the same content

Multiplies the one-time generation cost by however many variants you add. Realistic counts (2–16 variants) stay free even at current content size.

### More content (new items added later)

Only the new/changed items get regenerated (`--force` flag re-runs existing ones on a text edit) — existing cached audio is untouched. Growing the content library 2–3x is roughly where a real library would start brushing past the free 1M/month pool, and even then it's a few dollars, once.

## 06. One thing it can't do

No age dimension in the voice catalog.

Google's voices are labeled only by language, accent, and gender — there's no "younger" or "older" voice selector. Some voice IDs happen to sound a bit younger or older by ear, but it's not a documented, filterable attribute. If distinct age-variation voices actually matter, that needs a different provider (e.g. ElevenLabs) with its own separate pricing — not something Google Cloud TTS offers today.

Internal reference · Spica spoken-prompt audio pricing
