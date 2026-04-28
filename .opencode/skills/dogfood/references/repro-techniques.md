# Reproduction Techniques

Technical guidance for capturing high-quality evidence during dogfood sessions.

## Command Reference

### Snapshot Commands

- `snapshot -i` — for finding clickable/fillable elements (buttons, inputs, links)
- `snapshot` (no flag) — for reading page content (text, headings, data lists)

### Screenshot Commands

```bash
# Annotated screenshot (highlights elements)
agent-browser --session {SESSION} screenshot --annotate {OUTPUT_DIR}/screenshots/{name}.png

# Regular screenshot
agent-browser --session {SESSION} screenshot {OUTPUT_DIR}/screenshots/{name}.png
```

### Recording Commands

```bash
# Start recording
agent-browser --session {SESSION} record start {OUTPUT_DIR}/videos/issue-{NNN}.webm

# Stop recording
agent-browser --session {SESSION} record stop
```

### Console Commands

```bash
# Check for JavaScript errors
agent-browser --session {SESSION} errors

# Check console output
agent-browser --session {SESSION} console
```

## Pacing for Humans

- Add `sleep 1` between actions during video recording
- Add `sleep 2` before the final result screenshot
- Videos should be watchable at 1x speed -- a human reviewing the report needs to see what happened, not a blur of instant state changes

## Typing vs Filling

- Use `type` instead of `fill` during video recording -- it types character-by-character for realism
- Use `fill` only outside of video recording when speed matters

## Scrolling

Use `agent-browser --session {SESSION} scroll down 300` for scrolling -- do not use `key` or `evaluate` to scroll.

## Batching Commands

Be efficient with commands. Batch multiple `agent-browser` commands in a single shell call when they are independent:

```bash
agent-browser --session {SESSION} screenshot {OUTPUT_DIR}/screenshots/page.png && agent-browser --session {SESSION} console
```

## Verification Checklist

Before finalizing an issue:

1. [ ] Issue is reproducible (verified with at least one retry)
2. [ ] Evidence type matches issue type (video for interactive, screenshot for static)
3. [ ] Screenshots are clear and show the problem
4. [ ] Repro steps are numbered and map to screenshots
5. [ ] Console was checked for errors
6. [ ] Severity and category are assigned correctly