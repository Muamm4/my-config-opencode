# Discovery & Attack Surface Mapping

Before scanning for patterns, a Senior Security Engineer maps the attack surface to understand where the application is exposed and where the trust boundaries lie.

## 1. Entry Point Mapping

Identify every way an external user can send data into the application.

### Route Analysis
- **Web Routes**: `routes/web.php`
- **API Routes**: `routes/api.php`
- **Console Commands**: `app/Console/Commands/`
- **Job Handlers**: `app/Jobs/` (if they process external data)
- **Webhooks**: Any route specifically designed for external service callbacks.

**Action**: Use `glob` or `read` on route files to list all endpoints.

### Controller Mapping
- Map routes to their corresponding Controller methods.
- Identify "God Controllers" (too many responsibilities) as they are higher-risk areas.

## 2. Trust Boundary Identification

A trust boundary is any point where data crosses from an untrusted source (the internet/user) to a trusted internal system (the database/filesystem/shell).

### Untrusted Sources (Sources of Taint)
- `$request->all()`, `$request->input()`, `$request->query()`
- `$_GET`, `$_POST`, `$_COOKIE`, `$_FILES`
- Data retrieved from external APIs (even if "trusted" services).
- Data from the database that was originally provided by a user (Stored Taint).

### Trusted Sinks
- Database queries (`DB::raw`, `whereRaw`)
- HTML output (`{!! !!}`, `echo`)
- System commands (`shell_exec`, `exec`)
- File operations (`file_get_contents`, `unlink`)

## 3. Middleware & Protection Analysis

Analyze the middleware stack to see what protections are already in place.

- **Auth Middleware**: Is the route protected by `auth`, `auth:sanctum`, or `can:permission`?
- **CSRF Middleware**: Is `VerifyCsrfToken` active for this route?
- **Throttle Middleware**: Is there a `throttle` limit to prevent brute-force/DoS?
- **Validation Middleware**: Is there a FormRequest (`php artisan make:request`) validating the input?

## 4. Attack Surface Summary

Create a mental or written map:
- **Public Surface**: Unauthenticated routes (Registration, Login, Public API).
- **Authenticated Surface**: Routes requiring login (User Profile, Settings).
- **Privileged Surface**: Routes requiring admin roles (Admin Dashboard, User Management).

**Goal**: Focus the audit on the most exposed and least protected boundaries first.
