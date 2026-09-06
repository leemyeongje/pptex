# PpTex

PpTex is a minimal markup language for rapidly creating presentations in a consistent
visual style. It describes **content, hierarchy, and spatial relationships**, while
visual styling is handled by the renderer.

**→ [leemyeongje.github.io/pptex](https://leemyeongje.github.io/pptex/)**

`index.html` is the reference renderer and editor — one self-contained file, no build
step. Use the link above, or run it locally:

```
python3 -m http.server 8000   # then open http://localhost:8000/
```

Open it over `http://localhost` or the HTTPS link rather than `file://` — editing files
in place needs a secure context. Chromium browsers only for the file-editing and
squircle parts; everything else renders anywhere.

## Syntax

| | |
|---|---|
| `---` | New slide |
| `^` | Caption / eyebrow |
| `#` | Title |
| `##` | Subtitle |
| *(none)* | Body text |
| `1.` | Numbered list |
| `!` | Image (bare `!` is an empty image slot) |
| `\|` | Image caption |
| `~` | Image zoom / focal point |
| `[ ]` | Group |
| newline | Horizontal layout |
| blank line | Vertical layout |
| `\` + newline | Line break within text |
| `@bg` | Background image |
| `@footer` | Footer |

### 1. Slides

```
# First Slide
Content
---
# Second Slide
Content
```

### 2. Text hierarchy

```
^ 01
# Section Title
## Subtitle
Body text sits beside the title block.
```

Quotation marks have no special syntax. They are simply part of the text.

```
# "Quotation marks are just text."
## Attribution
```

Adjacent `^` / `#` / `##` lines coalesce into a single title block. The title keeps
exactly the same position and size whether or not a caption is present.

Adjacent `1.` `2.` `3.` lines coalesce into a single numbered list.

### 3. Images

```
!photo.jpg

!a.jpg | First
!b.jpg | Second
!c.jpg | Third
```

Image captions are plain text. They do not have separate formatting syntax.

A bare `!` is an **empty image slot**. Drag an image onto that slot in the rendered
pane and the filename is written into the source.

Images are cropped to fill their frame. Double-click an image in the rendered pane to
set its zoom and focal point; the renderer writes the result after `~`:

```
!photo.jpg ~ 1.4 38 22 | Caption
```

`~ zoom focalX focalY` — you rarely type this by hand.

### 4. Layout

Layout is determined by line breaks.

```
A
B
C          →   A → B → C     (single line break — horizontal)

A

B

C          →   A ↓ B ↓ C     (blank line — vertical)
```

This rule applies to all elements, including text, images, and groups.

### 5. Groups

Use `[ ]` to combine multiple elements into a single layout element.

```
A
[
    B

    C
]
D          →   A → [B ↓ C] → D
```

The same layout rules apply recursively inside groups.

```
[
    A
    B
]

[
    C
    D
]          →   [A → B]
                  ↓
               [C → D]
```

Indentation has no syntactic meaning. It is used only for readability.

### 6. Line breaks inside text

A trailing `\` continues the same text element on a new line.

```
Objects on the desk exist separately. \
Cables become tangled, and the experience becomes fragmented.
```

The three whitespace rules are therefore:

```
newline        horizontal layout
blank line     vertical layout
\ + newline    line break within text
```

### 7. Slide modifiers

`@` introduces a modifier that applies to the slide itself.

```
@bg !backdrop.jpg

@footer Your name \
2026
```

The `!` retains its meaning as an image reference: `!backdrop.jpg` places the image as an
element, while `@bg !backdrop.jpg` uses it as the slide background. Everything following
`@footer` becomes the footer content, and `\` breaks its lines like any other text.

PpTex does not expose low-level styling properties such as `@color`, `@size`,
`@margin`, or `@align`. These are determined by the renderer.

## What the renderer decides

- First slide → cover type scale.
- A slide holding only a title block → large type, title centered exactly on the slide.
- A slide holding only images → full bleed, no margins.
- Everything else → the default scale.
- Rows containing an image share the leftover height; text-only rows take their own.

## Editor

The reference renderer doubles as an editor.

| | |
|---|---|
| ⌘/Ctrl + S | Save |
| ⌘/Ctrl + Enter | New slide |
| Click in the preview | Move the cursor to that element |
| Double-click an image | Set zoom and focal point |
| Drag an image onto a slot | Fill it in, writing the filename into the source |

Opening a `.pptex` / `.txt` file edits **that file in place**, autosaving shortly after
each change. Dropping a text file onto the window opens it. Without a file open, work
is kept in browser storage only.

The preview follows the caret: the slide your cursor is in stays in view as you type.
