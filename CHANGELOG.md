# Changelog

Versions are marked in `index.html` (`VERSION`), shown next to the logo in the editor,
and tagged in git.

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
