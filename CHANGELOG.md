# Changelog

Versions are marked in `index.html` (`VERSION`), shown next to the logo in the editor,
and tagged in git.

## 1.2 — 2026-09-08

A pass over the editor's own interface. The language and everything the renderer does
are untouched.

- **The toolbar is grouped.** `New` `Open` `Save` now sit next to the file they act on,
  on the left beside the logo. On the right there is a compact `?` for the syntax panel
  and one **Export** menu holding **PowerPoint (.pptx)** and **PDF (.pdf)** — the same
  two exports as before, at the same level.
- **`Follow caret` moved into the preview's own header**, where it belongs, and became a
  switch whose on/off state reads at a glance.
- **The image strip is a compact asset bar.** `Images`, the folder chip, `+ Add`, then
  the pictures. The permanent line of instructions is gone; what you can do with images
  is written in the syntax panel instead. The strip still stays put when it is empty —
  it stands for the deck's images, not for a picker.
- **The file state says what it means.** With no file open the chip reads
  **Browser draft** rather than sounding like a warning; with a file open it reads
  `Saving…` and then `Saved`.
- **The preview canvas sits a shade lighter than the slides**, so a black slide keeps a
  clear edge, with a hairline and a restrained shadow instead of a heavy border.
- **The `1 · cover` labels above each slide are gone.** Only the slide number is left —
  which scale the renderer inferred was never the reader's business.

## 1.1 — 2026-09-07

- **`~` works on `@bg`.** A background photo takes a zoom and focal point like any other
  image: `@bg !backdrop.jpg ~ 1.6 40 30`. Double-clicking the background — anywhere no
  element covers it — opens the same crop editor, and applying it rewrites the `@bg`
  line. The crop carries through to the PDF and to the PPTX `srcRect`.
- The sample deck's closing slide no longer repeats `Thank you` in its footer; the
  footer is just the `Made with PpTex` line.

## 1.0 — 2026-09-07

First numbered release. Everything the language and the reference renderer do today:

- **Language.** `---` slides, `^` `#` `##` and body text, numbered lists, `!` images
  with `|` captions and `~` zoom / focal point, `[ ]` groups, `\` line breaks, and the
  `@bg` / `@footer` slide modifiers. Layout is whitespace only — newline places across,
  blank line places down — and the same rule holds at every depth. There are no styling
  directives; the renderer decides type scale, colour and spacing.
- **Editor.** Live preview that follows the caret, click-to-jump between preview and
  source, double-click crop editing, drag-and-drop images, and direct file editing with
  autosave (Chromium).
- **Image folder.** A deck's images can live in a folder beside it; the pairing is
  remembered so reopening a deck restores its pictures.
- **Export.** PDF through the print dialog and PPTX written by hand as OOXML — real
  text boxes and pictures measured from the same DOM the preview uses.

Changed in this release:

- A slide holding only modifiers now renders: `@footer` on its own gives an empty slide
  with the footer in place, instead of being dropped.
- The sample deck ends with a `Made with PpTex <version>` footer, and the version is
  shown next to the logo.
