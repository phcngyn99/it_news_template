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

**Field 6 — Layout variant**
- Ask: "Which layout do you prefer?
  1. Base — standard vertical stack with 2×2 feature grid (or 1×3 for 3 features)
  2. L3 — compact hero with full-width stacked feature rows, EN/VI titles inline"
- Accept number or text. Map to exact value: `base` or `l3`
- No recommendation needed — present the confirmed selection

After all 6 fields confirmed, summarise Group 1 and ask: "Group 1 confirmed. Ready for Group 2?"

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

After all 2 fields confirmed, summarise Group 4 and ask: "Group 4 confirmed. Ready for Group 5?"

---

## Group 5: Screenshots (Optional)

**Field — Screenshot count**
- Ask: "Would you like to include product screenshots? This is optional. If yes, how many? (1, 2, or 3). If not, just say no or 0 to skip."
- Accept 0 (or "no"/"skip"), 1, 2, or 3
- If 0 or skipped: skip the rest of Group 5 entirely and move to final review
- If 1–3: ask user to place the image files in `assets/screenshots/` before proceeding. Screenshots will be scaled to fit the full poster width (540px) automatically.

Then for each screenshot (repeat SCREENSHOT_COUNT times):

**Screenshot N — Filename**
- Ask: "Screenshot [N] — what is the filename? (e.g., dashboard.png)"
- The file must exist in `assets/screenshots/`
- No recommendation — use exactly as provided

After all filenames confirmed (or if count is 0), summarise Group 5 and ask: "Group 5 confirmed. Ready for the final review?"

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

After Group 5 is confirmed, present a full summary table:

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

SCREENSHOTS
  Count:               [0/1/2/3]
  Files:               [filename1, filename2, ...] (or "None")

LOCKED (cannot be changed)
  Bottom bar:          WANEK FURNITURE CO., LTD. | AI TEAM
```

Then validate:
- Confirm `[Highlight]` appears exactly as a substring within `[Title]`. If not, flag: "The highlight text '[highlight]' does not appear exactly in the title '[title]'. Please correct one or both." Re-ask only the affected field(s). Re-validate immediately after correction. Loop until the check passes.

Then ask: "Does everything look correct? Type **yes** to generate the poster."

Do not produce any HTML until the user types yes (or equivalent confirmation).

---

## Generation Instructions

When the user confirms, generate the poster as follows:

1. Read the template based on the confirmed layout variant:
   - If `base`: read `.augment/skills/create-poster/templates/poster-light.html`
   - If `l3`: read `.augment/skills/create-poster/templates/poster-light-l3.html`

2. Construct derived values before replacing tokens:
   - `{{HERO_SUB}}` = `[Department] · [Launch Type]`
   - `{{STATUS_PILL}}` = the full pill HTML matching the confirmed status:
     - Now Live: `<div class="pill pill--live"><span class="dot dot--live"></span><span class="pill-text pill-text--live">Now Live</span></div>`
     - Coming Soon: `<div class="pill pill--soon"><span class="dot dot--soon"></span><span class="pill-text pill-text--soon">Coming Soon</span></div>`
     - Beta: `<div class="pill pill--beta"><span class="dot dot--beta"></span><span class="pill-text pill-text--beta">Beta</span></div>`
   - `{{BENEFITS_CLASS}}` = `benefits--4` if 4 features, `benefits--3` if 3 features (only used by `base` layout; L3 layout does not use this token — leave it unreplaced, it will not appear in L3 template)
   - `{{HERO_TITLE_PREFIX}}` = all words in the title that come before the highlighted text (empty string if highlight starts the title)
   - `{{HERO_TITLE_SUFFIX}}` = all words in the title that come after the highlighted text (empty string if highlight ends the title)

3. Replace every `{{TOKEN}}` placeholder with its confirmed value using this exact map:
   - `{{PRODUCT_NAME_EN}}` → confirmed product name (EN)
   - `{{HERO_SUB}}` → derived value from step 2
   - `{{STATUS_PILL}}` → derived HTML from step 2
   - `{{HERO_TITLE_PREFIX}}` → derived value from step 2
   - `{{HERO_HIGHLIGHT}}` → confirmed highlight word(s)
   - `{{HERO_TITLE_SUFFIX}}` → derived value from step 2
   - `{{DESCRIPTION_EN}}` → confirmed description (EN)
   - `{{DESCRIPTION_VI}}` → confirmed description (VI)
   - `{{BENEFITS_CLASS}}` → derived value from step 2
   - `{{F1_EN}}` → Feature 1 title (EN)
   - `{{F1_VI}}` → Feature 1 title (VI)
   - `{{F1_DESC}}` → Feature 1 description (EN)
   - `{{F2_EN}}` → Feature 2 title (EN)
   - `{{F2_VI}}` → Feature 2 title (VI)
   - `{{F2_DESC}}` → Feature 2 description (EN)
   - `{{F3_EN}}` → Feature 3 title (EN)
   - `{{F3_VI}}` → Feature 3 title (VI)
   - `{{F3_DESC}}` → Feature 3 description (EN)
   - `{{F4_EN}}` → Feature 4 title (EN) — only used when FEATURE_COUNT = 4
   - `{{F4_VI}}` → Feature 4 title (VI) — only used when FEATURE_COUNT = 4
   - `{{F4_DESC}}` → Feature 4 description (EN) — only used when FEATURE_COUNT = 4
   - `{{CONTACT_EN}}` → confirmed contact info (EN)
   - `{{CONTACT_VI}}` → confirmed contact info (VI)
   - `{{SCREENSHOTS}}` → generated screenshot HTML (see step 7)

4. If FEATURE_COUNT = 3: remove everything between and including `<!-- FEATURE_4_START -->` and `<!-- FEATURE_4_END -->` from the output. Do not leave any empty elements or placeholder text.

5. If FEATURE_COUNT = 4: leave all 4 feature blocks intact, remove only the comment lines `<!-- FEATURE_4_START -->` and `<!-- FEATURE_4_END -->`.

6. If layout is `base`: apply the `{{BENEFITS_CLASS}}` value to the `.benefits` div class attribute. If layout is `l3`: skip this step (L3 uses stacked rows, not a grid).

7. Handle screenshots:
   - If SCREENSHOT_COUNT = 0: remove everything between and including `<!-- SCREENSHOTS_START -->` and `<!-- SCREENSHOTS_END -->` from the output.
   - If SCREENSHOT_COUNT >= 1: for each confirmed filename, read the file from `assets/screenshots/[filename]`, convert it to base64, and generate an `<img>` tag: `<img src="data:image/png;base64,[BASE64]" alt="Screenshot [N]">`. Replace `{{SCREENSHOTS}}` with all generated `<img>` tags concatenated (one per line). If the file extension is `.jpg` or `.jpeg`, use `data:image/jpeg;base64,` instead of `data:image/png;base64,`.

8. Generate the output filename:
   - Date: current system date in YYYY-MM-DD format
   - Slug: product name (EN) lowercased, spaces replaced with hyphens, strip all characters that are not alphanumeric or hyphens
   - If slug is empty after stripping: use `poster`
   - Standard filename: `YYYY-MM-DD-[slug].html`

9. Save the standard poster to `output/[standard filename]`

10. **Generate Pro Variants (automatic, always run)**

    After saving the standard poster, generate 1-2 additional "pro" variants using the `ui-ux-pro-max` design system:

    a. Run the design system search using the product info as query:
    ```bash
    python3 .augment/skills/ui-ux-pro-max/scripts/search.py "[product name] [department] [launch type] internal tool announcement poster" --design-system -f markdown
    ```

    b. From the design system output, extract:
       - **Typography**: recommended font pairing (heading + body) from Google Fonts
       - **Color palette**: primary, secondary, accent, background colors
       - **Style**: visual effects, border treatments, card styles

    c. Create 1-2 pro variant HTML files by duplicating the standard poster and modifying its `<style>` block:
       - Replace the Google Fonts `<link>` with the recommended font pairing
       - Replace heading `font-family` with the recommended heading font
       - Replace body `font-family` with the recommended body font
       - Adjust color values (backgrounds, accents, text colors) using the recommended palette
       - Adapt the top stripe gradient, feature card backgrounds, and pill accents to the new palette
       - Adapt the bottom bar gradient to harmonize with the new palette

    d. **Pro variant constraints (must follow strictly):**
       - Primary color MUST remain orange — the recommended palette supplements but does not replace orange
       - Theme MUST be light — background must be a light color (white, cream, light gray, etc.)
       - Logo MUST be the same transparent PNG — never change or remove it
       - Bottom bar text MUST remain: left `WANEK FURNITURE CO., LTD.`, right `AI TEAM` with underline
       - Poster width MUST remain `540px`
       - All content (text, tokens, features, screenshots) MUST be identical to the standard poster
       - Only the visual styling (fonts, colors, gradients, borders, shadows) changes

    e. Save pro variants with suffixed filenames:
       - Pro variant 1: `YYYY-MM-DD-[slug]-pro-1.html`
       - Pro variant 2: `YYYY-MM-DD-[slug]-pro-2.html`

    f. If the `ui-ux-pro-max` search returns an error or no useful results, skip pro variants and inform the user: "Pro variants could not be generated — design system search returned no results."

11. Tell the user:
    "Posters generated:
    - Standard: `output/[standard filename]`
    - Pro variant 1: `output/[pro-1 filename]`
    - Pro variant 2: `output/[pro-2 filename]` (if generated)

    Open each in your browser, pick the one you like best, take a screenshot, and embed the image in your email newsletter."

---

## Locked Constants

### Standard poster (always locked)

These values must appear verbatim in every standard poster:

- Bottom bar left: `WANEK FURNITURE CO., LTD.`
- Bottom bar right: `AI TEAM` (with gold underline)
- Top stripe gradient: `linear-gradient(90deg, #a04e10, #e8903a, #f5b060, #e8903a, #a04e10)`
- Background: `#FDF8F3`
- Title font: `Playfair Display, serif`
- Body font: `DM Sans, sans-serif`
- Poster width: `540px`
- Logo: transparent PNG from template (never change)

### Pro variants (partially locked)

These values are locked in pro variants — never change them:

- Bottom bar left: `WANEK FURNITURE CO., LTD.`
- Bottom bar right: `AI TEAM` (with underline accent)
- Poster width: `540px`
- Logo: same transparent PNG as standard poster
- Primary color: orange (must remain the dominant brand color)
- Theme: light only (background must be a light color)
- Content: identical text, features, and screenshots as the standard poster

These values CAN change in pro variants (driven by ui-ux-pro-max recommendations):

- Title font and body font (must be from Google Fonts)
- Background color (must remain light)
- Top stripe gradient (adapt to new palette, keep orange as primary)
- Feature card backgrounds, borders, and accents
- Status pill colors and styles
- Footer gradient (adapt to new palette)
- Contact text colors
- Accent line and underline colors
