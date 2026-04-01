# Wanek IT News Poster Generator

An Augment Agent skill that guides you through a structured intake and generates branded HTML posters for internal product announcements at Wanek Furniture.

## Quick Start

Open this repo in your editor with Augment Agent active, then type:

```
create poster
```

The agent walks you through 5 groups of questions, recommends polished copy for each field, and generates a ready-to-screenshot HTML poster.

## How It Works

1. **You answer questions** -- product name, headline, features, contact info, optional screenshots
2. **The agent recommends** -- refined copy, Vietnamese translations, word count checks
3. **You confirm** -- lock each field, review the full summary
4. **The agent generates** -- a self-contained HTML file in `output/`
5. **You screenshot** -- open in browser, screenshot, embed in your email newsletter

## Intake Groups

| Group | Fields |
|-------|--------|
| 1. Product Basics | Product name (EN/VI), department, launch type, status, layout variant |
| 2. Hero Section | Hero title (EN), highlight word(s), description (EN/VI) |
| 3. Features | 3 or 4 features, each with title (EN/VI) and description (EN) |
| 4. Footer | Contact info (EN/VI) |
| 5. Screenshots | Optional: 1-3 product screenshots from `assets/screenshots/` |

## Layout Variants

**Base** -- Standard vertical stack with 2x2 feature grid (or 1x3 for 3 features)

**L3** -- Compact hero with full-width stacked feature rows, EN/VI titles inline

## Project Structure

```
.augment/skills/create-poster/
  SKILL.md                          # Agent instructions (intake, validation, generation)
  templates/
    poster-light.html               # Base layout template
    poster-light-l3.html            # L3 layout template
assets/
    logo-removebg.png               # Wanek logo (transparent PNG)
    screenshots/                    # Place product screenshots here
output/                             # Generated posters land here
docs/superpowers/
    specs/                          # Design specification
    plans/                          # Implementation plan
```

## Design

- **Theme**: Light only -- warm ivory (#FDF8F3) background
- **Fonts**: Playfair Display (headings), DM Sans (body)
- **Width**: 540px fixed
- **Status pills**: Now Live (green), Coming Soon (amber), Beta (blue)
- **Footer**: Diagonal gradient bottom bar with gold "AI TEAM" accent
- **Logo**: Transparent PNG, embedded as base64
- **Screenshots**: Optional, stacked vertically, full poster width

## Output

Generated posters are saved to `output/` with the naming convention:

```
YYYY-MM-DD-product-name.html
```

Open the HTML file in a browser, take a screenshot, and embed the image in your email newsletter.

## Requirements

- [Augment Agent](https://www.augmentcode.com/) active in your editor
- A modern browser for viewing generated posters
- No dependencies, no build tools, no scripts -- the AI is the only engine
