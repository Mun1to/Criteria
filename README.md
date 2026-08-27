# Criteria

A Claude Code skill that turns several loose questions into a one-file HTML questionnaire
instead of a wall of text in the terminal: a progress bar, one card per question, an "Other"
option that is always there, and a button that copies every answer as one block of text to
paste back into the chat.

## Install

Inside Claude Code, two lines:

```
/plugin marketplace add Mun1to/Vibeset
/plugin install criteria@vibeset
```

If the install summary says `Run /reload-plugins to activate`, run it.

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

Say "make me a questionnaire", "criteria", "ask me with a form", or anything that means you
would rather fill in several answers at once than dictate them one by one. It only fires when
asked: it will not pop up a form on its own in the middle of something else.

## What it looks like

One HTML file, opened straight in your own browser, never a hosted page:

- A progress bar at the top, always visible while you scroll.
- One card per question: short text, a paragraph, pick one, pick several, or a 0-10 scale.
  "Pick several" uses real checkboxes and says so with its own badge, so it is never confused
  with "pick one".
- Every "pick one" or "pick several" question always carries an "Other:" option with a free
  text field, so an answer outside the list never gets lost. Any option can also carry a short
  explanation and a badge (like "recommended"), so a nuanced question does not need a custom
  page to explain its choices.
- A button at the bottom that builds one plain-text block, one line per question, and copies
  it straight to your clipboard. If the browser blocks the clipboard (it can happen on a
  local `file://` page), the text is left selected so `Ctrl+C` still works.
- Nothing is required to answer: an unanswered question just copies as `(no answer)`.
- Whatever you already typed survives closing the tab by accident, saved in that page's own
  local storage.
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
