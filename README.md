# portfolio.raceus.co.il

A personal portfolio built as a faithful replica of the [Claude Code](https://claude.com/claude-code)
terminal UI. One self-contained HTML file: no build step, no dependencies, no framework.

**Live:** https://portfolio.raceus.co.il

## What it does

Boots like a real session — welcome box, then it types `whoami`, shows a thinking
spinner with elapsed seconds, prints the output, types `ls ~/projects`, and reveals
a selection list.

The list is a real TUI control:

| Input | Action |
|---|---|
| `↑` `↓` or `j` `k` | move the `❯` marker |
| `1`–`7` | jump to a project |
| `Home` / `End` | first / last |
| `Enter` | open the project in a new tab |
| `Tab` | describe it without opening |
| hover / click | same as move / open |
| `?` | shortcuts |

The prompt below also takes typed commands: `help`, `ls`, `cat <project>`,
`open <project>`, `stack`, `contact`, `mcp`, `clear`.

Projects carry an honest status flag. `● live` entries open; `○ private` entries
report `⎿ private · no public link` rather than dead-ending on a link that goes
nowhere or lands on a login wall.

## Implementation notes

Three things that are less obvious than they look:

**Box drawing doesn't survive mixed content.** The panels were first drawn with
`╭─╮│╰─╯` padded by counting characters. That cannot hold: an inline SVG and glyphs
like `↑↓·` are not one character wide, so every border column drifts. The boxes are
CSS borders — visually identical, immune to whatever is inside them.

**`white-space: pre` belongs on rows, not the container.** On the container it
renders every newline and indent in the HTML source as visible whitespace.

**A terminal hard-wraps; a phone can't.** Prose rows use `pre-wrap` with a hanging
indent so they reflow, and the project list drops descriptions rather than truncating
mid-word when the measured column count is too small. Column widths derive from the
longest project name so a name is never clipped.

The block cursor only stands in while the input is empty. As soon as there's text the
native caret takes over, which keeps selection, paste and mobile IME composition
working — hiding the real input to draw a fake cursor breaks all three.

## Deploy

Static files in `public/`, served by Cloudflare Pages. GitHub Actions deploys on
every push to `main`; each pull request gets its own preview deployment on a
`pr-N` branch, so you can look at a change before the live host moves.

`.github/workflows/deploy.yml` needs two repository secrets:

| Secret | Value |
|---|---|
| `CLOUDFLARE_API_TOKEN` | API token with Account → Cloudflare Pages: Edit, scoped to the account |
| `CLOUDFLARE_ACCOUNT_ID` | the Cloudflare account id |

The workflow stamps the deployed commit into `<meta name="build-sha">`, so the
live page says which commit it is serving.

## Licence

MIT
