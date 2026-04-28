# SQL Injection Vulnerabilities

SQL Injection occurs when untrusted data (Taint) reaches a database query sink without being neutralized.

## Taint Analysis: SQL Injection

### 1. Sources of Taint
- `$request->input()`, `$request->query()`, `$_GET`, `$_POST`.
- Data from external APIs or previously stored user input.

### 2. Dangerous Sinks
Any function that executes raw SQL strings.
- `DB::raw()`, `whereRaw()`, `selectRaw()`, `orderByRaw()`, `havingRaw()`.

### 3. The Taint Path (Vulnerable)
`Source ($request->q)` $\to$ `Concatenation ("... WHERE name = '" . $q . "'")` $\to$ `Sink (whereRaw)`

**Insecure Pattern:**
```php
// DANGEROUS: Taint reaches sink via concatenation
$users = DB::table('users')
    ->whereRaw("name = '$request->name'")
    ->get();
```

### 4. Neutralizers (Taint Breaking)
- **Parameterized Bindings**: The gold standard. Separates the command from the data.
- **Strict Type Casting**: `(int)$request->id` (neutralizes for numeric fields).
- **Whitelist Validation**: `in_array($request->sort, ['name', 'email'])`.

**Secure Pattern:**
```php
// SAFE: Taint is neutralized by parameterized binding
$users = User::where('email', $request->email)->get();

// Or with explicit binding in raw queries:
$users = DB::table('users')
    ->whereRaw("email = ?", [$request->email])
    ->get();
```

## Auditing Workflow
1. **Find Sinks**: Grep for `Raw` methods in `app/`.
2. **Trace Backwards**: For every sink, trace the variables back to their source.
3. **Verify Neutralization**: Check if the variable was cast to int, validated against a whitelist, or bound as a parameter.
4. **Construct Proof**: Create a payload (e.g., `' OR 1=1 --`) that would alter the query logic.

## Severity: CRITICAL
SQL Injection can allow data exfiltration, privilege escalation, or complete database compromise.
