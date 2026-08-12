# PRD: Emoji Maker Backbone

**Status:** Draft (v6)  
**Date:** 2026-08-12  
**Scope:** Product backbone and architecture only. Concrete generator art/copy is out of scope; the generator **file contract** is in scope.

---

## Problem

People who want custom Slack emojis for a specific moment usually have to hunt for images, crop them, composite them by hand, and rename the file to something Slack will accept. That is slow, inconsistent, and hard to repeat across characters and situations.

Teams that already share a culture of characters (for example Pepe or Bufo) lack a single, simple place to:

1. Pick a known **character**
2. Pick a matching **situation**
3. Name the **thing** the emoji is about
4. Provide (or later, find) an image of that thing
5. Get a Slack-ready image back

Without a shared backbone—registry, generator definitions, composition pipeline, and download path—each new generator would reinvent the same flow, and quality would diverge.

---

## Solution

Ship a single-page maker with one form, a JSON registry of generators, per-generator JSON definitions, and one composition pipeline.

### Experience

- Form inputs:
  - **Character** — dropdown, derived from the registry
  - **Situation** — dropdown, situations available for the selected character
  - **Thing** — free-text field (used in naming and labeling; the subject of the emoji)
  - **Thing image** — **drag and drop** (M1/M2); user supplies the image
- Character, situation, and thing text stay on **one line**; thing image uses a drag-and-drop affordance
- Primary action: a **Make emoji** button (composition does not run until clicked)
- After a successful make:
  1. Resolve the selected generator (character + situation)
  2. Load that generator’s **base** image from its definition
  3. Use the user-provided thing image (M1/M2) or a searched image (M3)
  4. Place the thing into the base using the generator’s relative **x, y, w, h** placement
  5. Render a **64×64 PNG** with transparent background on a **canvas**
  6. Offer **download** named `{character}-{situation}-{thing}` (kebab-case; see F13)

### Thing image drop (M1+)

- Accept common image types including **JPEG**, PNG, and other browser-decodable formats used for drop
- After drop, check whether the image has an **alpha channel**
- If there is **no alpha channel** (typical for JPEG), show a **note** that the image lacks transparency
- The note is advisory only — it **must not block** Make emoji or download

### Architecture (conceptual)

| Piece | Responsibility |
| --- | --- |
| **Registry (JSON)** | Lists available generators, organized by **character** then **situation** |
| **Generator definition (JSON)** | Per generator: base image URL(s) and relative thing placement (`x`, `y`, `w`, `h`) |
| **Form** | Character, situation, thing text, thing image drop zone, **Make emoji** CTA |
| **Thing image (M1/M2)** | User-provided via drag and drop (incl. JPEG); alpha-channel check with non-blocking note |
| **Thing image search (M3)** | Locates a royalty-free transparent image matching the thing text |
| **Composer** | Maps relative placement onto the base’s pixel size, draws the thing, then outputs 64×64 transparent PNG (M2: masks + top layers) |
| **Canvas preview** | Shows the composed emoji |
| **Download** | Exports the canvas PNG as `{character}-{situation}-{thing}.png` |

Individual generator content (which characters, art, copy) is defined outside this PRD. This PRD owns the **contracts** those generators must follow.

### Data contracts (normative intent)

**Registry JSON** — catalog only:

- Top level groups by **character**
- Under each character, lists available **situations**
- Each situation points at its **generator definition** (path or id)

**Generator JSON** — one character–situation pair:

- URL for the **base** image
- Placement for the thing as **relative values** (so bases of any pixel size work):
  - **x**, **y** — position from the top-left, each in **0–1** relative to the base image
  - **w**, **h** — size in **0–1**, each relative to the base image’s **width** (not height)
- Pixel rect at compose time:  
  `px = x * baseWidth`, `py = y * baseHeight`, `pw = w * baseWidth`, `ph = h * baseWidth`
- Then the full composition is scaled to **64×64** for output
- **M2:** optional mask URL and top-layer URL

Exact field names can be finalized in the implementation plan; the responsibilities above are required.

### Success criteria

- User selects character and situation, names the thing, drops a thing image, clicks **Make emoji**, and gets a 64×64 transparent PNG preview plus download
- Non-alpha thing images (including JPEG) show a lack-of-transparency note but still compose
- New generators are added by updating registry JSON + adding a generator JSON—no form redesign
- Situation choices always stay valid for the selected character

---

## Milestones

### M1 — Backbone (MVP)

End-to-end path with user-provided thing image and placement-rect composition (no masks/top layers, no image search).

**Includes**

- Form: character, situation, thing text (one line) + thing image **drag and drop** + **Make emoji**
- Registry JSON → character/situation dropdowns
- Load generator JSON for the selection (base URL + relative `x/y/w/h`)
- User provides the thing image via drag and drop (JPEG allowed)
- Alpha-channel check; note if missing (does not block)—JPEG always notifies
- Compose thing into the resolved pixel placement on base; output **64×64 transparent PNG**
- Canvas preview + download as `{character}-{situation}-{thing}.png`
- Sample registry + one sample generator definition to prove the pipeline

**Exit criteria**

- [ ] Character list comes from registry JSON
- [ ] Situation list filters by selected character
- [ ] Changing character updates situations to a valid set
- [ ] User can drag and drop a thing image, including JPEG
- [ ] Image without an alpha channel shows a lack-of-transparency note and still allows Make emoji
- [ ] **Make emoji** requires character, situation, thing text, and thing image
- [ ] **Make emoji** is required; no compose on field change alone
- [ ] Successful make shows 64×64 transparent PNG on canvas
- [ ] Download is PNG named `{character}-{situation}-{thing}` in kebab-case (e.g. `bufo-offers-you-ice-cream.png`)
- [ ] Sample registry + generator JSON works end to end

### M2 — Layered composition

**Includes**

- Optional **mask** image URL on the generator definition
- Optional **top layer** image URL on the generator definition
- Thing cut/fit via mask; top layer drawn above the thing
- Still uses user-provided thing image (drag and drop)

**Exit criteria**

- [ ] Generator JSON may declare mask and/or top-layer URLs
- [ ] Thing is constrained by mask when present
- [ ] Top layer renders above the thing when present
- [ ] Generators without mask/top layer still behave as in M1

### M3 — Thing image search

**Includes**

- Search for a royalty-free, transparent-background image from the thing text
- Integrate search into the make flow as an alternative (or complement) to drag and drop
- Provider, license bar, and fallback behavior defined in a follow-up to this PRD or an M3 addendum before build

**Exit criteria**

- [ ] User can obtain a thing image from search using the thing text
- [ ] Search results prefer transparent, royalty-free images
- [ ] Failure to find an image is explained clearly; user can still fall back to drag and drop

### Later (out of this PRD)

- Shipping a full catalog of real generators
- Slack API upload / workspace installation
- Accounts, history, favorites
- Admin CMS for registry/generators

---

## Requirements

### Functional

| ID | Requirement |
| --- | --- |
| F1 | The UI is a single form with Character (dropdown), Situation (dropdown), and Thing (text) on one line; a thing-image **drag-and-drop** zone; and a **Make emoji** button. |
| F2 | Character options are derived from the registry JSON. |
| F3 | Situation options are derived from the registry JSON and limited to the selected character. |
| F4 | Changing character refreshes situation options; an invalid prior situation is cleared or replaced with a valid default. |
| F5 | Thing is free text entered by the user. Product language uses **Thing** (not Target). |
| F6 | **M1/M2:** The user provides the thing image via drag and drop. **JPEG is allowed**, along with other common image types (e.g. PNG). |
| F7 | After a thing image is provided, check for an **alpha channel**. If none is present, show a note that the image lacks transparency; do **not** block Make emoji or download. |
| F8 | Composition runs only when the user activates **Make emoji**, and only if character, situation, thing text, and thing image are present. |
| F9 | The selected character + situation resolves to one generator definition via the registry. |
| F10 | Each generator is defined by a JSON file that includes a base image URL and relative thing placement `x`, `y`, `w`, `h` (see F11). |
| F11 | Placement is resolution-independent: `x`/`y` are 0–1 from the base top-left; `w`/`h` are 0–1 relative to the base **width**. Composer converts to pixels on the loaded base, draws the thing, then scales the result to 64×64. |
| F12 | Output is a **PNG** with **transparent background** at **64×64** pixels, rendered to a canvas for preview. |
| F13 | The user can download the previewed PNG. |
| F14 | Download filename is `{character}-{situation}-{thing}` in kebab-case, plus `.png`. Multi-word parts are hyphenated. Example: character `bufo`, situation `offers you`, thing `ice cream` → `bufo-offers-you-ice-cream.png`. |
| F15 | The registry is a JSON file listing available generators, structured by character then situation. |
| F16 | Concrete generator content is out of scope; sample registry/generator JSON is allowed to prove M1. |
| F17 | **M2:** Generator JSON may include a mask image URL used to cut/fit the thing. |
| F18 | **M2:** Generator JSON may include a top-layer image URL drawn above the thing. |
| F19 | **M3:** The system can search for a royalty-free transparent image matching the thing text; drag and drop remains available as fallback. |

### Non-functional

| ID | Requirement |
| --- | --- |
| N1 | UI follows `/docs/design` (Sticker Studio). Copy uses Character / Situation / Thing / Make emoji. |
| N2 | After **Make emoji**, preview should feel interactive for a single emoji (exact latency budget still TBD). |
| N3 | Final asset is always 64×64 PNG with transparency. |
| N4 | Failures (missing generator, bad asset URL, missing thing image, compose error) show plain, actionable feedback—no silent blank canvas. |
| N5 | Lack-of-transparency note is plain and helpful; it must not read as a hard error. |
| N6 | **M3:** Searched thing images must meet a royalty-free license bar defined before M3 build. |

### Decisions locked

| Topic | Decision |
| --- | --- |
| Naming | **Thing** |
| Compose trigger | **Make emoji** button |
| Thing image (M1/M2) | User provides via **drag and drop**; **JPEG allowed** |
| Transparency | Check for **alpha channel** only; if missing, **note** lack of transparency; **do not block** |
| Thing image search | **M3** (not M1) |
| Generator definition | JSON with base URL + relative `x/y/w/h` placement |
| Placement | `x`,`y` ∈ [0,1] from top-left; `w`,`h` ∈ [0,1] of base **width**; supports any base size |
| Registry | JSON listing generators by character → situation |
| Output | 64×64 PNG, transparent background |
| Filename | `{character}-{situation}-{thing}` kebab-case + `.png` (e.g. `bufo-offers-you-ice-cream.png`) |

### Still open

| Topic | Why it matters |
| --- | --- |
| Latency budget | Softens N2 |
| M3 search provider / license | Needed before M3 implementation, not before M1 |

---

## Out of scope

- Designing or shipping specific full generators beyond sample data
- Slack API upload / workspace installation
- User accounts, saved emojis, or sharing
- Mobile-native apps
- Admin CMS (JSON files are the source of truth for M1/M2)
- Automatic thing-image search before M3

---

## Glossary

| Term | Meaning in this PRD |
| --- | --- |
| **Character** | Selectable persona/figure; top level in the registry |
| **Situation** | Pose/scenario under a character; selects a generator |
| **Thing** | User-entered subject name (text) used for labeling and filename |
| **Thing image** | Image composited into the base (user drop in M1/M2; search in M3) |
| **Registry** | JSON catalog of generators by character → situation |
| **Generator definition** | JSON for one character–situation: base URL + placement (+ M2 layers) |
| **Base** | Preset artwork for the generator |
| **Placement** | Relative rect: `x`/`y` 0–1 from top-left; `w`/`h` 0–1 of base width |
| **Mask** | Image used to cut/fit the thing (M2) |
| **Top layer** | Image drawn above the thing (M2) |
