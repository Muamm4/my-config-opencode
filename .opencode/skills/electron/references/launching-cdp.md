# Launching Electron Apps with CDP

Every Electron app supports the `--remote-debugging-port` flag since it's built into Chromium. The flag must be present at launch time. If the app is already running, quit it first, then relaunch with the flag.

## macOS

```bash
# Slack
open -a "Slack" --args --remote-debugging-port=9222

# VS Code
open -a "Visual Studio Code" --args --remote-debugging-port=9223

# Discord
open -a "Discord" --args --remote-debugging-port=9224

# Figma
open -a "Figma" --args --remote-debugging-port=9225

# Notion
open -a "Notion" --args --remote-debugging-port=9226

# Spotify
open -a "Spotify" --args --remote-debugging-port=9227
```

## Linux

```bash
slack --remote-debugging-port=9222
code --remote-debugging-port=9223
discord --remote-debugging-port=9224
```

## Windows

```bash
"C:\Users\%USERNAME%\AppData\Local\slack\slack.exe" --remote-debugging-port=9222
"C:\Users\%USERNAME%\AppData\Local\Programs\Microsoft VS Code\Code.exe" --remote-debugging-port=9223
```

## Port Allocation Strategy

When running multiple Electron apps simultaneously, assign unique ports:

| App | Port |
|-----|------|
| Slack | 9222 |
| VS Code | 9223 |
| Discord | 9224 |
| Figma | 9225 |
| Notion | 9226 |
| Spotify | 9227 |

## Common Issues

- **Flag not生效**: The app was already running. Quit and relaunch.
- **Port in use**: Use `lsof -i :9222` to check what's using the port.