# PRD: Emoji Maker Backbone

**Status:** Draft  
**Date:** 2026-08-12  
**Scope:** Product backbone and architecture only. Individual generators are out of scope.

---

## Problem

People who want custom Slack emojis for a specific moment usually have to hunt for images, crop them, composite them by hand, and rename the file to something Slack will accept. That is slow, inconsistent, and hard to repeat across characters and situations.

Teams that already share a culture of characters (for example Pepe or Bufo) lack a single, simple place to:

1. Pick a known **character**
2. Pick a matching **situation**
3. Name the **thing** the emoji is about
4. Get a Slack-ready image back

Without a shared backbone—registry, composition pipeline, and download path—each new generator would reinvent the same flow, and quality would diverge.

---

## Solution

Ship a single-page maker with one form and one composition pipeline.

### Experience

- One line of inputs:
  - **Character** — dropdown, from a registry
  - **Situation** — dropdown, from the registry, filtered by the selected character
  - **Thing** — free-text field (the subject placed into the composition)
- When the form is complete, the system:
  1. Resolves the selected character–situation **base** (a preset image for that pairing)
  2. Finds a **royalty-free, transparent-background** image for the thing
  3. Composites thing + base into a Slack-ready emoji
  4. Renders the result on a **canvas**
  5. Offers **download** with a Slack-compatible filename (descriptive, short, kebab-case)

### Architecture (conceptual)

| Piece | Responsibility |
| --- | --- |
| **Registry** | Source of truth for characters, their situations, and links to base assets |
| **Form** | Collects character, situation, and thing; situations update when character changes |
| **Thing image source** | Locates a royalty-free transparent image matching the thing |
| **Composer** | Builds the final image from base + thing (later: masks and top layers) |
| **Canvas preview** | Shows the composed emoji |
| **Download** | Exports the canvas image with a Slack-compatible name |

Generators are defined separately and plug into this backbone. This PRD does not specify any individual generator’s art or copy.

### Success criteria

- A user can complete the one-line form and receive a downloadable Slack-ready emoji without leaving the page
- Adding a new character/situation later means registering it—not rebuilding the form or pipeline
- Situation choices always stay valid for the selected character

---

## Milestones

### M1 — Backbone (MVP)

Deliver the end-to-end path with flat composition (base + thing, no masks/top layers yet).

**Includes**

- One-line form: character, situation, thing
- Registry-backed character and situation lists; situation options depend on character
- Resolve character–situation base image
- Locate royalty-free transparent thing image
- Compose base + thing
- Canvas preview + download with Slack-compatible kebab-case filename

**Exit criteria**

- [ ] User can select character → see filtered situations → enter thing → see preview
- [ ] Changing character resets/updates situation to a valid option for that character
- [ ] Incomplete forms do not produce a download
- [ ] Download filename is short, descriptive, kebab-case, and Slack-usable
- [ ] At least one registered character–situation pair works end to end (fixture / sample data allowed)
- [ ] No individual “full” generator catalog required beyond what the registry needs to prove the pipeline

### M2 — Layered composition

Improve fit and finish for bases that need it.

**Includes**

- Optional **mask** images on a base to cut/fit the thing image
- Optional **top layer** images on a base for shading / overlays
- Masks and top layers are images associated with the character–situation base

**Exit criteria**

- [ ] A base can declare mask and/or top-layer assets
- [ ] Thing image is constrained by the mask when present
- [ ] Top layer renders above the thing when present
- [ ] Bases without mask/top layer still behave as in M1

### Later (out of this PRD)

- Individual generator definitions and art packs
- Upload-to-Slack integrations
- Accounts, history, favorites

---

## Requirements

### Functional

| ID | Requirement |
| --- | --- |
| F1 | The UI is a single form on one line with three inputs: Character (dropdown), Situation (dropdown), Thing (text). |
| F2 | Character options come from the registry. |
| F3 | Situation options come from the registry and are limited to the selected character. |
| F4 | Changing character refreshes situation options; an invalid prior situation is cleared or replaced with a valid default. |
| F5 | Thing is free text entered by the user. |
| F6 | Composition runs only when character, situation, and thing are all present. |
| F7 | The system resolves a preset base image for the character–situation pair. |
| F8 | The system obtains a royalty-free image of the thing with a transparent background. |
| F9 | The composer combines base and thing into one emoji image (M1: simple overlay/placement). |
| F10 | The result is rendered to a canvas element for preview. |
| F11 | The user can download the previewed image. |
| F12 | Download filenames are descriptive, short, kebab-cased, and Slack-compatible. |
| F13 | Character–situation definitions live in a registry so new pairs can be added without redesigning the form. |
| F14 | Individual generators are not defined in this PRD; the backbone must allow them to be added later as registry entries / packs. |
| F15 | **M2:** A base may include a mask image used to cut/fit the thing. |
| F16 | **M2:** A base may include a top-layer image drawn above the thing for shading or similar. |

### Non-functional

| ID | Requirement |
| --- | --- |
| N1 | UI follows `/docs/design` (Sticker Studio tokens and language). Prefer product term **Thing** in this flow if confirmed; otherwise align with vision term **Target**. |
| N2 | Preview and download must complete in a time that feels interactive for a single emoji (exact budget TBD). |
| N3 | Thing images must be usable under a royalty-free license suitable for Slack emoji distribution (exact license bar TBD). |
| N4 | Output should be suitable as a Slack emoji (size/format expectations TBD). |
| N5 | Failures (missing base, no thing image found, compose error) must show plain, actionable feedback—no silent blank canvas. |

### Open decisions (block clarity)

See scoring notes and questions below; resolve before implementation plan.

---

## Out of scope

- Designing or shipping specific generators (Pepe hat, Bufo offering, etc.) beyond sample registry data
- Slack API upload / workspace installation
- User accounts, saved emojis, or sharing
- Mobile-native apps
- Admin CMS for the registry (file- or code-based registry is enough for M1)

---

## Glossary

| Term | Meaning in this PRD |
| --- | --- |
| **Character** | Selectable persona/figure from the registry |
| **Situation** | Pose/scenario available for a character |
| **Thing** | User-entered subject composited into the base *(naming vs “Target” TBD)* |
| **Base** | Preset artwork for a character–situation pair |
| **Mask** | Image used to cut/fit the thing (M2) |
| **Top layer** | Image drawn above the thing for shading/overlay (M2) |
| **Registry** | Catalog of characters, situations, and asset references |
| **Generator** | Separately defined character/situation pack; out of scope here |
