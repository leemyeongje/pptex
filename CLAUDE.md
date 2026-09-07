# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

PpTex is a markup language for presentations plus its reference renderer/editor. The
whole app is `index.html` — one self-contained file, ~2100 lines, no build step, no
dependencies, no test suite. `README.md` is the language specification; keep it in sync
whenever syntax or renderer behaviour changes.

`legacy/` holds earlier prototypes (`deck-md*.html`) and is untracked — don't edit or
resurrect it.

## Running

```
python3 -m http.server 8000    # then http://localhost:8000/
```

Serve over `http://localhost` or HTTPS, never `file://`: the File System Access API
needs a secure context. Verification is manual — open the page, type in the editor,
check the preview, and for export changes actually run the PDF (print dialog) and PPTX
buttons. Chromium only for file/folder editing and for `corner-shape: squircle`.

Deployed as GitHub Pages from `main` (`.nojekyll` at the root).

## Pipeline

Source text flows one way and is rendered by exactly one function:

```
editor.value
  → parseDoc()      splitChunks (--- ) → parseSlide → layout tree {axis:'row'|'col', children}
  → inferScale()    'cover' | 'statement' | 'plain', plus s.bleed
  → renderSlide()   tree → DOM (.slide, 1280×720, styled by CSS custom properties)
  → three consumers:
       #preview      scaled with transform, live=true (click/drag/dblclick wired)
       #print-root   same DOM, live=false, laid out by @media print for PDF
       off-screen stage (section 9) measured with getBoundingClientRect → OOXML
```

**The export paths never re-implement layout.** PDF prints the same DOM through
`@media print`; PPTX renders slides off-screen at real 1280×720 size and converts the
*measured* geometry into PowerPoint shapes. If a slide looks wrong in an export, fix
the CSS or the renderer, not a parallel layout implementation.

The `<script>` is divided into numbered sections (`1. PARSE` … `9. PPTX EXPORT`);
find them with `grep -n "====" index.html`.

## Invariants worth knowing

- **Line provenance.** Every parsed cell carries `line` (its source line number) and
  every slide carries `from`/`to`. This drives caret↔preview sync (`syncFromCursor`,
  `jumpToLine`) and write-back: crops and dropped filenames are applied by rewriting
  one source line (`writeLine` → `imgLine`), never by mutating the tree. Any new
  preview-side edit must round-trip through the source the same way.
- **Layout is whitespace only.** Newline = horizontal row, blank line = vertical
  column, `[ ]` = group, trailing `\` = line break inside one text element. The rule is
  identical at every depth; `prune()` drops empty groups and collapses single-child ones.
- **The language has no styling directives.** No `@color`, `@size`, `@margin`,
  `@align` — the renderer decides. Scale is inferred (`inferScale`), and visual values
  live in CSS custom properties on `.slide` (`--tit`, `--body`, `--cap`, `--rowgap`,
  `--radius`, …) overridden by `.s-cover` / `.s-statement` / `.bleed`. Add styling by
  teaching the renderer, not by adding markup.
- **The caption slot is always reserved** (`--capslot`), so a title sits in exactly the
  same place with or without a `^` line. Body text beside a title compensates for
  line-height to align optical top edges — see `.row.hastitle > .cell.text`.
- **Photos are absolutely positioned inside `.imgbox`** so they contribute nothing to
  box height; a percentage height here breaks side-by-side heights under print
  pagination. `~ zoom focalX focalY` maps to `--z/--ox/--oy` on screen and to `srcRect`
  in PPTX.

## Versioning

`VERSION` in `index.html` is the single source (shown next to the logo, and written into
the sample deck's closing footer). Bumping it means: edit `VERSION`, add a `CHANGELOG.md`
entry, update the version line in `README.md`, and tag the commit (`v1.0`). `isSample`
strips `Made with PpTex <n>` before fingerprinting, so a version bump alone does not need
a new `SAMPLE_FINGERPRINTS` entry — changing the deck's text still does.

## Persistence

- Draft text: `localStorage['pptex.draft']`. A stored draft that fingerprints as an
  untouched sample (`isSample`, `SAMPLE_FINGERPRINTS`) is discarded so a new
  `DEFAULT_SRC` is never shadowed. When you change the sample deck, add the old
  fingerprint to that set.
- Open file: a `FileSystemFileHandle`, autosaved ~800 ms after each edit.
- Image folder: IndexedDB `pptex` / `links` pairs a file handle with a directory
  handle so reopening a deck restores its images; permission may need re-granting,
  which the folder chip prompts for (`pendingDir`).
- Images without a linked folder are in-memory blob URLs only (`images` Map).

## Conventions

Everything — code comments, the UI, the README, commit messages — is written in English. Commit subjects read as a plain sentence describing the user-visible effect
("Keep side-by-side photos the same height in the PDF"), not a conventional-commit
prefix. Match the existing terse style: compact multi-statement lines, `el()` helper
for DOM, `$()` for lookups.

The PPTX writer builds its own ZIP (`crc32`, `deflateRaw`, `zipBlob`) and OOXML strings
by hand — there is no library, and none should be added; keeping the file dependency-free
is the point.
