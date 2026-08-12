# Design System

Product design language for Slack Emoji Creator. This document defines tokens, color, type, space, motion, and product language. It does not define implementation or stylesheets.

Use these token names in UI work. Do not invent parallel values.

---

## Direction

**Name:** Sticker Studio  
**Feel:** Fast, playful, and clear—like a small workshop for reactions, not a dashboard and not a meme dump.

The product should feel:

- **Character-led** — the emoji is the hero; chrome stays quiet
- **Choice-simple** — Character → Situation → Target reads as one path
- **Slack-ready** — the end state feels like something you can drop into chat today

Avoid: corporate purple, neon glow stacks, dense admin layouts, and “AI product” gradients.

---

## Product language

### Vocabulary

Use these words consistently in UI and docs:

| Term | Meaning | Use |
| --- | --- | --- |
| **Character** | The figure or persona in the emoji | Selection step 1 |
| **Situation** | The pose, prop, or moment applied to the character | Selection step 2 |
| **Target** | What the emoji is about or aimed at | Selection step 3 |
| **Generator** | A character- or situation-focused way to make an emoji | Library / catalog |
| **Emoji** | The finished Slack-ready image | Output |
| **Make** | Primary action to produce the emoji | Primary CTA |

Prefer **Make emoji** over “Generate,” “Render,” or “Create asset.”  
Prefer **Add to Slack** only when the next step is literally taking the image into Slack.

### Voice

- Short, direct, a little wry
- Chat-native, not corporate
- Celebrate the character and situation, not the tooling

**Do say:** “Pick a character. Set the moment. Make the emoji.”  
**Don’t say:** “Configure your generation parameters to synthesize an output asset.”

### Tone by moment

| Moment | Tone |
| --- | --- |
| Empty / first visit | Inviting, low pressure |
| Selecting | Crisp labels, almost no filler |
| Making | Confident and brief |
| Done | Satisfying, ready-to-use |
| Error | Plain and helpful—no blame, no jokes that obscure the fix |

### Microcopy patterns

- Step labels: **Character**, **Situation**, **Target**
- Primary CTA: **Make emoji**
- Secondary: **Try another**, **Download**, **Copy**
- Empty state: one line that points to the next choice
- Errors: what happened + what to do next

---

## Color

Palette is light-first. Color supports the emoji stage; it should not compete with the artwork.

### Brand & surfaces

| Token | Value | Role |
| --- | --- | --- |
| `color.ink` | `#12241F` | Primary text, strong icons |
| `color.ink.soft` | `#3A534B` | Secondary text |
| `color.ink.muted` | `#6B7F76` | Tertiary text, hints, captions |
| `color.paper` | `#F3F7F4` | Page background |
| `color.paper.raised` | `#FFFFFF` | Raised interactive surfaces only when needed |
| `color.mist` | `#E4EEE8` | Subtle bands, wells, inactive tracks |
| `color.line` | `#C9D7CF` | Dividers, outlines |

### Accents

| Token | Value | Role |
| --- | --- | --- |
| `color.signal` | `#C8F000` | Primary action, focus energy, “ready” |
| `color.signal.ink` | `#1A2E14` | Text/icons on `color.signal` |
| `color.coral` | `#FF5B45` | Emphasis, destructive, hot moments |
| `color.coral.ink` | `#3A0F0A` | Text/icons on `color.coral` |
| `color.aqua` | `#1FB8A8` | Links, progress, supportive highlights |
| `color.aqua.ink` | `#062E2A` | Text/icons on `color.aqua` |

### Semantic

| Token | Value | Role |
| --- | --- | --- |
| `color.success` | `#1F9D6C` | Success, completed make |
| `color.warning` | `#D9891F` | Caution |
| `color.danger` | `#D93A2E` | Blocking errors, destructive confirms |
| `color.info` | `#1F7A8C` | Neutral information |

### Overlay & focus

| Token | Value | Role |
| --- | --- | --- |
| `color.overlay` | `#12241F` at 48% opacity | Modal scrims |
| `color.focus` | `#C8F000` | Focus ring / keyboard focus |

### Color rules

- Default text is `color.ink` on `color.paper`
- Primary actions use `color.signal` with `color.signal.ink`
- Do not use accent colors as large page backgrounds
- Keep the emoji preview area visually calm so the image leads
- Never rely on color alone for state; pair with label or shape

---

## Typography

### Typefaces

| Token | Face | Role |
| --- | --- | --- |
| `font.display` | **Cabinet Grotesk** | Brand wordmark, hero titles, step titles |
| `font.body` | **Satoshi** | UI labels, body, forms, buttons |
| `font.mono` | **JetBrains Mono** | Rare technical strings only (IDs, filenames) |

Do not use Inter, Roboto, Arial, or system UI defaults as the design voice.

### Type scale

| Token | Size | Line height | Tracking | Use |
| --- | --- | --- | --- | --- |
| `type.display.lg` | 48 | 52 | -0.02em | Brand / rare hero |
| `type.display.md` | 32 | 36 | -0.02em | Page titles |
| `type.display.sm` | 24 | 28 | -0.01em | Section / step titles |
| `type.title` | 18 | 24 | -0.01em | Card titles, strong labels |
| `type.body` | 16 | 24 | 0 | Default UI copy |
| `type.body.sm` | 14 | 20 | 0 | Secondary copy, controls |
| `type.caption` | 12 | 16 | 0.01em | Hints, metadata |
| `type.label` | 13 | 16 | 0.02em | Uppercase-capable control labels |

### Type rules

- Brand name uses `font.display` and must outrank nearby headlines on first view
- Step names (Character / Situation / Target) use `type.display.sm` or `type.title`
- Body copy stays short; prefer one supporting sentence per section
- Avoid long uppercase runs except short `type.label` chips when necessary

---

## Space

Base unit: **4**.

| Token | Value | Typical use |
| --- | --- | --- |
| `space.1` | 4 | Hairline gaps |
| `space.2` | 8 | Icon-to-label |
| `space.3` | 12 | Compact control padding |
| `space.4` | 16 | Default control padding |
| `space.5` | 20 | Dense section gaps |
| `space.6` | 24 | Default section padding |
| `space.8` | 32 | Between major blocks |
| `space.10` | 40 | Stage breathing room |
| `space.12` | 48 | Hero / stage padding |
| `space.16` | 64 | Page-level separation |

### Layout rules

- One job per section
- First viewport: brand, one headline, one short line, one CTA group, one dominant emoji stage
- No stats strips, promo chips, or secondary marketing blocks in the hero
- Default: no cards. Use cards only when they wrap a real choice or control

---

## Radius

| Token | Value | Use |
| --- | --- | --- |
| `radius.none` | 0 | Sharp edges when intentional |
| `radius.sm` | 6 | Small controls |
| `radius.md` | 10 | Buttons, inputs |
| `radius.lg` | 16 | Panels, stage frame |
| `radius.xl` | 24 | Rare large wells |
| `radius.full` | 9999 | Avatars / circular affordances only |

Prefer `radius.md` and `radius.lg`. Avoid pill-heavy UI.

---

## Stroke & elevation

| Token | Value | Use |
| --- | --- | --- |
| `stroke.thin` | 1 | Default borders (`color.line`) |
| `stroke.thick` | 2 | Selected / active outline |
| `elev.0` | none | Flat by default |
| `elev.1` | soft 0 1 2 / 8% ink | Slight lift for active controls |
| `elev.2` | soft 0 8 24 / 12% ink | Modal / popover only |

Stay mostly flat. Elevation is for temporary layers, not decoration.

---

## Motion

Motion should create presence and hierarchy, not noise. Ship a few intentional moves.

| Token | Duration | Easing | Use |
| --- | --- | --- | --- |
| `motion.fast` | 120ms | standard | Hover, press |
| `motion.base` | 200ms | standard | Step changes, selection |
| `motion.slow` | 320ms | emphasize | Emoji reveal / “made” moment |
| `motion.stagger` | 40ms | — | Offset between sibling reveals |

### Motion rules

- Selection changes ease with `motion.base`
- The completed emoji enters with `motion.slow` (scale + fade, subtle)
- Respect reduced motion: replace motion with instant state changes
- No looping decorative animation in chrome

---

## Iconography & imagery

- Icons: simple, 1.5–2px optical stroke, aligned to `color.ink` / `color.ink.soft`
- Emoji artwork is the only full-bleed hero visual
- Do not place badges, stickers, or callout chips on top of the emoji stage
- Generator previews show the character or situation clearly at small size

---

## Interaction states

| State | Treatment |
| --- | --- |
| Default | Flat, `color.ink` on `color.paper` / `color.mist` |
| Hover | Slightly deeper surface or stronger stroke; keep motion on `motion.fast` |
| Focus | `color.focus` ring; never remove visible focus |
| Selected | `stroke.thick` + `color.signal` accent cue |
| Disabled | `color.ink.muted` on `color.mist`; no strong accent |
| Loading | Brief, labeled; prefer progress over skeleton theater |
| Error | `color.danger` plus plain text |

---

## Content density

- Prefer one primary action per view
- Keep supporting copy to a single short sentence
- Character, Situation, and Target should remain the only required decisions
- Library browsing can be denser; the make flow stays sparse

---

## Do / Don’t

**Do**

- Lead with the brand and the emoji stage
- Speak in Character / Situation / Target
- Use `color.signal` for the make action
- Keep chrome lighter than the artwork

**Don’t**

- Introduce new colors or typefaces without updating this document
- Use raw visual values in UI when a token exists
- Create new components without user approval
- Cover the emoji with labels, badges, or promo overlays
- Turn the first screen into a dashboard

---

## Token index (quick reference)

**Color:** `color.ink`, `color.ink.soft`, `color.ink.muted`, `color.paper`, `color.paper.raised`, `color.mist`, `color.line`, `color.signal`, `color.signal.ink`, `color.coral`, `color.coral.ink`, `color.aqua`, `color.aqua.ink`, `color.success`, `color.warning`, `color.danger`, `color.info`, `color.overlay`, `color.focus`

**Font:** `font.display`, `font.body`, `font.mono`

**Type:** `type.display.lg`, `type.display.md`, `type.display.sm`, `type.title`, `type.body`, `type.body.sm`, `type.caption`, `type.label`

**Space:** `space.1` … `space.16` (4-based scale)

**Radius:** `radius.none`, `radius.sm`, `radius.md`, `radius.lg`, `radius.xl`, `radius.full`

**Stroke / elev:** `stroke.thin`, `stroke.thick`, `elev.0`, `elev.1`, `elev.2`

**Motion:** `motion.fast`, `motion.base`, `motion.slow`, `motion.stagger`
