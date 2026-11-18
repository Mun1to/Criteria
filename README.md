# Criteria

A Claude Code skill that turns several loose questions into a one-file HTML questionnaire
instead of a wall of text in the terminal: one question per screen, choices as tiles, a progress
bar, an "Other" option that is always there, and a button that copies every answer as one block
of text to paste back into the chat.

## Install

Inside Claude Code, two lines:

```
/plugin marketplace add Mun1to/Vibeset
/plugin install criteria@vibeset
```

If the install summary says `Run /reload-plugins to apply`, Claude Code reloads by itself. If it warns and leaves the plugin pending, run `/reload-plugins --force`.

<details>
<summary>Prefer to install it by hand?</summary>

Clone the repository and point your skills folder at it.

```bash
git clone https://github.com/Mun1to/Criteria.git
ln -s "$(pwd)/Criteria" ~/.claude/skills/criteria
```

On Windows, with PowerShell:

```powershell
New-Item -ItemType Junction -Path "$env:USERPROFILE\.claude\skills\criteria" -Target "C:\path\to\Criteria"
```
</details>

## Use it

Type `/criteria` at the start of your message, followed by what you need to decide. It only fires
when you run it: it will not pop up a form on its own in the middle of something else.

## What it looks like

One HTML file, opened straight in your own browser, never a hosted page:

- One question at a time, filling the screen: the question on top, why it is being asked now and
  what happens if you skip it, then Back and Next. The last step lists every answer with a Change
  button next to each.
- Choices are tiles: the option's word in large type with its number, a sentence underneath, and
  the tile fills with the accent color when picked. The recommended one says so in words (never
  pre-selected) and explains why once you pick it; under the tiles, the bad, the good and the cost
  of your pick, one line each, so options compare at a glance.
- A progress bar at the top, always visible while you scroll.
- Question types: short text, a paragraph, pick one, pick several, or a 0-10 scale. "Pick several"
  uses real checkboxes (square, not round) and says so above the tiles, so it is never confused
  with "pick one".
- Every "pick one" or "pick several" question always carries an "Other:" free text field, so an
  answer outside the list never gets lost; typing there counts on its own and keeps what you
  already picked.
- No badges or pills: emphasis is done with weight, size and color of the text itself.
- A single Copy answers button at the bottom that builds one plain-text block, one line per
  question (picked options keep their number), and copies it straight to your clipboard. If the
  browser blocks the clipboard (it can happen on a local `file://` page), the text is left
  selected so `Ctrl+C` still works.
- Nothing is required to answer: an unanswered question just copies as `(sin responder)`, the template's own wording.
- Whatever you already typed survives closing the tab by accident, saved in that page's own
  local storage, and the page reopens on the question you were at.
- The color palette follows whatever project the skill is used in, so the same questionnaire
  does not always look the same; with no project palette to read, it falls back to a default
  theme that follows light or dark automatically, with no toggle.

## Why

Answering ten scattered questions by dictating each one, or typing them one at a time in a
chat box, is slower than just filling in a form once and pasting the result back. This skill
is the concrete way of doing that: it writes the form, opens it, and reads back whatever comes
out the other end.

## Don't trust it, check it

Open source only helps if somebody actually reads the code, and almost nobody does. So instead
of asking you to trust this project, here is the prompt to check it: point your own AI agent at
this repository and get a security report, in your language, in a few minutes, even if you do
not know how to program.

**[Open AI-AUDIT.md](AI-AUDIT.md)** and paste it into Claude Code, Codex, Cursor, Copilot or
whatever you use. It is the same prompt in every public repository here, so you can compare.

## License

MIT, see [LICENSE](LICENSE).
