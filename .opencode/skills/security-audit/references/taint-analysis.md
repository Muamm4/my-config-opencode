# Taint Analysis Methodology

Taint Analysis is the process of tracing untrusted data (Taint) from its entry point (Source) to a dangerous function (Sink).

## 1. The Taint Lifecycle

`Source` $\to$ `Propagation` $\to$ `Sanitization/Validation` $\to$ `Sink`

### Source (The Entry Point)
Untrusted data enters the system.
- **Examples**: `$request->input('id')`, `$request->all()`, `$_GET['search']`.

### Propagation (The Flow)
Data is passed between variables, methods, and classes.
- **Example**: `Controller` $\to$ `Service` $\to$ `Repository` $\to$ `Query`.
- **Warning**: Be careful with data transformation (e.g., base64 encoding, JSON decoding) which can hide the taint.

### Sanitization/Validation (The Neutralizer)
The process of making the data safe.
- **Validation**: Ensuring data matches a format (e.g., `integer`, `email`).
- **Escaping**: Neutralizing special characters (e.g., `htmlspecialchars`, `e()`).
- **Parameterized Queries**: Separating data from the command (e.g., `where('id', $id)`).

### Sink (The Danger Zone)
A function that performs a sensitive operation. If tainted data reaches a sink without being neutralized, a vulnerability exists.

| Sink Category | Dangerous Functions (Sinks) | Vulnerability if Tainted |
|---------------|-----------------------------|--------------------------|
| **Database** | `DB::raw()`, `whereRaw()`, `selectRaw()`, `orderByRaw()` | SQL Injection |
| **Output** | `{!! !!}`, `echo`, `print`, `response()->json()` (if rendered as HTML) | XSS |
| **System** | `shell_exec()`, `exec()`, `system()`, `passthru()` | RCE |
| **Filesystem** | `file_get_contents()`, `unlink()`, `fopen()` | Path Traversal / LFI |
| **Serialization** | `unserialize()` | PHP Object Injection |

## 2. How to Perform Taint Analysis

1. **Identify the Source**: Find where the user input starts.
2. **Trace the Variable**: Follow the variable through every function call.
   - Use `Read` to jump into method definitions.
   - Use `lsp_find_references` to see where a variable is used.
3. **Check for Neutralizers**: At each step, ask: "Is this data being validated or escaped?"
   - If it's passed through `(int)$id`, the taint is neutralized for SQLi.
   - If it's passed through `e($input)`, the taint is neutralized for XSS.
4. **Reach the Sink**: If the data reaches a Sink function while still "tainted," you have found a vulnerability.

## 3. Common Taint-Breaking Patterns (False Positives)

Do not report a vulnerability if the taint is broken by:
- **Strict Type Casting**: `(int)$request->id`
- **Whitelist Validation**: `in_array($request->sort, ['asc', 'desc'])`
- **Framework Protections**: Using Eloquent's standard `where()` methods.
- **Strong Validation**: `request()->validate(['id' => 'required|integer'])`
