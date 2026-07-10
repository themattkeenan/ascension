# Visual Companion Guide

Browser-based visual brainstorming companion for showing mockups, diagrams, and options during intent discovery. Ported from Superpowers; the Ascension build loads no external images and makes no network requests (no telemetry).

## When to Use

Decide per-question, not per-session. The test: **would your human partner understand this better by seeing it than reading it?**

**Use the browser** when the content itself is visual:

- **UI mockups** — wireframes, layouts, navigation structures, component designs
- **Architecture diagrams** — system components, data flow, relationship maps
- **Side-by-side visual comparisons** — two layouts, two color schemes, two design directions
- **Design polish** — questions about look and feel, spacing, visual hierarchy
- **Spatial relationships** — state machines, flowcharts, entity relationships rendered as diagrams

**Use the terminal** when the content is text or tabular:

- **Requirements and scope questions** — "what does X mean?", "which features are in scope?"
- **Conceptual A/B/C choices** — picking between approaches described in words
- **Tradeoff lists** — pros/cons, comparison tables
- **Technical decisions** — API design, data modeling, architecture selection
- **Clarifying questions** — anything where the answer is words, not a visual preference

A question *about* a UI topic is not automatically a visual question. "What kind of wizard do you want?" is conceptual — use the terminal. "Which of these wizard layouts feels right?" is visual — use the browser.

## How It Works

The server watches a directory for HTML files and serves the newest one to the browser. You write HTML to `screen_dir`; your human partner sees it and can click to select options. Selections are recorded to `state_dir/events`, which you read on your next turn.

**Content fragments vs full documents:** If your HTML starts with `<!DOCTYPE` or `<html`, the server serves it as-is (injecting only the helper script). Otherwise it wraps your content in the frame template — header, CSS theme, connection status, all interactive infrastructure. **Write content fragments by default.** Only write full documents when you need complete control over the page.

## Starting a Session

```bash
# Start AFTER your human partner approves the companion. --open auto-opens their
# browser on the first screen; --project-dir persists mockups and enables
# same-port restart.
scripts/start-server.sh --project-dir /path/to/project --open

# Returns: {"type":"server-started","port":52341,
#           "url":"http://localhost:52341/?key=ab12…",
#           "screen_dir":".../.ascension/brainstorm/12345-1706000000/content",
#           "state_dir":".../.ascension/brainstorm/12345-1706000000/state"}
```

Save `screen_dir` and `state_dir` from the response. With `--open`, the browser opens itself when you push the first screen — still share the URL as a fallback (headless/remote setups won't auto-open).

**The URL contains a session key (`?key=…`).** The server rejects any request without it, so always give the **complete** URL from the `url` field — never strip the query string, never hand out a bare `http://host:port`. The key gates HTTP and WebSocket access so a stray tab or another machine can't read the screens or inject events. After the first load the browser remembers the key via a cookie.

**Finding connection info:** The server writes its startup JSON to `$STATE_DIR/server-info`. If you launched in the background and didn't capture stdout, read that file for the URL and port. With `--project-dir`, check `<project>/.ascension/brainstorm/` for the session directory.

**Persistence:** Pass the project root as `--project-dir` so mockups persist in `.ascension/brainstorm/` and survive restarts. Without it, files go to `/tmp` and get cleaned up. Remind your human partner to add `.ascension/` to `.gitignore` if it isn't already.

**Launching by platform:**

**Claude Code:** the script backgrounds the server itself:
```bash
scripts/start-server.sh --project-dir /path/to/project --open
```
On Windows the script auto-detects and switches to foreground mode (which blocks the tool call). Use `run_in_background: true` on the Bash tool call so the server survives across turns, then read `$STATE_DIR/server-info` next turn for the URL and port.

**Other environments:** The server must keep running across turns. If your environment reaps detached processes, use `--foreground` and launch via your platform's background mechanism.

If the URL is unreachable from your browser (remote/containerized setups), bind a non-loopback host and control the printed hostname:
```bash
scripts/start-server.sh --project-dir /path/to/project --host 0.0.0.0 --url-host localhost
```

## The Loop

1. **Check server is alive**, then **write HTML** to a new file in `screen_dir`:
   - Confirm `$STATE_DIR/server-info` exists and `$STATE_DIR/server-stopped` does not before referring to the URL or pushing a screen. If it shut down, restart with the **same `--project-dir`** — it reuses the port, so the open tab reconnects on its own. The server auto-exits after 4 hours idle (`--idle-timeout-minutes`).
   - Use semantic filenames: `platform.html`, `visual-style.html`, `layout.html`
   - **Never reuse filenames** — each screen gets a fresh file
   - Use your file-creation tool — **never cat/heredoc** (dumps noise into the terminal)
   - The server serves the newest file automatically

2. **Tell your human partner what to expect and end your turn:** remind them of the URL every step, give a one-line summary of what's on screen, and ask them to respond in the terminal ("Click to select an option if you'd like.").

3. **On your next turn** — after they respond in the terminal:
   - Read `$STATE_DIR/events` if it exists — browser interactions as JSON lines
   - Merge with their terminal text; the terminal message is primary feedback

4. **Iterate or advance** — if feedback changes the current screen, write a new file (`layout-v2.html`). Only advance when the current step is validated.

5. **Unload when returning to terminal** — when the next step doesn't need the browser, push a waiting screen to clear stale content:
   ```html
   <!-- filename: waiting.html -->
   <div style="display:flex;align-items:center;justify-content:center;min-height:60vh">
     <p class="subtitle">Continuing in terminal...</p>
   </div>
   ```

6. Repeat until done.

## Writing Content Fragments

Write just the content inside the page; the server wraps it in the frame template (header, theme CSS, connection status, interactive infra).

**Minimal example:**
```html
<h2>Which layout works better?</h2>
<p class="subtitle">Consider readability and visual hierarchy</p>

<div class="options">
  <div class="option" data-choice="a" onclick="toggleSelect(this)">
    <div class="letter">A</div>
    <div class="content"><h3>Single Column</h3><p>Clean, focused reading</p></div>
  </div>
  <div class="option" data-choice="b" onclick="toggleSelect(this)">
    <div class="letter">B</div>
    <div class="content"><h3>Two Column</h3><p>Sidebar navigation with main content</p></div>
  </div>
</div>
```

No `<html>`, CSS, or `<script>` needed — the server provides all of that.

## CSS Classes Available

The frame template provides: `.options` / `.option` / `.letter` (A/B/C choices; add `data-multiselect` on the container for multi-select), `.cards` / `.card` (visual designs), `.mockup` / `.mockup-header` / `.mockup-body`, `.split` (side-by-side), `.pros-cons` / `.pros` / `.cons`, wireframe blocks (`.mock-nav`, `.mock-sidebar`, `.mock-content`, `.mock-button`, `.mock-input`, `.placeholder`), and typography (`h2` title, `h3` heading, `.subtitle`, `.section`, `.label`). See `scripts/frame-template.html` for the full CSS reference.

## Browser Events Format

Clicks are recorded to `$STATE_DIR/events` (one JSON object per line; cleared when you push a new screen):
```jsonl
{"type":"click","choice":"a","text":"Option A - Simple Layout","timestamp":1706000101}
{"type":"click","choice":"c","text":"Option C - Complex Grid","timestamp":1706000108}
```
The last `choice` event is typically the final selection, but the click pattern can reveal hesitation worth asking about. If the file doesn't exist, they didn't interact — use only their terminal text.

## Design Tips

- **Scale fidelity to the question** — wireframes for layout, polish for polish questions
- **Explain the question on each page** — "Which layout feels more professional?" not just "Pick one"
- **Iterate before advancing** — new version file if feedback changes the current screen
- **2-4 options max** per screen
- **Use real content when it matters** — real images for a portfolio; placeholders obscure design issues
- **Keep mockups simple** — focus on layout and structure

## Cleaning Up

```bash
scripts/stop-server.sh $SESSION_DIR
```
With `--project-dir`, mockup files persist in `.ascension/brainstorm/`. Only `/tmp` sessions get deleted on stop.

## Reference

- Frame template (CSS reference): `scripts/frame-template.html`
- Helper script (client-side): `scripts/helper.js`
