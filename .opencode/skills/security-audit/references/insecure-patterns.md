# Insecure Code Patterns

Common insecure patterns that introduce vulnerabilities beyond the OWASP Top 10.

## Detection Patterns

### 1. Code Execution Functions
```bash
# Find dangerous PHP functions
grep -r "eval\|shell_exec\|system\|exec\|passthru\|proc_open\|popen" app/
```

**Insecure Pattern:**
```php
// DANGEROUS: Executing user input as code
eval($request->code);
shell_exec($request->command);
system("ls " . $request->directory);
```

**Safe Alternative:**
```php
// SAFE: Use predefined commands
$allowedCommands = ['list', 'details'];
if (in_array($request->action, $allowedCommands)) {
    // Execute only predefined actions
}
```

### 2. File Upload Validation
```bash
# Find file upload handling
grep -r "move\|upload\|store" app/Http/Controllers/
```

**Insecure Pattern:**
```php
// DANGEROUS: No validation
$request->file('image')->move('/uploads', $request->filename);
```

**Secure Pattern:**
```php
// SAFE: Validate file type and size
$request->validate([
    'image' => 'required|image|mimes:jpg,png|max:2048',
]);

$filename = $request->file('image')
    ->storeAs('uploads', uniqid() . '.' . $request->file('image')->extension());
```

### 3. Unserialization
```bash
# Find unserialize calls
grep -r "unserialize" app/
```

**Insecure Pattern:**
```php
// DANGEROUS: Unserializing user input
$data = unserialize($request->data);
```

**Safe Alternative:**
```php
// SAFE: Use JSON instead
$data = json_decode($request->data, true);
```

### 4. Path Traversal
```bash
# Find file operations with user input
grep -r "file_get_contents\|readfile\|fopen" app/
```

**Insecure Pattern:**
```php
// DANGEROUS: User-controlled file path
$content = file_get_contents('/uploads/' . $request->filename);
```

**Secure Pattern:**
```php
// SAFE: Validate and sanitize path
$filename = basename($request->filename); // Remove directories
$path = storage_path('uploads/' . $filename);
if (realpath($path) === $path) {
    $content = file_get_contents($path);
}
```

### 5. Sensitive Data Exposure
```bash
# Find sensitive data in responses
grep -r "password\|secret\|api_key\|token" app/Http/Controllers/
```

**Insecure Pattern:**
```php
// DANGEROUS: Returning sensitive data
return response()->json($user);
```

**Secure Pattern:**
```php
// SAFE: Exclude sensitive fields
return response()->json($user->makeHidden(['password', 'api_key']));
```

## Remediation Steps

1. **Never** use `eval()` or shell execution functions
2. **Always** validate file uploads (type, size, name)
3. **Prefer** JSON over `unserialize()`
4. **Validate** file paths to prevent traversal
5. **Exclude** sensitive data from API responses

## Severity: Varies by Issue

Code execution and path traversal are CRITICAL; file upload issues are HIGH.