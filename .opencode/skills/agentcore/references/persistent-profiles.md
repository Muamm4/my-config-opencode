# AgentCore Persistent Profiles

Use persistent profiles to maintain browser state (cookies, localStorage) across sessions. Essential for maintaining authentication.

## Basic Usage

```bash
# First run: log in and authenticate
AGENTCORE_PROFILE_ID=my-app agent-browser -p agentcore open https://app.example.com/login
agent-browser snapshot -i
agent-browser fill @e1 "user@example.com"
agent-browser fill @e2 "password"
agent-browser click @e3
agent-browser close

# Future runs: already authenticated
AGENTCORE_PROFILE_ID=my-app agent-browser -p agentcore open https://app.example.com/dashboard
```

## Profile Behavior

- **State preserved**: Cookies, localStorage, sessionStorage persist across sessions
- **Isolated**: Each `PROFILE_ID` creates a separate browser profile
- **Reusable**: Same profile ID can be used across multiple automation runs

## Use Cases

1. **Login persistence**: Maintain authenticated sessions for repeated tasks
2. **Cart/State**: Preserve shopping cart, form data, or user preferences
3. **Testing**: Set up authenticated test scenarios once, reuse many times

## Cleanup

To clear a profile, either:
- Delete the browser through AWS Console
- Use a new `AGENTCORE_PROFILE_ID` value