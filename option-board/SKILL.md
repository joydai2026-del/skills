---
name: option-board
description: >-
  Turns a creative ask into an OPTION BOARD: N distinct generated options on one numbered board, a range with a recommendation. Use when: "option board", "give the client options", "element study", "variant menu", "design catalog options", "give me versions to pick from", "several directions to choose". For code or web-UI variants use a code-variant skill; your own still-generation skill owns image craft.
---

# Option Board (creative options for the client to pick)

The recurring move: one creative ask in, a curated board of genuinely different options out, the decision-maker picks, the winner becomes the locked master. Built from catalog, cover-design, and mockup-board work.

**Philosophy (locked):** you are producing options for someone else to choose from. Deliverables are ranges of distinct options that you screen and curate, and the decision-maker makes the final pick. Never frame the ask as "which one is final". Propose options WITH a recommendation and why; forcing a premature convergence is the bug.

Style: follow your own label conventions before drafting anything the decision-maker sees.

## 1. Scope the axes first

Decide what varies BEFORE generating. Options must differ on a BOLD axis, visible at a glance (setting, subject, composition, mood, material), not a subtle one. Two options that differ only in a small detail are the same option in different clothes: cut one (the novelty check). 6 to 10 options is a board; 3 is a choice; 40 is a dump.

## 2. Element-level vs whole-design

For a COMPLEX design (a package, a label, a catalog), do not generate whole compositions first. Use the 3-phase modular method (your own still-generation skill owns the generation craft; the method is summarized here for the board and pick context):

1. **Element study pages**: one page per element, ~6 variants each, on a plain gray background. Client picks per element. Cheap, parallel.
2. **Full combinations**: assemble the picked elements into complete compositions.
3. **Composite + lock**: pixel-precise placement, with any element the model renders unreliably composited deterministically instead of generated.

For a SIMPLE ask (a cover, a hero image, a look), go straight to whole-design options across the bold axes.

## 3. Generate and curate

- Production craft lives in your own generation skills: author the still prompts there and follow your own still-image rules (one master image for consistency, a two-pass textless-then-overlay build).
- Generate wide, curate HARD. The board carries only options that each prove a different point. Keep rejects in folders for recovery; never explain a rejection on the board.
- Generated assets pass your own image QA gate before they reach the board.

## 4. The board (one artifact)

One self-contained HTML page (or a contact-sheet PNG for a quick internal round):

- Numbered cards; numbering restarts per section unless the owner asks for global numbers. The pick happens BY NUMBER, so numbers must be unambiguous.
- `object-fit: contain` so no artwork is cropped; neutral padding for grid alignment.
- Clean formal labels, written in the audience's own language. NO internal words (rejected, QA, prompt, risk, tool).
- Group by what each option PROVES or by element, not by visual style.
- Local-image HTML does not render in an embedded preview panel: open via `file://` in a real browser, or base64-embed the images if the file will be shared.
- Auto-open on your primary desktop (`open <path>`); print the path in one line. Skip it on a secondary machine or a headless run.

## 5. Present

For each option: one line on what it is + what it optimizes for. Then a recommendation and WHY (one line). Then stop. You screen the range; the decision-maker picks. Do not ask for a final pick; do ask which options to drop if the board is over-full.

## 6. After the pick

- The chosen option becomes the LOCKED master reference for all derivatives (one master, derive all).
- Iterate ONLY from the approved base, in ONE pass per revision round (stacked iterative edits drift: go back to the approved base each time).
- Record the pick and the asset path with the project's own notes (a dated decision note), so the next session inherits the lock instead of re-asking.

## Validation loop (before handing over)

1. Open the board and LOOK (the house rule): every image renders, no broken paths, no cropped art.
2. Count check: 6-10 curated options (or ~6 variants per element page), not the raw dump.
3. Label scan: no internal words, labels in the audience's own language correct.
4. Distinctness pass: any two options that read as the same at a glance = cut or regenerate before showing.

## Siblings

- A code-repo design-variant skill: web/UI variants inside a code repo. Not this skill.
- Your own still-generation and prompt-authoring skills: how each option is actually made.
- Your own image QA gate: what a generated asset passes before it reaches the board.
- A report skill of your own: when the board ships as a PDF instead of HTML.
