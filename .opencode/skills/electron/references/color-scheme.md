# Color Scheme Preservation

Electron apps may render in light mode by default when connected via CDP. Preserve dark mode for accurate screenshots.

## Per-Command

```bash
agent-browser connect 9222
agent-browser --color-scheme dark snapshot -i
```

## Session-Wide

```bash
# Set before connecting
AGENT_BROWSER_COLOR_SCHEME=dark agent-browser connect 9222
```

## Environment Variable

Add to shell profile for persistent dark mode:

```bash
export AGENT_BROWSER_COLOR_SCHEME=dark
```

## Why This Matters

- Accurate visual regression testing
- Readable screenshots in dark-mode apps
- Consistent extracted UI values