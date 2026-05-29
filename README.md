# Quarto Comments Extension

The **Quarto Comments** extension adds collaboration-friendly annotations to Quarto documents. Authors can insert inline notes, to-dos, and discussion points that render as margin callouts in HTML outputs and as [`todonotes`](https://ctan.org/pkg/todonotes) in PDF/LaTeX builds. Comments can be toggled globally and customised per author or reviewer.

## Installation

```bash
quarto add vgreg/quarto-comments
```

Then enable the extension for your project:

```yaml
project:
  extensions:
    - comments
```

## Screenshot

Here are sample renderings of the same document with comments in both HTML and PDF formats:

![HTML rendering with margin callouts and inline comments](sample-html.webp)

![PDF rendering with todo list and margin notes](sample-pdf.webp)

## Shortcodes

Use the `comment` shortcode or one of its aliases directly inside your document:

```markdown
{{< comment "Need to expand this section" author="vg" type="todo" >}}
{{< note "Cross-check with appendix" >}}
{{< todo "Update Table 2 after rerun" author="sm" >}}
{{< question "Can we validate with external data?" inline=true >}}
```

### Arguments

| Argument    | Type                                    | Description                                         |
|-------------|-----------------------------------------|-----------------------------------------------------|
| positional  | string                                  | Required comment text                               |
| `author`    | string                                  | Matches a key defined in the configuration          |
| `type`      | comment \| todo \| note \| question     | Controls styling for iconography and colours        |
| `inline`    | boolean                                 | Forces inline rendering instead of a margin callout |

All aliases (`todo`, `note`, `question`) map to the same underlying logic and set the default `type`.

## Configuration

Options live under the `extensions.quarto-comments` key in the document front matter:

```yaml
---
title: "My Document"
format:
  html: default
  pdf: default
extensions:
  quarto-comments:
    enabled: true       # toggle comments globally (default: true)
    show_author: true   # show author labels (default: true)
    show_list: true     # prepend a list of all comments in PDF (default: false)
    authors:
      vg:
        name: "Vincent"
      cg:
        name: "Clara"
---
```

### Author colours

Each author is automatically assigned a distinct colour drawn from the Bootstrap 5 palette, derived from their name. No manual colour configuration is required.

To override, add `color_html` (CSS hex) and/or `color_latex` (xcolor spec) to the author entry:

```yaml
authors:
  vg:
    name: "Vincent"
    color_html: "#0072B2"
    color_latex: "blue!20"
```

### Other settings

- `enabled: false` strips all comment shortcodes from the output.
- `show_author: false` hides author labels on all comments.
- Anonymous comments (no `author`) automatically suppress author labels.

## Output Behaviour

### HTML

- Margin callouts styled as standard Quarto callouts, colourised per author.
- Icons use [Font Awesome 6](https://fontawesome.com/), injected automatically — no manual `include-in-header` needed.
- Inline comments render as compact badges that sit within text runs.

### PDF / LaTeX

- Comments render via the [`todonotes`](https://ctan.org/pkg/todonotes) package; inline comments use `\todo[inline]{...}`.
- Required LaTeX packages (`xcolor`, `todonotes`, `fontawesome5`) are injected automatically — no manual `include-in-header` needed.
- Icons use [Font Awesome 5](https://ctan.org/pkg/fontawesome5), compatible with pdflatex, xelatex, and lualatex.
- Author colours are defined dynamically via `\definecolor`.
- **List of comments**: set `show_list: true` in the configuration to prepend a clickable list of all margin comments to the PDF. Inline comments are excluded from the list.

### Other Formats

For formats other than HTML and PDF/LaTeX (e.g. `.docx`, EPUB, Markdown), comments fall back to a plain-text representation rather than disappearing silently:

```
TODO (vg): Need to expand this section.
```

## Minimal Example

A runnable example document is included at `example.qmd`. From the repository root:

```bash
quarto render example.qmd --to html,pdf
```

The example demonstrates author configuration, automatic colour assignment, margin callouts, inline comments, and the comment list. Use it as a starting point when wiring the extension into your own projects.
