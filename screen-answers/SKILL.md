---
name: Screen Answers
description: Read the user's screen, solve the questions/problems visible on it (homework, quizzes, forms, error messages), and present the answers. Use when the user says "check my screen", "see what's on my screen/browser", "answer the questions on it", "give me answers for what I'm looking at", or similar.
---

# Screen Answers

Capture the user's screen, read it, solve what's on it, present the answer with the working. **Never click Submit / Next / Save unless the user explicitly asks.**

## Setup (first use)

Requires Python 3.10+. Install the capture library:

```
python -m pip install pyscreeze
```

- **Windows / Linux**: no extra permissions.
- **macOS**: grant the terminal *Screen Recording* (System Settings → Privacy & Security → Screen Recording), or captures come back blank.
- **No Python available**: use the platform fallback instead of the capture step below:
  - macOS: `screencapture -x /tmp/screen.png`
  - Windows: `python` fallback not possible → use PowerShell: `Add-Type -AssemblyName System.Drawing; $s=[System.Windows.Forms.Screen]::PrimaryScreen.Bounds; $b=New-Object System.Drawing.Bitmap $s.Width,$s.Height; (New-Object System.Drawing.Graphics $b).CopyFromScreen($s.Location,[System.Drawing.Point]::Empty,$b.Size); $b.Save('<path>\screen.png')`

## Workflow

1. If the relevant window isn't in front and a window switcher is available (e.g. `windows-mcp` `Switch-Tool`), bring it to the front. Otherwise ask the user to bring it up.
2. Capture the full screen to a **unique** file per capture (`screen1.png`, `screen2.png`, … — never reuse a name, or you'll read a stale image):
   ```
   python -c "import pyscreeze; pyscreeze.screenshot().save('<tempdir>/screenN.png')"
   ```
3. Read the image with the `read` tool (multimodal).
4. Solve. Show the key steps (a short table or 3–5 line calculation) so the user can verify, then state the final answer plainly.
5. If the user asks to fill in a form: fill only the fields asked for, verify with a follow-up screenshot, and stop before any submit button.
6. On "next" / "again": repeat from step 1 (re-capture; the screen has changed).

## Pitfalls (learned the hard way)

- **MCP screenshot tools may return null/empty** when called through a code-execution layer (the image content doesn't round-trip). If a screenshot tool returns nothing, fall back to the pyscreeze file + `read` flow above.
- **Resolution scaling**: the image you read is often downscaled from the native screen (e.g. 2560×1440 shown as 1920×1080). Never trust eyeballed pixel coordinates. If coordinates are needed, detect them programmatically (e.g. PIL scan for input-box borders) and scale to the native resolution reported by the screenshot file's size.
- **Browser content is invisible to UIA**: element-finder/text tools (UIA-based) cannot see the DOM of Chrome/Brave/Firefox. For browser forms, coordinate-based click+type is the only path.
- **Native `<select>` dropdowns are flaky** with synthetic input: a click may not open the list, and the list often closes before a follow-up screenshot. Try click → `down` → `enter`. If it still fails after 2–3 attempts, stop, skip the dropdown, and hand it to the user — don't burn turns.
- **No `setTimeout` in code-execution runtimes**: don't chain calls with sleeps; do click-then-capture as separate sequential calls.
- **Verify before mass entry**: after the first field, re-capture and confirm the text landed in the right box before filling the rest.
