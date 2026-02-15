# Watashi Colorizer

**AI-powered manga and webtoon colorization with cross-panel color consistency.**

[watashicolorizer.com](https://watashicolorizer.com)

---

## What It Does

Watashi Colorizer automatically converts black-and-white manga and webtoon pages into full color using Google Gemini's vision models. Unlike simple per-image colorization tools, it maintains color consistency across entire chapters through a multi-stage pipeline that understands page structure.

Upload your B&W pages, select a character palette, and get back colorized images at the original dimensions — ready to drop back into your reader or publishing workflow.

## Key Features

### Virtual Image Splitting
Webtoon pages often contain multiple scenes separated by black panel dividers. Watashi Colorizer detects these dividers using pure-black row scanning and splits each page into individual art bands (virtual images). Each band becomes an independent unit for batching, so a scene that starts at the bottom of one page and continues at the top of the next gets grouped into the same AI batch — producing consistent colors across page boundaries.

### Intelligent Batch Formation
Art bands are grouped into batches based on scene continuity, not arbitrary page counts. The system scores cross-image boundaries using row-based fully-black detection to determine where scenes break. Batches respect a maximum stitch ratio (total height / width) to ensure the AI receives high-quality input. Boundaries always fall at natural scene breaks — never mid-panel.

### Character Palette Enforcement
Define character color palettes with specific hex values for hair, eyes, skin, clothing, and accessories. These palettes are injected directly into the AI prompt so characters maintain their exact color identity across every panel and chapter. Palettes are reusable across projects.

### Context Learning
After colorization, the system can learn environment-specific colors (e.g., "hospital hallway: pale mint-green walls"). Learned context is stored per palette and injected into future prompts, so recurring locations stay consistent across sessions.

### Smart Segmentation
Panel dividers are detected by scanning each row for pure-black pixels (RGB sum < 15). Only rows with 95%+ truly-black pixels qualify as void, preventing dark art (shadows, cross-hatching) from being misidentified. Short gaps like speech bubbles on black backgrounds are filled using gap-analysis heuristics. The result: only actual art content reaches the AI, saving API costs while preserving artistic integrity.

### Original Dimension Output
Every colorized image is output at exactly the same width and height as the original input. Colorized files can be dropped directly back in as replacements with no resizing, cropping, or manual adjustments.

## How the Pipeline Works

```
Input Pages
    │
    ▼
┌──────────────────────┐
│  Black Row Scanning   │  Detect panel dividers (RGB sum < 15, 95%+ threshold)
│  + Gap Filling        │  Fill speech bubbles, handle edge cases
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│  Virtual Image Split  │  Split pages into individual art bands at black voids
│  + Void Extension     │  Extend bands into dark transition zones
│  + Band Padding       │  Add 15px context padding between bands
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│  Dynamic Batching     │  Group bands by scene continuity
│  + Boundary Scoring   │  Row-based fully-black detection at edges
│  + Ratio Constraint   │  Max stitch ratio 3.5 (height/width)
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│  Stitch + Colorize    │  Combine batch into single strip → send to Gemini
│  + Palette Injection  │  Character colors + learned context in prompt
│  + Slice Apart        │  Split colorized result back into individual bands
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│  Compose Final Image  │  Paste colorized bands onto original black canvas
│                       │  Output at original dimensions
└──────────────────────┘
```

## Tech Stack

| Layer | Technology |
|-------|-----------|
| AI Engine | Google Gemini (2.0 Flash, 2.5 Flash/Pro) |
| Backend | Node.js, Express 5, pg-boss (job queue) |
| Frontend | React 19, Vite, Tailwind CSS 4 |
| Database | PostgreSQL with Drizzle ORM |
| Storage | DigitalOcean Spaces (S3-compatible) |
| Image Processing | Sharp |
| Auth | Google OAuth + email/password with JWT |
| Payments | Stripe (credit packs + BYOK plans) |

## Pricing

| Plan | Price | Details |
|------|-------|---------|
| Free | $0 | 3 colorization credits to try |
| 50 Credits | $9.99 | ~50 images |
| 200 Credits | $29.99 | ~200 images |
| 500 Credits | $59.99 | ~500 images |
| BYOK Access | $19.99 | Bring your own Gemini API key — 1,000 images |
| BYOK Unlimited | $39.99 | Bring your own Gemini API key — unlimited |

## Comparison with Other Tools

| Feature | Watashi Colorizer | Generic AI Colorizers |
|---------|-------------------|----------------------|
| Cross-panel consistency | Yes — virtual image splitting + scene-aware batching | No — each image processed independently |
| Character palette enforcement | Yes — hex-level color control per character | No |
| Context learning | Yes — remembers environment colors across sessions | No |
| Panel divider handling | Smart detection preserves dividers, strips voids before AI | Often fills in or damages dividers |
| Output dimensions | Exact match to input | Often resized or cropped |
| Webtoon-optimized | Yes — designed for long vertical strips | Usually designed for single illustrations |
| Batch processing | Yes — full chapters at once | Usually single image |

## FAQ

**What makes this better than other colorization tools?**

Three things: (1) Virtual image splitting ensures scenes that span page boundaries get colorized together for consistent colors. (2) Character palettes and context learning maintain color identity across entire chapters. (3) Smart segmentation strips panel dividers and voids before AI processing, saving cost and preventing artifacts.

**How does virtual image splitting work?**

Each uploaded page is scanned row-by-row for pure-black panel dividers (>= 60px tall). The page is split into individual art bands at these dividers. Bands are then grouped across pages by scene continuity — if a scene starts at the bottom of page 5 and continues at the top of page 6, those bands land in the same AI batch.

**Does it work with manga (right-to-left) and webtoons (vertical scroll)?**

Yes. The pipeline is optimized for long vertical webtoon strips but works with any black-and-white comic format. Panel divider detection adapts to the layout.

**Can I use my own Gemini API key?**

Yes. BYOK (Bring Your Own Key) plans let you use your own Google Gemini API key. You pay Google directly for API usage and get either 1,000 images or unlimited access through Watashi Colorizer.

**Do output images match the original dimensions?**

Yes. Every colorized image is output at exactly the same width and height as the original. You can drop colorized files directly back into your manga reader or publishing workflow as replacements.

---

Built by [Watashi Games](https://watashicolorizer.com)
