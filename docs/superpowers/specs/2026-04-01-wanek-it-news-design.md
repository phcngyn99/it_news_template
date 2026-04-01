# Wanek IT News - Create Poster Skill Design Spec
Date: 2026-04-01

## Purpose
- Generate HTML poster announcements for internal Wanek product/tool launches
- End user invokes skill via prompt, AI conducts structured intake, generates self-contained HTML file
- HTML is rendered in browser and screenshotted into image for email newsletter embedding

---

## Constraints & Locked Values

| Property | Value |
|---|---|
| Audience | Internal Wanek employees only |
| Theme | Light - Warm Ivory `#FDF8F3` |
| Language | Always bilingual: English (primary) + Vietnamese (secondary) |
| Output format | Self-contained HTML file → browser screenshot → image |
| Poster width | 540px fixed |
| Font: titles | Playfair Display 700 (Google Fonts) |
| Font: body | DM Sans 300/400/500/600 (Google Fonts) |
| Top stripe | Gradient `#a04e10 → #e8903a → #f5b060 → #e8903a → #a04e10` |
| Background | `#FDF8F3` (Warm Ivory) |
| Bottom bar line 1 | `WANEK FURNITURE CO., LTD.` - hardcoded, never editable |
| Bottom bar line 2 | `AI TEAM` - gold text `#f5b060` with gold underline accent, hardcoded, never editable |
| Bottom bar background | `linear-gradient(135deg, #5a3e28, #a04e10)` - diagonal brown-to-orange |
| Logo | `logo-removebg.png` - transparent PNG, baked into template as base64. No background, border, or shadow on logo container. |
| CTA button | Removed - poster is a static image, buttons are not clickable |
| Feature icons | Orange accent bar (`#e8903a`, 16×2px) - no emoji |
| Scripts/tooling | None - skill is purely text-driven via SKILL.md |

---

## File Structure

```
it_news_template/
├── .augment/
│   └── skills/
│       ├── ui-ux-pro-max/               (installed, unchanged)
│       └── create-poster/
│           ├── SKILL.md                 (intake rules, constraints, generation instructions)
│           └── templates/
│               └── poster-light.html    (HTML template with {{TOKEN}} placeholders)
├── assets/
│   ├── logo.jpg                         (source - baked into template as base64)
│   └── base_poster.html                 (dark theme reference, archive only)
├── docs/
│   └── superpowers/
│       └── specs/
│           └── 2026-04-01-wanek-it-news-design.md
└── output/
    └── YYYY-MM-DD-<product-slug>.html   (generated posters)
```

---

## Status Pill Variants

| Status | Pill Color | Dot Color |
|---|---|---|
| Now Live | `rgba(74,222,128,0.1)` border `rgba(74,222,128,0.3)` | `#4ade80` |
| Coming Soon | `rgba(245,158,11,0.1)` border `rgba(245,158,11,0.3)` | `#f59e0b` |
| Beta | `rgba(96,165,250,0.1)` border `rgba(96,165,250,0.3)` | `#60a5fa` |

---

## Template Token Map

| Token | Source | Constraint |
|---|---|---|
| `{{PRODUCT_NAME_EN}}` | User → AI recommendation | max 40 chars |
| `{{PRODUCT_NAME_VI}}` | User → AI recommendation | max 50 chars |
| `{{DEPARTMENT}}` | User → AI recommendation | max 30 chars |
| `{{LAUNCH_TYPE}}` | User selects | New Tool / System Update / Feature Release / New Platform |
| `{{STATUS}}` | User selects | Now Live / Coming Soon / Beta |
| `{{HERO_SUB}}` | Auto-generated | `{{DEPARTMENT}} · {{LAUNCH_TYPE}}` |
| `{{HERO_TITLE_EN}}` | User → AI recommendation | max 6 words. **English only - intentional bilingual exception.** The hero title is a large display element; adding a VI line would break the visual hierarchy. |
| `{{HERO_HIGHLIGHT}}` | User → AI recommendation | exact substring of `{{HERO_TITLE_EN}}` |
| `{{DESCRIPTION_EN}}` | User → AI recommendation | max 35 words |
| `{{DESCRIPTION_VI}}` | User → AI recommendation | max 50 words |
| `{{FEATURE_COUNT}}` | User selects | 3 or 4 |
| `{{F1_EN}}` – `{{F4_EN}}` | User → AI recommendation | max 3 words each |
| `{{F1_VI}}` – `{{F4_VI}}` | User → AI recommendation | max 5 words each |
| `{{F1_DESC}}` – `{{F4_DESC}}` | User → AI recommendation | max 12 words each. **English only - intentional bilingual exception.** Feature cards are compact; a VI description line would overflow the card bounds. |
| `{{CONTACT_EN}}` | User → AI recommendation | max 15 words |
| `{{CONTACT_VI}}` | User → AI recommendation | max 20 words |
| `{{LAYOUT}}` | User selects | `base` or `l3` - determines which template file to use |
| `{{SCREENSHOTS}}` | AI-generated | 0–3 `<img>` tags, base64-encoded from `assets/screenshots/`. Entire screenshot section removed if count = 0. |
| `{{SCREENSHOT_COUNT}}` | User selects | 0, 1, 2, or 3 |

---

## Layout Variants

### Base layout (`poster-light.html`)
- Standard vertical stack: header → hero → divider → features grid → contact → bottom bar
- 4-Feature: `grid-template-columns: 1fr 1fr` (2×2 grid)
- 3-Feature: `grid-template-columns: 1fr 1fr 1fr` (1×3 row)

### L3 layout (`poster-light-l3.html`)
- Compact hero with EN/VI description inline (not stacked)
- Features as full-width stacked vertical rows with tall left accent bars
- EN title and VI title sit inline on the same line per feature
- 4-Feature: 4 stacked rows
- 3-Feature: 3 stacked rows (4th block removed via comment markers)

---

## SKILL.md Internal Structure

### Block 1 - Activation
- Trigger phrases: "create poster", "new poster", "generate poster", "make a poster"
- AI introduces itself and explains the two-phase process (intake → generate)

### Block 2 - Intake Rules
- Four groups collected in order: Product Basics → Hero → Features → Footer
- One group at a time; AI waits for full confirmation before advancing
- For each field: AI asks question, user provides raw input, AI recommends refined version with constraint count, user confirms or adjusts

### Block 3 - Recommendation Behavior
- Tone: professional, concise, internal-audience
- Vietnamese: natural idiomatic Vietnamese, not literal translation
- Every recommendation states word/char count alongside the suggested copy
- If user's raw input is already within limits and well-written, AI says so and confirms rather than rewriting
- If user's raw input exceeds the limit: AI must NOT advance to the next field. AI trims/rewrites the input to fit the constraint, presents the trimmed recommendation explicitly labeled "Trimmed to fit limit:", states the new count, and re-asks for confirmation before proceeding

### Block 4 - Validation Gate
- After all groups confirmed, AI presents a full summary table of all locked fields
- AI validates that `{{HERO_HIGHLIGHT}}` is an exact substring of `{{HERO_TITLE_EN}}`. If not, AI flags the mismatch and requires the user to correct one or both fields. After each correction, AI re-validates immediately. This loop repeats until the substring check passes before the summary proceeds
- AI explicitly asks: "Does everything look correct? Type yes to generate."
- No HTML output until user confirms

### Block 5 - Generation Instructions
- Read `.augment/skills/create-poster/templates/poster-light.html` (workspace-relative path)
- Before replacing tokens, construct derived values:
  - `{{HERO_SUB}}` = `{{DEPARTMENT}} · {{LAUNCH_TYPE}}` (concatenated from confirmed Group 1 values)
- Replace all `{{TOKEN}}` placeholders with confirmed values
- Apply correct grid layout based on `{{FEATURE_COUNT}}`:
  - If `{{FEATURE_COUNT}}` = 4: use `grid-template-columns: 1fr 1fr` (2×2 grid), include all 4 feature blocks
  - If `{{FEATURE_COUNT}}` = 3: use `grid-template-columns: 1fr 1fr 1fr` (1×3 row), remove the entire 4th feature HTML block (`{{F4_EN}}`, `{{F4_VI}}`, `{{F4_DESC}}` and their wrapper element) from the output - do not leave empty or placeholder content
- Apply correct pill color/dot color based on `{{STATUS}}` (see Status Pill Variants table)
- `YYYY-MM-DD` in the output filename = current system date at generation time
- Auto-generate output filename: `YYYY-MM-DD-<product-slug>.html` (slug = lowercase product name EN, spaces → hyphens, special chars stripped). If stripping produces an empty string, use the fallback slug `poster`
- Save standard poster to `output/`
- **Auto-generate 1-2 pro variants** using `ui-ux-pro-max` design system search (see Block 6)

### Block 6 - Pro Variants (ui-ux-pro-max integration)
- After standard poster is saved, automatically run: `python3 .augment/skills/ui-ux-pro-max/scripts/search.py "[product] [department] [type]" --design-system -f markdown`
- Extract typography, color palette, and style recommendations
- Generate 1-2 pro variant HTML files by modifying the standard poster's `<style>` block
- Pro variant filenames: `YYYY-MM-DD-[slug]-pro-1.html`, `YYYY-MM-DD-[slug]-pro-2.html`
- **Pro variant constraints:**
  - Primary color: orange (must remain dominant)
  - Theme: light only
  - Logo: same transparent PNG
  - Bottom bar text: locked (`WANEK FURNITURE CO., LTD.` + `AI TEAM`)
  - Width: 540px
  - Content: identical to standard poster
- **Pro variants CAN change:** fonts (Google Fonts), background color (must be light), gradients, card styling, pill colors, accent colors

### Block 7 - Locked Constants (standard poster)
- Never modify regardless of user input: bottom bar text, stripe gradient, fonts, poster width, background color

---

## Intake Flow - Group Detail

### Group 1: Product Basics
1. Product name (EN)
2. Product name (VI) - AI recommends if user provides EN only
3. Department / Team
4. Launch type - select: New Tool / System Update / Feature Release / New Platform
5. Status - select: Now Live / Coming Soon / Beta

### Group 2: Hero Section
6. Hero title (EN) - max 6 words
7. Which word(s) to highlight in orange - must be exact substring of title
8. Description (EN) - max 35 words
9. Description (VI) - max 50 words; AI recommends if user provides EN only

### Group 3: Features
10. Number of features - 3 or 4
11. For each feature: title EN (max 3 words), title VI (max 5 words), description EN (max 12 words)
    - AI recommends VI title if user provides EN only

### Group 4: Footer
12. Contact info (EN) - max 15 words
13. Contact info (VI) - max 20 words; AI recommends if user provides EN only

### Group 5: Screenshots
14. Screenshot count - 0, 1, 2, or 3
15. For each screenshot: filename in `assets/screenshots/` (e.g., `dashboard.png`)
    - Files must be placed in `assets/screenshots/` before intake
    - AI reads and embeds as base64
    - Stacked vertically at full width, "PREVIEW" section label above
    - If count = 0: entire screenshot section removed from output

---

## Output Naming Convention
- Standard: `YYYY-MM-DD-<product-slug>.html`
- Pro variant 1: `YYYY-MM-DD-<product-slug>-pro-1.html`
- Pro variant 2: `YYYY-MM-DD-<product-slug>-pro-2.html`
- Slug: product name EN lowercased, spaces replaced with hyphens, special chars stripped
- Example: "Applicant Tracking System" → `2026-04-01-applicant-tracking-system.html`, `-pro-1.html`, `-pro-2.html`
- Saved to: `output/`
