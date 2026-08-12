# PRD: Emoji Maker Backbone

**Status:** Draft (v2)  
**Date:** 2026-08-12  
**Scope:** Product backbone and architecture only. Concrete generator art/copy is out of scope; the generator **file contract** is in scope.

---

## Problem

People who want custom Slack emojis for a specific moment usually have to hunt for images, crop them, composite them by hand, and rename the file to something Slack will accept. That is slow, inconsistent, and hard to repeat across characters and situations.

Teams that already share a culture of characters (for example Pepe or Bufo) lack a single, simple place to:

1. Pick a known **character**
2. Pick a matching **situation**
3. Name the **thing** the emoji is about
4. Get a Slack-ready image back

Without a shared backbone—registry, generator definitions, composition pipeline, and download path—each new generator would reinvent the same flow, and quality would diverge.

---

## Solution

Ship a single-page maker with one form, a JSON registry of generators, per-generator JSON definitions, and one composition pipeline.

### Experience

- One line of inputs:
  - **Character** — dropdown, derived from the registry
  - **Situation** — dropdown, situations available for the selected character
  - **Thing** — free-text field (the subject placed into the composition)
- Primary action: a **Make emoji** button on the form (composition does not run until clicked)
- After a successful make:
  1. Resolve the selected generator (character + situation)
  2. Load that generator’s **base** image from its definition
  3. Find a **royalty-free, transparent-background** image for the thing
  4. Place the thing into the base using the generator’s **x, y, width, height** placement
  5. Render a **64×64 PNG** with transparent background on a **canvas**
  6. Offer **download** with a Slack-compatible filename (descriptive, short, kebab-case)

### Architecture (conceptual)

| Piece | Responsibility |
| --- | --- |
| **Registry (JSON)** | Lists available generators, organized by **character** then **situation** |
| **Generator definition (JSON)** | Per generator: base image URL(s) and thing placement (`x`, `y`, `w`, `h`) |
| **Form** | Character, situation, thing + **Make emoji** CTA; situations depend on character |
| **Thing image source** | Locates a royalty-free transparent image matching the thing |
| **Composer** | Draws thing into the placement rect on the base; outputs 64×64 transparent PNG (M2: masks + top layers) |
| **Canvas preview** | Shows the composed emoji |
| **Download** | Exports the canvas PNG with a Slack-compatible name |

Individual generator content (which characters, art, copy) is defined outside this PRD. This PRD owns the **contracts** those generators must follow.

### Data contracts (normative intent)

**Registry JSON** — catalog only:

- Top level groups by **character**
- Under each character, lists available **situations**
- Each situation points at its **generator definition** (path or id)

**Generator JSON** — one character–situation pair:

- URL for the **base** image
- Placement for the thing: **x**, **y**, **w**, **h** (coordinates/size on the base canvas before final resize to 64×64, unless specified otherwise)
- **M2:** optional mask URL and top-layer URL

Exact field names can be finalized in the implementation plan; the responsibilities above are required.

### Success criteria

- User fills character, situation, and thing, clicks **Make emoji**, and gets a 64×64 transparent PNG preview plus download
- New generators are added by updating registry JSON + adding a generator JSON—no form redesign
- Situation choices always stay valid for the selected character

---

## Milestones

### M1 — Backbone (MVP)

End-to-end path with placement-rect composition (no masks/top layers).

**Includes**

- One-line form: character, situation, thing + **Make emoji** button
- Registry JSON → character/situation dropdowns
- Load generator JSON for the selection (base URL + `x/y/w/h`)
- Locate royalty-free transparent thing image
- Compose thing into placement rect on base; output **64×64 transparent PNG**
- Canvas preview + download with Slack-compatible kebab-case filename
- Sample registry + one sample generator definition to prove the pipeline

**Exit criteria**

- [ ] Character list comes from registry JSON
- [ ] Situation list filters by selected character
- [ ] Changing character updates situations to a valid set
- [ ] **Make emoji** is required; no compose on field change alone
- [ ] Incomplete form (missing character, situation, or thing) does not compose; button stays disabled or errors clearly
- [ ] Successful make shows 64×64 transparent PNG on canvas
- [ ] Download is PNG; filename is short, descriptive, kebab-case, Slack-usable
- [ ] Sample registry + generator JSON works end to end

### M2 — Layered composition

**Includes**

- Optional **mask** image URL on the generator definition
- Optional **top layer** image URL on the generator definition
- Thing cut/fit via mask; top layer drawn above the thing

**Exit criteria**

- [ ] Generator JSON may declare mask and/or top-layer URLs
- [ ] Thing is constrained by mask when present
- [ ] Top layer renders above the thing when present
- [ ] Generators without mask/top layer still behave as in M1

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
| F1 | The UI is a single form on one line with Character (dropdown), Situation (dropdown), Thing (text), and a **Make emoji** button. |
| F2 | Character options are derived from the registry JSON. |
| F3 | Situation options are derived from the registry JSON and limited to the selected character. |
| F4 | Changing character refreshes situation options; an invalid prior situation is cleared or replaced with a valid default. |
| F5 | Thing is free text entered by the user. Product language uses **Thing** (not Target). |
| F6 | Composition runs only when the user activates **Make emoji**, and only if character, situation, and thing are present. |
| F7 | The selected character + situation resolves to one generator definition via the registry. |
| F8 | Each generator is defined by a JSON file that includes a base image URL and thing placement `x`, `y`, `w`, `h`. |
| F9 | The system obtains a royalty-free image of the thing with a transparent background. |
| F10 | The composer places the thing image into the generator’s placement rectangle on the base. |
| F11 | Output is a **PNG** with **transparent background** at **64×64** pixels, rendered to a canvas for preview. |
| F12 | The user can download the previewed PNG. |
| F13 | Download filenames are descriptive, short, kebab-cased, and Slack-compatible. |
| F14 | The registry is a JSON file listing available generators, structured by character then situation. |
| F15 | Concrete generator content is out of scope; sample registry/generator JSON is allowed to prove M1. |
| F16 | **M2:** Generator JSON may include a mask image URL used to cut/fit the thing. |
| F17 | **M2:** Generator JSON may include a top-layer image URL drawn above the thing. |

### Non-functional

| ID | Requirement |
| --- | --- |
| N1 | UI follows `/docs/design` (Sticker Studio). Copy uses Character / Situation / Thing / Make emoji. |
| N2 | After **Make emoji**, preview should feel interactive for a single emoji (exact latency budget still TBD). |
| N3 | Thing images must be royalty-free and transparent-background; exact license allow-list still TBD. |
| N4 | Final asset is always 64×64 PNG with transparency. |
| N5 | Failures (missing generator, bad asset URL, no thing image, compose error) show plain, actionable feedback—no silent blank canvas. |

### Decisions locked

| Topic | Decision |
| --- | --- |
| Naming | **Thing** |
| Compose trigger | **Make emoji** button |
| Generator definition | JSON with base URL + `x/y/w/h` placement |
| Registry | JSON listing generators by character → situation |
| Output | 64×64 PNG, transparent background |

### Still open

| Topic | Why it matters |
| --- | --- |
| Thing image provider / API | Blocks F9 implementation |
| No-image / error behavior details | Blocks N5 acceptance tests |
| Filename pattern | Blocks F13 acceptance tests |
| Placement coordinate space | Base native pixels vs already-64 space |
| Latency budget | Softens N2 |

---

## Out of scope

- Designing or shipping specific full generators beyond sample data
- Slack API upload / workspace installation
- User accounts, saved emojis, or sharing
- Mobile-native apps
- Admin CMS (JSON files are the source of truth for M1/M2)

---

## Glossary

| Term | Meaning in this PRD |
| --- | --- |
| **Character** | Selectable persona/figure; top level in the registry |
| **Situation** | Pose/scenario under a character; selects a generator |
| **Thing** | User-entered subject composited into the base |
| **Registry** | JSON catalog of generators by character → situation |
| **Generator definition** | JSON for one character–situation: base URL + placement (+ M2 layers) |
| **Base** | Preset artwork for the generator |
| **Placement** | `x`, `y`, `w`, `h` where the thing is drawn on the base |
| **Mask** | Image used to cut/fit the thing (M2) |
| **Top layer** | Image drawn above the thing (M2) |
