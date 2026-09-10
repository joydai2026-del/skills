---
name: html-confirm
description: >-
  Produces an openable HTML page when the owner has to LOOK at something to decide or is reacting to a report. Use HTML for images, designs, storyboards, covers, renders, approving a deliverable, or reacting to a report or audit. Use plain chat for a few text choices, scoping questions, yes/no gates, "should I proceed", "which mailbox", "A or B". Use when: "confirm", "which one", "approve", "pick one", "review these", "should I proceed", "make a confirm page".
---

# html-confirm, HTML when there is something to SEE, chat when there is not

> **The narrow rule, not the blanket one.** HTML is for reports and for anything the reader must
> LOOK at to decide. Ordinary text choices belong in chat, including the app's own option cards.
> An earlier blanket rule sent every confirmation point to an HTML page; it is retired.

## The line to draw

| Situation | Surface |
|---|---|
| The decision-maker must look at an image, design, storyboard, render, layout, cover, or any generated artifact to decide | **HTML page**, opened locally |
| The decision-maker is reacting to a report, audit, comparison, options-with-tradeoffs page, or status dashboard | **HTML page** (this is the house rule's territory anyway) |
| The decision needs per-item notes across many rows, or the answer may come across several sittings | **HTML page** with the review layer |
| Plain choice among a few text options | **chat**, option cards are fine |
| Session-start scoping, "which mailbox", "should I proceed", yes/no gates | **chat** |
| One-line clarification | **chat**, plain text |

The mechanical test, when unsure: **can the thing being decided fit inside a chat option
card?** If it is a picture, a page, or sixteen variants, it cannot, so build the page. If it is
"A or B", it can, so just ask.

Do not build a page for a small choice. That was the failure mode the owner named: everything became a
page, which is friction, not service.

## Still true regardless of surface (the capture rule)

Before asking anything, answer: **when the decision-maker is done answering, how do I actually get the answers
back?** This survived the narrowing because it was a separate, real failure:

- A reviewer filled in a design review layer, clicked export, and the markdown never
  arrived. The template now also writes the export into a selectable, auto-selected `textarea`
  so a blocked download still yields the text.
- Never hand a decision to a sandboxed embed. The sandboxed iframe breaks
  `localStorage`, downloads, and the clipboard, so a whole round of per-item picks was lost.
  Give the decision-maker a local file and `open` it.

## What to produce (when HTML is the right surface)

ONE self-contained `.html` file that:
1. **States the decision up top** in plain language plus how to answer ("reply A / B / C, or tell
   me what to tweak"). Lead with the ask.
2. **Shows the actual thing being decided.** This is the whole reason the page exists. Embed the
   images, render options side by side, show before and after, display the artifact under review.
3. **Lists each option** with a one-line tradeoff and **marks your recommendation**.
4. **Terse context** that changes the choice: cost, risk, what is blocked.

Format per the house rule: inline `<style>`, real content only, UTF-8 and CJK-safe, no React/Vue/Tailwind,
**no em dashes**. Reference local images by relative path, or base64-embed when the page must be
portable.

For a REPORT-type page, keep your commenting layer in its own template file and append that
file to the page rather than re-authoring the markup each time.

## Where to save and how to open

- Save where the topic lives, per the house rule: `<project>/docs/<topic>-confirm.html`.
- **Auto-open on your primary desktop:** `open "<path>"`. Skip auto-open on a secondary machine (check its hostname against your own naming convention) or headless/agent runs.
- Print one line: `Opened: <path>`.
- Then stop and wait. Do not also ask the same question in chat.
- **If it cannot be opened**, do not re-send the same page. Put the decision in chat as a
  short lettered list that can be answered in three lines, and ask whether a different
  format (PDF, plain text) for that device.

## Relationship to other skills

- Extends your own HTML-first artifact convention; this is its *decision/approval* application.
- Heavy multi-page reports belong in a dedicated long-report workflow.
- Research and comparison artifacts follow the house rule directly, no skill needed.

## Minimal skeleton

```html
<!doctype html><html><head><meta charset="utf-8"><style>
 /* clean, inline, theme-aware; recommendation card highlighted */
</style></head><body>
 <h1>&lt;Decision&gt;</h1>
 <p>Reply <b>A</b>, <b>B</b>, or <b>C</b>, or tell me what to tweak.</p>
 <!-- option cards, recommendation marked -->
 <!-- the visuals being decided: <img src="../path/to/art.png"> -->
</body></html>
```
