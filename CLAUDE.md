# CLAUDE.md — quarto-comments

## Project goal

Quarto extension adding collaboration-friendly annotations to Quarto documents:
inline margin comments, todos, notes and questions. Renders as styled callouts
in HTML and as `todonotes` margin notes in PDF/LaTeX.

Repository: https://github.com/vgreg/quarto-comments (upstream)
Fork: https://github.com/zinc75/quarto-comments

## Architecture

```
_extensions/comments/
├── _extension.yml       ← extension declaration
├── comment_core.lua     ← main Lua filter (config reading, rendering logic)
├── comment             ← shortcode dispatcher → comment_core.lua
├── note                ← shortcode dispatcher → comment_core.lua
├── question            ← shortcode dispatcher → comment_core.lua
├── todo                ← shortcode dispatcher → comment_core.lua
└── assets/
    ├── comments.css     ← HTML styles
    └── comments.sty     ← LaTeX base styles (margin width, todo defaults)
example.qmd              ← runnable demo (HTML + PDF)
README.md
```

## Key logic (comment_core.lua)

- `get_config(meta)` — reads extension config from document metadata
- `COMMENT_ICONS` — Unicode emoji for HTML output
- `LATEX_EMOJI_COMMANDS` — LaTeX commands for PDF icons (currently uses `emoji` package)
- HTML rendering: styled Quarto callouts with CSS, colourised per author
- LaTeX rendering: `\todo[...]{}` and `\todo[inline,...]{}`  from `todonotes`
- Author colours: hex → `\definecolor` conversion for LaTeX

## PRs already merged / in progress (zinc75 contributions)

| Branch | Status | What |
|---|---|---|
| `fix/windows-compatibility` | PR #5 | `(.*/)`→`(.*[/\\])` in 4 shortcode files |
| `fix/scope-config-under-extensions-namespace` | PR open | `comments:`→`extensions.quarto-comments:`, removes `validate-yaml: false` |

## Current LaTeX icon approach (to be replaced)

```lua
local LATEX_EMOJI_COMMANDS = {
  comment  = "\\emoji{speech-balloon}",
  todo     = "\\emoji{memo}",
  note     = "\\emoji{pushpin}",
  question = "\\emoji{red-question-mark}",
}
```

Uses the `emoji` LaTeX package — **incompatible with pdflatex**. Only works
with XeLaTeX / LuaLaTeX.

## Next work (FontAwesome PR)

Replace `emoji` package with `fontawesome5` (pdflatex-compatible, already
loaded by Quarto when callouts are present):

- `comment_core.lua`: replace `LATEX_EMOJI_COMMANDS` with FontAwesome5 commands
- `_extension.yml`: declare `\usepackage{xcolor}`, `\usepackage{todonotes}`,
  `\usepackage{fontawesome5}` under `format.pdf.include-in-header` so users
  no longer need any manual `include-in-header` in their document
- `example.qmd`: remove `include-in-header` block (now handled by extension)
- `README.md`: update accordingly

FontAwesome5 icon mapping:
| Type     | emoji package         | fontawesome5      |
|----------|-----------------------|-------------------|
| comment  | `\emoji{speech-balloon}` | `\faComment{}`    |
| todo     | `\emoji{memo}`        | `\faEdit{}`       |
| note     | `\emoji{pushpin}`     | `\faThumbtack{}`  |
| question | `\emoji{red-question-mark}` | `\faQuestionCircle{}` |

## Code standards

- Lua only (no Python, no shell)
- English comments in Lua code
- Never commit CLAUDE.md (add to .git/info/exclude)
