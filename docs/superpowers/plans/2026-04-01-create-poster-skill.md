# Create Poster Skill Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build a self-contained Augment skill that guides any user through a structured intake and generates a branded Wanek IT News HTML poster, ready to screenshot and embed in email.

**Architecture:** Two files only — `SKILL.md` (all AI instructions, intake rules, constraints) and `poster-light.html` (HTML template with `{{TOKEN}}` placeholders). No scripts, no tooling dependencies. The AI is the only engine.

**Tech Stack:** Plain HTML + CSS, Google Fonts (CDN), base64-embedded logo, Augment skill system (SKILL.md).

**Spec:** `docs/superpowers/specs/2026-04-01-wanek-it-news-design.md`

---

## Task 1: Scaffold Repo Structure

**Files:**
- Create: `output/.gitkeep`
- Create: `.gitignore`
- Create: `.augment/skills/create-poster/templates/` (directory)

- [ ] **Step 1: Create output directory**

All paths are relative to the repo root. The skill must live inside `.augment/skills/create-poster/` — not at the repo root.

```bash
mkdir -p output
touch output/.gitkeep
mkdir -p .augment/skills/create-poster/templates
```

Verify the directory was created at the right location:
```bash
ls .augment/skills/create-poster/
```
Expected: `templates/`

- [ ] **Step 2: Create .gitignore**

Create `.gitignore` with this exact content:

```
.superpowers/
```

- [ ] **Step 3: Verify structure**

```bash
find . -not -path './.git/*' -not -path './.augment/skills/ui-ux-pro-max/*' | sort
```

Expected output includes:
```
./.augment/skills/create-poster/templates/
./assets/base_poster.html
./assets/logo.jpg
./output/.gitkeep
./.gitignore
```

- [ ] **Step 4: Commit**

```bash
git add output/.gitkeep .gitignore .augment/skills/create-poster/
git commit -m "chore: scaffold create-poster skill directory structure"
```

---

## Task 2: Build `poster-light.html` Template

**Files:**
- Create: `.augment/skills/create-poster/templates/poster-light.html`
- Read: `assets/base_poster.html` (extract the base64 logo string from the `src` attribute of the `<img>` tag inside `.logo-wrap`)

**Critical notes before starting:**
- The logo base64 string is already in `assets/base_poster.html` inside `<img src="data:image/jpeg;base64,..." alt="Wanek">`. Copy the entire `data:image/jpeg;base64,...` value — do not re-encode the JPG.
- The template must use `{{TOKEN}}` placeholders for every user-supplied value. Do NOT hardcode any content except locked constants.
- Add HTML comment markers around the 4th feature block exactly as shown — the AI uses these to locate and remove the block when FEATURE_COUNT=3.
- Background color: `#FDF8F3` (Warm Ivory — NOT the dark `#1e1409` from the base poster).
- Feature icons: orange accent bar `<div class="b-accent"></div>` — NO emoji.
- Bottom bar: hardcoded, verbatim, never tokenised.

- [ ] **Step 1: Create the template file**

Create `.augment/skills/create-poster/templates/poster-light.html` with this structure (replace `LOGO_BASE64_HERE` with the full base64 string extracted from `assets/base_poster.html`):

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>{{PRODUCT_NAME_EN}}</title>
<link href="https://fonts.googleapis.com/css2?family=Playfair+Display:wght@700&family=DM+Sans:wght@300;400;500;600&display=swap" rel="stylesheet">
<style>
  * { margin: 0; padding: 0; box-sizing: border-box; }
  body {
    background: #e8ddd3;
    display: flex;
    justify-content: center;
    align-items: center;
    min-height: 100vh;
    font-family: 'DM Sans', sans-serif;
    padding: 20px;
  }
  .poster {
    width: 540px;
    background: #FDF8F3;
    border-radius: 5px;
    overflow: hidden;
    box-shadow: 0 20px 60px rgba(0,0,0,0.12), 0 0 0 1px rgba(201,104,32,0.12);
  }
  .top-stripe {
    height: 4px;
    background: linear-gradient(90deg, #a04e10, #e8903a, #f5b060, #e8903a, #a04e10);
  }
  .header {
    padding: 18px 32px 16px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    border-bottom: 1px solid rgba(201,104,32,0.12);
  }
  .logo-wrap {
    background: #fff4e8;
    border-radius: 5px;
    padding: 5px 11px;
    display: inline-flex;
    align-items: center;
    box-shadow: 0 2px 8px rgba(0,0,0,0.08);
  }
  .logo-wrap img { height: 26px; display: block; }
  /* Status pill variants */
  .pill {
    display: flex; align-items: center; gap: 6px;
    border-radius: 20px; padding: 4px 12px;
  }
  .pill--live { background: rgba(74,222,128,0.1); border: 1px solid rgba(74,222,128,0.3); }
  .pill--soon { background: rgba(245,158,11,0.1); border: 1px solid rgba(245,158,11,0.3); }
  .pill--beta { background: rgba(96,165,250,0.1); border: 1px solid rgba(96,165,250,0.3); }
  .dot { width: 6px; height: 6px; border-radius: 50%; }
  .dot--live { background: #4ade80; }
  .dot--soon { background: #f59e0b; }
  .dot--beta { background: #60a5fa; }
  .pill-text {
    font-size: 10px; font-weight: 600;
    letter-spacing: 1.5px; text-transform: uppercase;
  }
  .pill-text--live { color: #16a34a; }
  .pill-text--soon { color: #d97706; }
  .pill-text--beta { color: #3b82f6; }
  /* Hero */
  .hero { padding: 24px 32px 18px; }
  .hero-sub {
    font-size: 10px; letter-spacing: 2.5px; text-transform: uppercase;
    color: #d4782a; font-weight: 600; margin-bottom: 10px;
  }
  .hero-title {
    font-family: 'Playfair Display', serif;
    font-size: 30px; line-height: 1.2; color: #1c1008; margin-bottom: 12px;
  }
  .hero-title span { color: #e8903a; }
  .hero-desc { font-size: 12.5px; line-height: 1.65; color: #5a3e28; }
  .hero-desc strong { color: #2d1a0a; font-weight: 500; }
  .hero-desc .vi {
    color: #b07040; font-size: 11px; display: block; margin-top: 4px;
  }
  /* Divider */
  .div {
    height: 1px;
    background: linear-gradient(90deg, transparent, rgba(201,104,32,0.2), transparent);
    margin: 0 32px;
  }
  /* Benefits */
  .benefits {
    padding: 18px 32px;
    display: grid;
    gap: 11px;
  }
  .benefits--4 { grid-template-columns: 1fr 1fr; }
  .benefits--3 { grid-template-columns: 1fr 1fr 1fr; }
  .benefit {
    background: rgba(201,104,32,0.05);
    border: 1px solid rgba(201,104,32,0.12);
    border-radius: 4px; padding: 13px 15px;
  }
  .b-accent {
    width: 16px; height: 2px;
    background: #e8903a; border-radius: 1px; margin-bottom: 8px;
  }
  .b-en { font-size: 12px; font-weight: 600; color: #1c1008; margin-bottom: 1px; }
  .b-vi { font-size: 10px; color: #e8903a; margin-bottom: 5px; font-weight: 500; }
  .b-desc { font-size: 11px; color: #8a6848; line-height: 1.5; }
  /* Footer */
  .footer-row {
    padding: 13px 32px;
    background: rgba(201,104,32,0.04);
    border-top: 1px solid rgba(201,104,32,0.1);
    display: flex; align-items: center; justify-content: space-between; gap: 16px;
  }
  .contact-text { font-size: 11px; color: #7a5535; line-height: 1.55; }
  .contact-text .vi-small {
    display: block; font-size: 10px; color: #b07040; margin-top: 2px;
  }
  .cta-btn {
    background: linear-gradient(135deg, #b85e18, #e8903a);
    color: #fff; font-size: 11px; font-weight: 600;
    letter-spacing: 1.2px; text-transform: uppercase;
    padding: 10px 22px; border-radius: 3px; border: none;
    cursor: pointer; white-space: nowrap; text-decoration: none;
    font-family: 'DM Sans', sans-serif;
    box-shadow: 0 4px 14px rgba(184,94,24,0.25);
  }
  .bottom {
    border-top: 1px solid rgba(201,104,32,0.08);
    padding: 9px 32px;
    display: flex; justify-content: space-between;
    background: #fdf0e4;
  }
  .bottom span {
    font-size: 9px; letter-spacing: 1px;
    color: rgba(28,16,8,0.3); text-transform: uppercase;
  }
</style>
</head>
<body>
<div class="poster">
  <div class="top-stripe"></div>

  <div class="header">
    <div class="logo-wrap">
      <img src="LOGO_BASE64_HERE" alt="Wanek">
    </div>
    {{STATUS_PILL}}
  </div>

  <div class="hero">
    <div class="hero-sub">{{HERO_SUB}}</div>
    <h1 class="hero-title">{{HERO_TITLE_PREFIX}}<span>{{HERO_HIGHLIGHT}}</span>{{HERO_TITLE_SUFFIX}}</h1>
    <p class="hero-desc">
      {{DESCRIPTION_EN}}
      <span class="vi">{{DESCRIPTION_VI}}</span>
    </p>
  </div>

  <div class="div"></div>

  <div class="benefits {{BENEFITS_CLASS}}">
    <div class="benefit">
      <div class="b-accent"></div>
      <div class="b-en">{{F1_EN}}</div>
      <div class="b-vi">{{F1_VI}}</div>
      <div class="b-desc">{{F1_DESC}}</div>
    </div>
    <div class="benefit">
      <div class="b-accent"></div>
      <div class="b-en">{{F2_EN}}</div>
      <div class="b-vi">{{F2_VI}}</div>
      <div class="b-desc">{{F2_DESC}}</div>
    </div>
    <div class="benefit">
      <div class="b-accent"></div>
      <div class="b-en">{{F3_EN}}</div>
      <div class="b-vi">{{F3_VI}}</div>
      <div class="b-desc">{{F3_DESC}}</div>
    </div>
    <!-- FEATURE_4_START -->
    <div class="benefit">
      <div class="b-accent"></div>
      <div class="b-en">{{F4_EN}}</div>
      <div class="b-vi">{{F4_VI}}</div>
      <div class="b-desc">{{F4_DESC}}</div>
    </div>
    <!-- FEATURE_4_END -->
  </div>

  <div class="footer-row">
    <div class="contact-text">
      {{CONTACT_EN}}
      <span class="vi-small">{{CONTACT_VI}}</span>
    </div>
    <a class="cta-btn" href="{{CTA_LINK}}">{{CTA_TEXT}}</a>
  </div>

  <div class="bottom">
    <span>WANEK FURNITURE CO., LTD.</span>
    <span>BUILT IN-HOUSE · AI TEAM</span>
  </div>
</div>
</body>
</html>
```

**Token clarifications for the implementer — read carefully before writing the template:**

- `{{HERO_TITLE_EN}}` is intentionally split into THREE tokens to enable the orange highlight `<span>`:
  - `{{HERO_TITLE_PREFIX}}` — all words in the title that come before the highlighted text (may be an empty string if the highlight starts the title)
  - `{{HERO_HIGHLIGHT}}` — the exact word(s) that render in orange inside `<span>`, confirmed during intake
  - `{{HERO_TITLE_SUFFIX}}` — all words after the highlighted text (may be empty string)
  - Example: title "Meet Your New ATS Platform." with highlight "ATS Platform." → PREFIX=`Meet Your New `, HIGHLIGHT=`ATS Platform.`, SUFFIX=``
  - The template must use all three tokens in that order: `{{HERO_TITLE_PREFIX}}<span>{{HERO_HIGHLIGHT}}</span>{{HERO_TITLE_SUFFIX}}`
  - Do NOT use a single `{{HERO_TITLE_EN}}` token — that would make the orange span impossible to render
- `{{STATUS_PILL}}` — the AI replaces this with the full pill HTML block (including dot + text) matching the chosen status and its CSS modifier class (`pill--live`, `pill--soon`, or `pill--beta`)
- `{{BENEFITS_CLASS}}` — `benefits--4` or `benefits--3` depending on FEATURE_COUNT
- `<!-- FEATURE_4_START -->` / `<!-- FEATURE_4_END -->` — comment markers the AI uses to locate and remove the 4th feature block when FEATURE_COUNT=3. These comments must remain in the template exactly as written.

- [ ] **Step 2: Verify template opens correctly in browser**

Open `.augment/skills/create-poster/templates/poster-light.html` in a browser.
Expected: poster renders with warm ivory background, orange stripe, Wanek logo visible, placeholder tokens visible as literal text, warm ivory card layout — no broken images, no console errors.

- [ ] **Step 3: Commit**

```bash
git add .augment/skills/create-poster/templates/poster-light.html
git commit -m "feat: add light-theme poster template with token placeholders"
```

---

## Task 3: Write `SKILL.md`

**Files:**
- Create: `.augment/skills/create-poster/SKILL.md`

- [ ] **Step 1: Create SKILL.md**

The SKILL.md has exactly **6 blocks**. Blocks are the top-level structural sections. Groups 1–4 (Product Basics, Hero, Features, Footer) are subdivisions within **Block 2 — Intake Rules**, not separate blocks. The 6 blocks are:
- Block 1: Activation
- Block 2: Intake Rules (contains Groups 1–4 as subsections)
- Block 3: Recommendation Behavior
- Block 4: Validation Gate
- Block 5: Generation Instructions
- Block 6: Locked Constants

Create `.augment/skills/create-poster/SKILL.md` with this exact content:

````markdown
---
name: create-poster
description: "Guides the user through a structured intake and generates a branded Wanek IT News HTML poster for internal product announcements. Trigger with: create poster, new poster, generate poster, make a poster."
---

# Create Poster

## Activation

Activate when the user says any of: "create poster", "new poster", "generate poster", "make a poster".

When activated, introduce yourself:

> "I will help you create a Wanek IT News poster. We will go through 4 groups of questions — I will ask for your raw input and suggest polished copy for each field. Nothing is generated until you confirm everything at the end.
>
> Let's start with Group 1: Product Basics."

---

## Intake Rules

Collect fields in this exact order, one group at a time. Do not advance to the next group until the user explicitly confirms all fields in the current group.

For every field:
1. Ask the question
2. User provides raw input
3. You recommend a refined version (see Recommendation Behavior below)
4. User confirms or adjusts
5. Lock the field and move on

---

## Group 1: Product Basics

Ask these 5 fields in order:

**Field 1 — Product name (English)**
- Ask: "What is the product name in English?"
- Constraint: max 40 characters
- Recommend a clean, title-cased version

**Field 2 — Product name (Vietnamese)**
- Ask: "What is the product name in Vietnamese? (Leave blank if you want me to suggest one from the English name)"
- Constraint: max 50 characters
- If blank: recommend a Vietnamese translation
- If provided: recommend a refined version or confirm if already clean

**Field 3 — Department / Team**
- Ask: "Which department or team owns this product? (e.g., IT Department, HR Department, Finance Team)"
- Constraint: max 30 characters
- Recommend a clean, title-cased version

**Field 4 — Launch type**
- Ask: "What type of launch is this? Choose one:
  1. New Tool
  2. System Update
  3. Feature Release
  4. New Platform"
- Accept number or text. Map to exact value: `New Tool`, `System Update`, `Feature Release`, `New Platform`
- No recommendation needed — present the confirmed selection

**Field 5 — Status**
- Ask: "What is the current status? Choose one:
  1. Now Live
  2. Coming Soon
  3. Beta"
- Accept number or text. Map to exact value: `Now Live`, `Coming Soon`, `Beta`
- No recommendation needed — present the confirmed selection

After all 5 fields confirmed, summarise Group 1 and ask: "Group 1 confirmed. Ready for Group 2?"

---

## Group 2: Hero Section

Ask these 4 fields in order:

**Field 6 — Hero title (English)**
- Ask: "Give me a headline for the poster — describe the product in a punchy phrase. I will shape it into the hero title."
- Constraint: max 6 words, no punctuation except a period at the end
- Recommend a 4–6 word title in sentence case ending with a period
- State word count in recommendation

**Field 7 — Highlight word(s)**
- Ask: "Which word or words in the title should be highlighted in orange? It must be an exact part of the title we just confirmed."
- Constraint: must be an exact substring of Field 6 value
- Recommend the product name or the most distinctive noun phrase from the title
- If the user's choice is not a substring of the title, flag it immediately: "That text does not appear exactly in the title '[TITLE]'. Please choose words that match exactly."

**Field 8 — Description (English)**
- Ask: "Describe the product in 2–3 sentences for the poster. What does it do and why does it matter?"
- Constraint: max 35 words
- Recommend a polished version, state word count
- If over 35 words: trim, label "Trimmed to fit limit:", state new count, re-ask for confirmation

**Field 9 — Description (Vietnamese)**
- Ask: "Provide the Vietnamese description, or leave blank and I will translate."
- Constraint: max 50 words
- If blank: recommend a natural Vietnamese translation of Field 8
- If provided and over 50 words: trim, label "Trimmed to fit limit:", state new count, re-ask

After all 4 fields confirmed, summarise Group 2 and ask: "Group 2 confirmed. Ready for Group 3?"

---

## Group 3: Features

**Field 10 — Feature count**
- Ask: "How many key features do you want to highlight? (3 or 4)"
- Accept only 3 or 4

Then for each feature (repeat FEATURE_COUNT times, label each as "Feature 1", "Feature 2", etc.):

**Feature N — Title (English)**
- Ask: "Feature [N] title in English — what is this feature called?"
- Constraint: max 3 words
- Recommend a clean, title-cased short label
- If over 3 words: trim, label "Trimmed to fit limit:", re-ask

**Feature N — Title (Vietnamese)**
- Ask: "Feature [N] title in Vietnamese? (Leave blank for suggestion)"
- Constraint: max 5 words
- If blank: recommend a Vietnamese translation of the English title

**Feature N — Description (English)**
- Ask: "Feature [N] — describe what this feature does in one short sentence."
- Constraint: max 12 words
- Recommend a concise action-oriented sentence
- If over 12 words: trim, label "Trimmed to fit limit:", re-ask

After all features confirmed, summarise Group 3 and ask: "Group 3 confirmed. Ready for Group 4?"

---

## Group 4: Footer

Ask these 4 fields in order:

**Field — Contact info (English)**
- Ask: "Who should employees contact for access or help? Provide a contact line."
- Constraint: max 15 words
- Recommend a clean contact sentence
- If over 15 words: trim, label "Trimmed to fit limit:", re-ask

**Field — Contact info (Vietnamese)**
- Ask: "Vietnamese contact line? (Leave blank for suggestion)"
- Constraint: max 20 words
- If blank: recommend a natural Vietnamese translation

**Field — CTA button text**
- Ask: "What should the call-to-action button say? (e.g., Get Access →, Learn More →, Try It Now →)"
- Constraint: max 4 words
- Recommend a short action phrase ending with →
- If over 4 words: trim, label "Trimmed to fit limit:", re-ask

**Field — CTA link**
- Ask: "What URL should the button link to? (Enter a URL or # if not applicable)"
- No constraint, no recommendation — use exactly as provided

After all 4 fields confirmed, summarise Group 4 and ask: "Group 4 confirmed. Ready for the final review?"

---

## Recommendation Behavior

- Tone: professional, concise, written for an internal company audience
- Vietnamese: always natural idiomatic Vietnamese — never literal word-for-word translation
- Always state the word or character count alongside the recommendation:
  - For word-limited fields: "(X/Y words)"
  - For character-limited fields: "(X/Y chars)"
- If the user's raw input is already within limits and well-written, say: "This looks good as-is (X/Y words). Confirming:" and proceed
- If the user's raw input exceeds the limit: do NOT advance. Trim or rewrite to fit, present as "Trimmed to fit limit: [text] (X/Y words)", and re-ask for confirmation

---

## Validation Gate

After Group 4 is confirmed, present a full summary table:

```
PRODUCT BASICS
  Product name (EN):   [value]
  Product name (VI):   [value]
  Department:          [value]
  Launch type:         [value]
  Status:              [value]

HERO
  Title:               [value]
  Highlight:           [value]
  Description (EN):    [value]
  Description (VI):    [value]

FEATURES ([count])
  Feature 1:  [EN title] / [VI title] — [description]
  Feature 2:  [EN title] / [VI title] — [description]
  Feature 3:  [EN title] / [VI title] — [description]
  [Feature 4: if applicable]

FOOTER
  Contact (EN):        [value]
  Contact (VI):        [value]
  CTA text:            [value]
  CTA link:            [value]

LOCKED (cannot be changed)
  Bottom bar:          WANEK FURNITURE CO., LTD. | BUILT IN-HOUSE · AI TEAM
```

Then validate:
- Confirm `[Highlight]` appears exactly as a substring within `[Title]`. If not, flag: "The highlight text '[highlight]' does not appear exactly in the title '[title]'. Please correct one or both." Re-ask only the affected field(s). Re-validate immediately after correction. Loop until the check passes.

Then ask: "Does everything look correct? Type **yes** to generate the poster."

Do not produce any HTML until the user types yes (or equivalent confirmation).

---

## Generation Instructions

When the user confirms, generate the poster as follows:

1. Read `.augment/skills/create-poster/templates/poster-light.html`

2. Construct derived values:
   - `{{HERO_SUB}}` = `[Department] · [Launch Type]`
   - `{{STATUS_PILL}}` = the full pill HTML matching the status:
     - Now Live: `<div class="pill pill--live"><span class="dot dot--live"></span><span class="pill-text pill-text--live">Now Live</span></div>`
     - Coming Soon: `<div class="pill pill--soon"><span class="dot dot--soon"></span><span class="pill-text pill-text--soon">Coming Soon</span></div>`
     - Beta: `<div class="pill pill--beta"><span class="dot dot--beta"></span><span class="pill-text pill-text--beta">Beta</span></div>`
   - `{{BENEFITS_CLASS}}` = `benefits--4` if 4 features, `benefits--3` if 3 features
   - `{{HERO_TITLE_PREFIX}}` = all words in the title before the highlight (may be empty)
   - `{{HERO_TITLE_SUFFIX}}` = all words in the title after the highlight (may be empty)

3. Replace all `{{TOKEN}}` placeholders with confirmed values

4. If FEATURE_COUNT = 3: remove everything between and including `<!-- FEATURE_4_START -->` and `<!-- FEATURE_4_END -->` from the output. Do not leave any empty elements or placeholder text.

5. If FEATURE_COUNT = 4: leave all 4 feature blocks intact.

6. Apply the `{{BENEFITS_CLASS}}` value to the `.benefits` div class.

7. Generate the output filename:
   - Date: current system date in YYYY-MM-DD format
   - Slug: product name (EN) lowercased, spaces → hyphens, strip all non-alphanumeric characters except hyphens
   - If slug is empty after stripping: use `poster`
   - Full filename: `YYYY-MM-DD-[slug].html`

8. Save the complete HTML to `output/[filename]`

9. Tell the user: "Poster generated: `output/[filename]`. Open it in your browser, take a screenshot, and embed the image in your email newsletter."

---

## Locked Constants

These values must appear verbatim in every generated poster. Never modify them regardless of any user input:

- Bottom bar left: `WANEK FURNITURE CO., LTD.`
- Bottom bar right: `BUILT IN-HOUSE · AI TEAM`
- Top stripe gradient: `linear-gradient(90deg, #a04e10, #e8903a, #f5b060, #e8903a, #a04e10)`
- Background: `#FDF8F3`
- Title font: `Playfair Display, serif`
- Body font: `DM Sans, sans-serif`
- Poster width: `540px`
````

- [ ] **Step 2: Verify SKILL.md is well-formed**

```bash
wc -l .augment/skills/create-poster/SKILL.md
```

Expected: between 180 and 280 lines.

```bash
grep -c "{{" .augment/skills/create-poster/SKILL.md
```

Expected: at least 15 matches (confirms all tokens are referenced).

- [ ] **Step 3: Commit**

```bash
git add .augment/skills/create-poster/SKILL.md
git commit -m "feat: add create-poster SKILL.md with intake rules and generation instructions"
```

---

## Task 4: Smoke Test

**No code to run — manual browser verification.**

- [ ] **Step 1: Manually fill the template (4-feature variant)**

Make a copy of the template for testing:

```bash
cp .augment/skills/create-poster/templates/poster-light.html output/smoke-test-4.html
```

Open `output/smoke-test-4.html` in a text editor and replace all tokens manually with sample values:

| Token | Sample value |
|---|---|
| `{{PRODUCT_NAME_EN}}` | `Leave As-Is — used in title tag only` |
| `{{HERO_SUB}}` | `IT Department · New Tool` |
| `{{STATUS_PILL}}` | `<div class="pill pill--live"><span class="dot dot--live"></span><span class="pill-text pill-text--live">Now Live</span></div>` |
| `{{HERO_TITLE_PREFIX}}` | `Meet Your New ` |
| `{{HERO_HIGHLIGHT}}` | `ATS Platform.` |
| `{{HERO_TITLE_SUFFIX}}` | `` (empty) |
| `{{DESCRIPTION_EN}}` | `Our in-house Applicant Tracking System is officially live — making hiring faster, smarter, and fully transparent.` |
| `{{DESCRIPTION_VI}}` | `Hệ thống nội bộ đã chính thức ra mắt — tuyển dụng nhanh hơn, rõ ràng hơn.` |
| `{{BENEFITS_CLASS}}` | `benefits--4` |
| `{{F1_EN}}` | `Faster Screening` |
| `{{F1_VI}}` | `Sàng lọc nhanh hơn` |
| `{{F1_DESC}}` | `Auto-rank candidates by role criteria.` |
| `{{F2_EN}}` | `Full Visibility` |
| `{{F2_VI}}` | `Minh bạch toàn trình` |
| `{{F2_DESC}}` | `Real-time pipeline tracking for every position.` |
| `{{F3_EN}}` | `Team Collaboration` |
| `{{F3_VI}}` | `Phối hợp nội bộ` |
| `{{F3_DESC}}` | `HR and hiring managers work together.` |
| `{{F4_EN}}` | `Smart Reports` |
| `{{F4_VI}}` | `Báo cáo thông minh` |
| `{{F4_DESC}}` | `Weekly and monthly hiring analytics.` |
| `{{CONTACT_EN}}` | `Contact IT Dept. for access and onboarding.` |
| `{{CONTACT_VI}}` | `Liên hệ bộ phận IT để được hướng dẫn sử dụng.` |
| `{{CTA_LINK}}` | `#` |
| `{{CTA_TEXT}}` | `Get Access →` |

Also replace `LOGO_BASE64_HERE` with the base64 string from `assets/base_poster.html`.

- [ ] **Step 2: Open 4-feature smoke test in browser**

Open `output/smoke-test-4.html` in your browser.

Expected:
- Warm ivory background (#FDF8F3)
- Orange gradient top stripe
- Wanek logo visible in cream pill
- "Now Live" pill in green top-right
- "IT Department · New Tool" sub-label
- "Meet Your New " + orange "ATS Platform." title
- Description in dark brown, Vietnamese line below in amber
- 2×2 grid of 4 feature cards, each with orange accent bar
- Orange gradient CTA button bottom-right
- `WANEK FURNITURE CO., LTD.` and `BUILT IN-HOUSE · AI TEAM` in bottom bar

- [ ] **Step 3: Manually fill the template (3-feature variant)**

```bash
cp .augment/skills/create-poster/templates/poster-light.html output/smoke-test-3.html
```

Open `output/smoke-test-3.html`, apply same token replacements as Step 1 but:
- Set `{{BENEFITS_CLASS}}` = `benefits--3`
- Remove everything between and including `<!-- FEATURE_4_START -->` and `<!-- FEATURE_4_END -->`
- Change STATUS_PILL to the "Coming Soon" variant

- [ ] **Step 4: Open 3-feature smoke test in browser**

Open `output/smoke-test-3.html` in your browser.

Expected:
- 1×3 row of 3 feature cards (no 4th card, no empty space)
- "Coming Soon" pill in amber top-right
- All other elements identical to 4-feature variant

- [ ] **Step 5: Commit smoke test files**

```bash
git add output/smoke-test-4.html output/smoke-test-3.html
git commit -m "test: add smoke test posters for 3 and 4 feature variants"
```

---

## Task 5: Final Cleanup and .gitignore

- [ ] **Step 1: Confirm .gitignore covers superpowers directory**

```bash
cat .gitignore
```

Expected output:
```
.superpowers/
```

- [ ] **Step 2: Verify full file structure**

```bash
find . -not -path './.git/*' -not -path './.augment/skills/ui-ux-pro-max/*' | sort
```

Expected:
```
.
./.augment
./.augment/skills
./.augment/skills/create-poster
./.augment/skills/create-poster/SKILL.md
./.augment/skills/create-poster/templates
./.augment/skills/create-poster/templates/poster-light.html
./assets
./assets/base_poster.html
./assets/logo.jpg
./.gitignore
./docs
./docs/superpowers
./docs/superpowers/plans
./docs/superpowers/plans/2026-04-01-create-poster-skill.md
./docs/superpowers/specs
./docs/superpowers/specs/2026-04-01-wanek-it-news-design.md
./output
./output/.gitkeep
./output/smoke-test-3.html
./output/smoke-test-4.html
```

- [ ] **Step 3: Final commit**

```bash
git add -A
git commit -m "chore: final structure verification and cleanup"
```
