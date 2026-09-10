---
title: Translation — Complete Guide (Costs & Methods)
tags: [discussion, frontend, localization, tooling, cost]
status: open
date: 2026-09-01
related: ["[[Translation Automation — Methods & Recommendation]]", "[[Localization — The Non-English B2C Learner Case]]"]
---

# Translation — Complete Guide (Costs & Methods)

> [[00 - Index|← Back to Index]]

Everything covered on this topic, in one place, in plain language. Covers: what gets translated, how `constants` and `locales` would coexist, how the automation actually works, every method discussed, what each one costs, which one to actually pick, and how other companies actually do this in practice. Companion to [[Translation Automation — Methods & Recommendation]] (the shorter, decision-focused version) and [[Localization — The Non-English B2C Learner Case]] (the scenario this tooling would serve). Same status: open discussion, nothing decided or scheduled.

---

## 1. What actually gets translated

Only the **words around the test** — buttons, menus, instructions, the consent screen. Things like "Start Practice Session," "Submit," "Log out."

**The actual test questions and audio never get translated — ever, no matter what.** This isn't a "not yet" thing — it's permanent. The test is checking how well someone speaks and understands English. If you translated the questions into Hindi, you'd be testing something completely different (whether they understand Hindi), which defeats the whole purpose. So no matter which method below you use, you only ever point it at your UI text, never at your exam content.

---

## 2. How your text needs to be organized first

Before any automated translation makes sense, your app's text needs to be written in a way that *can* be swapped out. Right now, if your button says:

```jsx
<button>Start Practice Session</button>
```

...that English text is glued directly into the button. To support other languages, you change it to:

```jsx
<button>{t('buttons.startSession')}</button>
```

Now the button just asks "give me whatever `buttons.startSession` says" — and a small helper (a library called `react-i18next`) looks that up in whichever language file is currently active. You keep one file per language:

```
src/locales/
  en/translation.json   ("buttons.startSession": "Start Practice Session")
  hi/translation.json   ("buttons.startSession": "अभ्यास सत्र शुरू करें")
  es/translation.json   ("buttons.startSession": "Comenzar sesión de práctica")
```

The automated methods below are all about **filling in those language files automatically**, instead of you typing each translation by hand.

---

## 3. How the `constants` folder still fits in, alongside `locales`

Earlier in this project, a `constants` folder was set up to hold UI text — before `locales` came into the picture. These two aren't competing with each other; once translating into multiple languages, they end up doing two different jobs, side by side.

**The split that actually makes sense:**

| Folder | Holds | Example |
|---|---|---|
| `constants` | Values your *code* checks or branches on — things that have logic attached, not just display text | Task type names (`'repeats'`, `'dictation'`), timing numbers (15 seconds, 6 seconds), status values (`'pending'`, `'scored'`) |
| `locales` | Text a *human reads* — pure display strings, translated per language | "Start Practice Session," "Submit," consent screen wording |

```js
// src/constants/taskTypes.js — stays exactly as it was, untouched by translation
export const TASK_TYPES = {
  REPEATS: 'repeats',
  SENTENCE_BUILDS: 'sentence_builds',
  DICTATION: 'dictation',
};

// src/constants/timing.js — also untouched by translation
export const RESPONSE_WINDOWS = {
  repeats: { windowSeconds: 15, onsetDeadlineSeconds: 6 },
  sentence_builds: { windowSeconds: 15, onsetDeadlineSeconds: 8 },
  dictation: { windowSeconds: 25 },
};
```

```json
// src/locales/en/translation.json — this is what gets translated
{
  "buttons": { "startSession": "Start Practice Session" },
  "taskLabels": {
    "repeats": "Repeat the sentence",
    "sentence_builds": "Build the sentence",
    "dictation": "Type what you hear"
  }
}
```

**Why they need to stay separate, not merged into one folder:** `TASK_TYPES.REPEATS` (`'repeats'`) is a value the code actually compares against — e.g. `if (taskType === TASK_TYPES.REPEATS)`. If that value accidentally got run through a translation script, it might come back as `'répétitions'` in French, and every place the code checks `=== 'repeats'` would silently break, since the stored value no longer matches. Keeping constants untouched by the translation process is what prevents this — only the human-facing *label* for a task type (`taskLabels.repeats`, "Repeat the sentence") goes into `locales` and gets translated; the underlying identifier (`'repeats'`) never does.

**In practice, in a component, both get used together:**

```jsx
import { TASK_TYPES } from '../constants/taskTypes';
import { useTranslation } from 'react-i18next';

function TaskLabel({ taskType }) {
  const { t } = useTranslation();

  if (taskType === TASK_TYPES.REPEATS) {
    // logic branch uses the constant — never translated, always reliable
  }

  return <span>{t(`taskLabels.${taskType}`)}</span>; // display text — comes from locales, gets translated
}
```

The constant drives the logic. The `locales` lookup drives what the user actually sees. Neither replaces the other — the same distinction as smart vs. dumb components, in a sense: constants are the "backend truth," locale strings are the "display layer" on top of it.

> **Note on current state:** whether the `locales`/`t()` pattern this section describes has actually been built into the real codebase yet is unconfirmed — the Frontend Architecture note's `status: resolved` decision documents the current approach as plain JS objects only (`UI_TEXT.buttons.startSession`), with `t()`/`locales` named as a future migration path, not something already in place. This section is a real clarification of how the two folders *would* divide responsibility once that migration happens — it doesn't by itself confirm the migration has happened. See [[Translation Automation — Methods & Recommendation]] for the full discrepancy note.

---

## 4. The two main automation methods

### Method 1 — A script that translates everything for you, automatically

You write **one** file, in English. A small program reads it, sends every phrase to a translation service, and writes out a matching file for every language you want — all in one go.

**How it actually works, step by step:**
1. The script opens your English file and reads through it.
2. For each phrase (like "Start Practice Session"), it sends that phrase to a translation service and asks for it back in, say, Hindi.
3. It saves all those translated phrases into a new Hindi file, keeping the same structure as the English one.
4. It repeats this for every language you listed.
5. When it's done, you have a complete `hi/translation.json`, `es/translation.json`, etc. — without typing a single translation yourself.

**What the actual code looks like** (using Google's translation service as an example):

```js
// scripts/generate-translations.js
const fs = require('fs');
const { Translate } = require('@google-cloud/translate').v2;

const translate = new Translate(); // this connects to Google's translation service using your API key

const SOURCE_FILE = './src/locales/en/translation.json';        // your one English file
const TARGET_LANGUAGES = ['hi', 'es', 'fr', 'ta', 'te', 'bn'];   // the languages you want

// This function goes through every phrase in your file and translates it
async function translateObject(obj, targetLang) {
  const result = {};
  for (const key in obj) {
    if (typeof obj[key] === 'object') {
      result[key] = await translateObject(obj[key], targetLang); // handles nested groups like "buttons": {...}
    } else {
      const [translatedText] = await translate.translate(obj[key], targetLang);
      result[key] = translatedText;
    }
  }
  return result;
}

// This runs the whole process, one language at a time
async function run() {
  const sourceText = JSON.parse(fs.readFileSync(SOURCE_FILE, 'utf8'));
  for (const lang of TARGET_LANGUAGES) {
    console.log(`Translating to ${lang}...`);
    const translated = await translateObject(sourceText, lang);
    fs.mkdirSync(`./src/locales/${lang}`, { recursive: true });
    fs.writeFileSync(`./src/locales/${lang}/translation.json`, JSON.stringify(translated, null, 2));
  }
  console.log('All done!');
}

run();
```

You run this once by typing `node scripts/generate-translations.js` in your terminal, and it does everything automatically. If you add new English text later, you just run it again.

**Good for:** doing this once, or occasionally, without needing a whole team/process around it.
**Downside:** if you run it again, it re-translates everything from scratch — so if a human had manually fixed a translation, running the script again could overwrite that fix. There's no built-in way to track "what's new" vs. "what's already been checked."

---

### Method 2 — A translation platform that manages everything for you

Instead of a script you run yourself, this is a website/service (examples: **Lokalise, Crowdin, Phrase, Locize**) that connects to your English file and does the translating *and* the tracking *and* gives a person a place to review each translation before it goes live.

**How it actually works:**
1. You connect your English file to the platform once.
2. Whenever you add or change English text, the platform notices automatically.
3. It auto-translates just the new/changed bits (not everything all over again).
4. A real person can open the platform, see the English text next to the machine's translation, and approve it or fix it.
5. Once approved, the platform sends the finished translation files back into your project automatically.

**Good for:** an app that keeps growing and changing over time, especially with more than one person working on it, since it keeps track of what's been reviewed and what hasn't.
**Downside:** costs a monthly subscription on top of the actual translation cost, and takes more setup than a script.

---

## 5. Other methods that exist (shorter explanations)

| Method | What it is |
|---|---|
| **DeepL** | Same idea as Method 1, but a different translation service instead of Google's. Often considered better quality for European languages (German, French, Spanish), but supports fewer total languages than Google. |
| **AI/LLM translation** (like Claude or ChatGPT's API) | Instead of a dedicated translation service, you ask an AI model to translate, and you can give it extra instructions like "keep this short, it's a button" or "this is formal, it's a legal notice." Often sounds more natural, but costs more per phrase and needs more careful setup to get consistent results. |
| **Amazon Translate / Microsoft Azure Translator** | Same category as Google/DeepL — cloud company translation services. Only worth it if you're already using Amazon (AWS) or Microsoft (Azure) for other parts of your app, which you currently aren't (FRD §7 — Cloudflare R2 + EC2). |
| **Crowdsourced/human translation** | Real people translate by hand, sometimes for free (volunteers) or paid (hired translators). Best when accuracy really matters, like your consent/legal screens. |
| **Self-hosted, free translation software** (LibreTranslate, Argos Translate) | Explained in detail below — this is the genuinely free option. |

---

## 6. Pricing — what everything actually costs

### Paid translation services (charged by amount of text, "per character")

| Service | Free amount each month | Cost after that |
|---|---|---|
| Google Cloud Translation | ~500,000 characters free | ~$20 per 1,000,000 characters |
| DeepL | ~500,000 characters free | ~€20-25 per 1,000,000 characters |
| Amazon Translate | ~2,000,000 characters free (first 12 months only) | ~$15 per 1,000,000 characters |
| Azure Translator | ~2,000,000 characters free | ~$10 per 1,000,000 characters (cheapest of the four) |

**To put "1,000,000 characters" in perspective:** that's roughly 150,000-200,000 words — a genuinely large amount of writing, like a long book. If you're translating a smaller amount (like just your app's buttons/menus/instructions), you'll likely stay inside the free monthly amount and pay nothing. If you have a *lot* of content — articles, long guides, big documents — you could go past the free amount and start owing money, and the more languages you translate into, the faster that adds up (since each language is a separate full translation).

### Translation platforms (charged as a monthly subscription)

| Platform | Free version? |
|---|---|
| Crowdin | Yes, a real free plan exists (limited amount of text/projects) |
| Locize | Yes, a real free plan exists (limited amount of text) |
| Lokalise | No permanent free plan — only a free trial (about 1-2 weeks), then you must pay |
| Phrase | No permanent free plan — only a free trial, then you must pay |

**Important:** even on these platforms, the actual translating is still done by Google or DeepL underneath — so you might be paying the platform's subscription *and* still paying (or using free allowance) for the translation itself, depending on the platform's plan.

### The genuinely free option — no cost no matter how much content you have

This works completely differently from everything above. Instead of *paying a company* to translate your text, you run free, open-source translation software **on your own computer/server**. Since nothing is being sent to a paid company, there's no per-character bill — ever, regardless of volume.

- **LibreTranslate** — free, open-source translation software you can run yourself.
- **Argos Translate** — similar idea, runs completely offline, no internet connection needed at all.

**The script barely changes** — same structure as Method 1, just pointed at your own free software instead of Google's paid service:

```js
// Instead of calling Google's paid service...
const [translatedText] = await translate.translate(text, targetLang);

// ...you call your own free LibreTranslate server instead
const response = await fetch('http://localhost:5000/translate', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ q: text, source: 'en', target: targetLang }),
});
const data = await response.json();
const translatedText = data.translatedText;
```

**The real tradeoff, honestly:**
- **Cost:** genuinely $0, no matter how much content you translate.
- **Quality:** usually a bit lower than Google or DeepL — good enough for most everyday text, but worth double-checking on anything important.
- **Setup:** you need a server to actually run this software on, and it works faster with a more powerful server (ideally one with a graphics card/GPU) — your current server setup wasn't built with this in mind, so this might mean temporarily renting a bigger server just for the translation work, then going back to your normal setup afterward.

---

## 7. Which one should you actually use?

**If your content is small** (just your app's UI text — buttons, menus, a few screens' worth of instructions): use **Method 1 with Google Translate**. You'll very likely stay within the free monthly amount and pay nothing at all.

**If your content is large** (you said you have "a lot of content"): this changes things. A few honest options, depending on what matters most to you:

1. **Cost matters most → use the free self-hosted option (LibreTranslate).** No matter how much content you have, it costs nothing beyond the one-time effort of setting up a server to run it. Best choice if you want a real answer to "is this free," even at high volume.
2. **Quality matters most → use a paid service, and pick Azure Translator specifically**, since it's the cheapest per-character among the paid options while still being solid quality.
3. **A mix (recommended) → split by importance.** Use the free option (LibreTranslate) for your bulk, lower-stakes content, but use a paid service (Google, DeepL, or even an AI model with careful instructions) specifically for your consent screen and any legal-sounding text — since a bad translation there matters much more than a slightly awkward button label. This is the same conclusion [[Localization — The Non-English B2C Learner Case]] §5 reaches from the compliance side: consent is the one place worth spending real money or a human reviewer, even if everything else uses the free path.

**One thing to actually check before deciding:** how much content, in actual word or character count, are you translating? That number changes everything above from "this will likely be free" to "this could genuinely cost real money" — worth measuring before picking a method, since right now the advice is based on general ranges, not your exact numbers.

---

## 8. How other companies and websites actually do this

Useful to know you're not doing something unusual — here's the real industry picture.

### Two fundamentally different approaches websites use

**Approach A — Pre-translated, stored content (what this guide has been building toward).** The website has its text already translated and saved *before* anyone visits — sitting in files or a database, ready to serve instantly. When someone requests the Spanish version, the server just pulls up the already-translated Spanish text and sends it. Nothing gets translated live, in the moment.

This is what nearly every serious multi-language website or app does — Airbnb, Amazon, Duolingo's own interface, banking apps, government sites. The translation work happens **ahead of time**, by a team or a process, and gets reviewed before it ever reaches a real user. This is exactly the `locales/en/translation.json`, `locales/hi/translation.json` pattern from §2 — you're doing what real companies do, just at your own scale.

**Approach B — Translated live, on the spot.** This is what the "Translate this page" button in your browser does. Nothing is pre-translated. The page loads in its original language, and only if the user clicks "Translate" does a service (usually Google Translate, running in the browser) grab the page's text and translate it live, right there, replacing the text on screen.

This is different, and worse for a real product: the website itself has no control over quality, nothing was ever reviewed, and it often produces slightly broken or awkward results (you've probably seen this yourself — a translated webpage that reads a little "off"). Serious companies don't rely on this for their *own* interface; it's more of a fallback for websites that never bothered to translate themselves, letting the browser do a rough, uncontrolled job instead.

### How big companies actually run Approach A, in practice

This maps almost exactly onto what this guide has covered — not doing something different from the industry, just at smaller scale:

1. **Developers write text as lookup keys** in the code (`t('buttons.startSession')`), never hardcoded strings — universal practice across essentially every multi-language app.
2. **The English version gets written first**, treated as the "source of truth."
3. **Translation happens through some mix of:** professional human translators (for important, high-visibility text — homepages, legal text, key flows), machine translation as a fast first draft that a human then reviews and fixes (increasingly the default, since machine translation has gotten good enough to use as a starting point), or a translation management platform (the Crowdin/Lokalise/Phrase category from §5) coordinating all of it — detecting new text, sending it out, tracking what's approved.
4. **The finished, reviewed translations get bundled into the app** as static files (same shape as the `locales` folder) — this is what actually ships to users.
5. **Nothing is translated at the moment a user visits.** It was all done in advance.

### Where AI/LLMs are changing this recently

Worth flagging this part is more current and less settled. There's been a real, industry-wide shift toward using large language models as the *first-pass* translator instead of older-style translation engines, specifically because LLMs are noticeably better at handling context — tone, idioms, keeping technical terms untranslated, matching the right level of formality (exactly the consent-screen concern from §7). Many translation platforms have already integrated LLM-based translation as an option alongside or instead of traditional engines. The human review step generally still happens either way, especially for anything consequential — it's the *first draft* that's shifted, not the whole process.

### Is any of this actually helpful for this project?

Yes, in one specific way: it confirms the approach in this guide isn't a workaround or a shortcut — it's the same shape every serious company uses (write text as lookup keys → translate ahead of time → review before shipping → serve pre-translated files instantly). The only real decision left is *how* the translating gets done in step 3, which is the Method 1 vs. Method 2 vs. self-hosted choice already covered in §§4-7 — and that genuinely does vary company to company based on budget, content volume, and how much ongoing translation work they expect. Nothing here changes that decision; it just confirms this isn't missing some other, better-known way of doing it.

---

## 9. One thing that stays true no matter which method you pick

Only ever point any of these tools — script, platform, AI, self-hosted, whatever — at your **UI text**. Never at your item bank / exam questions / exam audio. That rule doesn't change based on cost, quality, or which method you use — it's a permanent boundary, not a budget decision.

## Connects to
- [[Translation Automation — Methods & Recommendation]] — the shorter, decision-focused version of this same material
- [[Localization — The Non-English B2C Learner Case]] — the scenario and requirements this tooling would serve
