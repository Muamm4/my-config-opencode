# Config & Environment Audit

Misconfigured environments are one of the most common ways attackers gain initial access to a system.

## 1. Environment File Audit (`.env`)

Check for the following critical misconfigurations:

### Debug Mode
- **Pattern**: `APP_DEBUG=true` in production.
- **Risk**: Exposes detailed stack traces, environment variables, and database queries to the end user.
- **Fix**: Set `APP_DEBUG=false`.

### Default Credentials
- **Pattern**: Default passwords for DB, Redis, Mail, etc.
- **Risk**: Trivial access to infrastructure.
- **Fix**: Use strong, unique passwords.

### Exposed Secrets
- **Pattern**: Hardcoded API keys or secrets in `.env.example` or committed `.env` files.
- **Risk**: Leakage of third-party service credentials.
- **Fix**: Ensure `.env` is in `.gitignore`.

## 2. Laravel Config Audit (`config/*.php`)

### Session Configuration
- **Pattern**: `SESSION_DRIVER=file` on a load-balanced server without shared storage.
- **Pattern**: Missing `SESSION_SECURE_COOKIE=true` in production.
- **Risk**: Session hijacking or instability.

### Cache & Queue Configuration
- **Pattern**: Using `sync` driver in production for heavy tasks.
- **Risk**: Denial of Service (DoS) via request timeouts.

### App Key
- **Pattern**: Missing `APP_KEY` or using a known default key.
- **Risk**: Inability to encrypt/decrypt cookies and passwords.

## 3. Auditing Workflow

1. **Read `.env`**: Check for `APP_DEBUG`, `APP_ENV`, and secret strength.
2. **Read `config/app.php`**: Verify environment-specific settings.
3. **Check `.gitignore`**: Ensure `.env` is explicitly ignored.
4. **Verify Production Parity**: Ensure the production config is as restrictive as possible.

## Severity: HIGH
A single `APP_DEBUG=true` in production can provide an attacker with everything they need to find a critical RCE.
