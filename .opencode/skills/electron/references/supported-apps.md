# Supported Apps

Any app built on Electron can be automated using this skill, as all Electron apps support the Chrome DevTools Protocol (CDP).

## Communication

- Slack
- Discord
- Microsoft Teams
- Signal
- Telegram Desktop

## Development

- VS Code (Visual Studio Code)
- GitHub Desktop
- Postman
- Insomnia

## Design

- Figma
- Notion
- Obsidian

## Media

- Spotify
- Tidal

## Productivity

- Todoist
- Linear
- 1Password

## Verification

To check if an app is Electron-based:

```bash
# macOS
otool -L "/Applications/Slack.app/Contents/MacOS/Slack" | grep -i electron

# Linux
ldd $(which slack) | grep -i electron
```

## App-Specific Notes

- **Slack**: Multiple workspaces open as separate windows
- **Discord**: Voice/Video windows are separate webviews
- **VS Code**: Extensions run in separate webviews
- **Figma**: Desktop app wraps the web version

## Adding New Apps

Most Electron apps support `--remote-debugging-port` out of the box. If an app supports it, you can add it to your automation workflow following the standard launch patterns.