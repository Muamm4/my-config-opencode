# XSS (Cross-Site Scripting) Vulnerabilities

XSS occurs when untrusted data (Taint) reaches an HTML output sink without being neutralized.

## Taint Analysis: XSS

### 1. Sources of Taint
- `$request->input()`, `$_GET`, `$_POST`.
- Data from the database that was originally user-provided (Stored XSS).
- Data from external APIs.

### 2. Dangerous Sinks
Any function or template directive that renders raw content to the browser.
- Blade: `{!! $variable !!}`
- PHP: `echo`, `print`, `<?= $variable ?>`
- JSON: `response()->json()` (if the client renders the value as HTML).

### 3. The Taint Path (Vulnerable)
`Source ($request->bio)` $\to$ `Stored in DB` $\to$ `Fetched in Controller` $\to$ `Sink ({!! $bio !!})`

**Insecure Pattern:**
```blade
<!-- DANGEROUS: Taint reaches sink without neutralization -->
<p>{!! $user->bio !!}</p>
```

### 4. Neutralizers (Taint Breaking)
- **HTML Escaping**: Converting `<` to `&lt;`, etc.
- **Blade Double Braces**: `{{ $variable }}` automatically calls `htmlspecialchars`.
- **Explicit Escaping**: Using the `e()` helper.
- **HTML Sanitization**: Using a library like HTMLPurifier to allow only safe tags.

**Secure Pattern:**
```blade
<!-- SAFE: Taint is neutralized by automatic escaping -->
<p>{{ $user->bio }}</p>

<!-- SAFE: Explicitly escaped -->
<p>{{ e($user->bio) }}</p>
```

## Auditing Workflow
1. **Find Sinks**: Grep for `{!!` in `resources/views/`.
2. **Trace Backwards**: For every raw output, trace the variable back to its source.
3. **Verify Neutralization**: Check if the data was sanitized using a trusted library before being stored or rendered.
4. **Construct Proof**: Create a payload (e.g., `<script>alert(1)</script>`) that would execute in the browser.

## Severity: HIGH
XSS can steal session cookies, redirect users, or perform actions as the authenticated user.
