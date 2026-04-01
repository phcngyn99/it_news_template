# Wanek IT News — Create Poster Skill Design Spec
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
| Theme | Light — Warm Ivory `#FDF8F3` |
| Language | Always bilingual: English (primary) + Vietnamese (secondary) |
| Output format | Self-contained HTML file → browser screenshot → image |
| Poster width | 540px fixed |
| Font: titles | Playfair Display 700 (Google Fonts) |
| Font: body | DM Sans 300/400/500/600 (Google Fonts) |
| Top stripe | Gradient `#a04e10 → #e8903a → #f5b060 → #e8903a → #a04e10` |
| Background | `#FDF8F3` (Warm Ivory) |
| Bottom bar line 1 | `WANEK FURNITURE CO., LTD.` — hardcoded, never editable |
| Bottom bar line 2 | `BUILT IN-HOUSE · AI TEAM` — hardcoded, never editable |
| Feature icons | Orange accent bar (`#e8903a`, 16×2px) — no emoji |
| Scripts/tooling | None — skill is purely text-driven via SKILL.md |

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
│   ├── logo.jpg                         (source — baked into template as base64)
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
| `{{HERO_TITLE_EN}}` | User → AI recommendation | max 6 words |
| `{{HERO_HIGHLIGHT}}` | User → AI recommendation | exact substring of `{{HERO_TITLE_EN}}` |
| `{{DESCRIPTION_EN}}` | User → AI recommendation | max 35 words |
| `{{DESCRIPTION_VI}}` | User → AI recommendation | max 50 words |
| `{{FEATURE_COUNT}}` | User selects | 3 or 4 |
| `{{F1_EN}}` – `{{F4_EN}}` | User → AI recommendation | max 3 words each |
| `{{F1_VI}}` – `{{F4_VI}}` | User → AI recommendation | max 5 words each |
| `{{F1_DESC}}` – `{{F4_DESC}}` | User → AI recommendation | max 12 words each |
| `{{CONTACT_EN}}` | User → AI recommendation | max 15 words |
| `{{CONTACT_VI}}` | User → AI recommendation | max 20 words |
| `{{CTA_TEXT}}` | User → AI recommendation | max 4 words |
| `{{CTA_LINK}}` | User provides | URL or `#` |

---

## Layout Variants

### 4-Feature: 2×2 Grid
- `grid-template-columns: 1fr 1fr`
- Used when `{{FEATURE_COUNT}}` = 4

### 3-Feature: 1×3 Row
- `grid-template-columns: 1fr 1fr 1fr`
- Used when `{{FEATURE_COUNT}}` = 3

---

## SKILL.md Internal Structure

### Block 1 — Activation
- Trigger phrases: "create poster", "new poster", "generate poster", "make a poster"
- AI introduces itself and explains the two-phase process (intake → generate)

### Block 2 — Intake Rules
- Four groups collected in order: Product Basics → Hero → Features → Footer
- One group at a time; AI waits for full confirmation before advancing
- For each field: AI asks question, user provides raw input, AI recommends refined version with constraint count, user confirms or adjusts

### Block 3 — Recommendation Behavior
- Tone: professional, concise, internal-audience
- Vietnamese: natural idiomatic Vietnamese, not literal translation
- Every recommendation states word/char count alongside the suggested copy
- If user's raw input is already within limits and well-written, AI says so and confirms rather than rewriting

### Block 4 — Validation Gate
- After all groups confirmed, AI presents a full summary table of all locked fields
- AI explicitly asks: "Does everything look correct? Type yes to generate."
- No HTML output until user confirms

### Block 5 — Generation Instructions
- Read `templates/poster-light.html`
- Replace all `{{TOKEN}}` placeholders with confirmed values
- Apply correct grid layout based on `{{FEATURE_COUNT}}`
- Apply correct pill color/dot color based on `{{STATUS}}`
- Auto-generate output filename: `YYYY-MM-DD-<product-slug>.html` (slug = lowercase product name EN, spaces → hyphens)
- Save file to `output/`

### Block 6 — Locked Constants
- Never modify regardless of user input: bottom bar text, stripe gradient, fonts, poster width, background color

---

## Intake Flow — Group Detail

### Group 1: Product Basics
1. Product name (EN)
2. Product name (VI) — AI recommends if user provides EN only
3. Department / Team
4. Launch type — select: New Tool / System Update / Feature Release / New Platform
5. Status — select: Now Live / Coming Soon / Beta

### Group 2: Hero Section
6. Hero title (EN) — max 6 words
7. Which word(s) to highlight in orange — must be exact substring of title
8. Description (EN) — max 35 words
9. Description (VI) — max 50 words; AI recommends if user provides EN only

### Group 3: Features
10. Number of features — 3 or 4
11. For each feature: title EN (max 3 words), title VI (max 5 words), description EN (max 12 words)
    - AI recommends VI title if user provides EN only

### Group 4: Footer
12. Contact info (EN) — max 15 words
13. Contact info (VI) — max 20 words; AI recommends if user provides EN only
14. CTA button text — max 4 words
15. CTA link — URL or `#`

---

## Output Naming Convention
- Pattern: `YYYY-MM-DD-<product-slug>.html`
- Slug: product name EN lowercased, spaces replaced with hyphens, special chars stripped
- Example: "Applicant Tracking System" → `2026-04-01-applicant-tracking-system.html`
- Saved to: `output/`
