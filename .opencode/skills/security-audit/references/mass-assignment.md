# Mass Assignment Vulnerabilities

Mass Assignment occurs when untrusted data (Taint) reaches a model creation or update sink without being filtered.

## Taint Analysis: Mass Assignment

### 1. Sources of Taint
- `$request->all()`, `$request->input()`.
- JSON payloads in API requests.

### 2. Dangerous Sinks
Methods that populate model attributes from an array.
- `Model::create($array)`
- `Model::update($array)`
- `Model::fill($array)`

### 3. The Taint Path (Vulnerable)
`Source ($request->all())` $\to$ `Sink (User::create)` $\to$ `Database (is_admin = true)`

**Insecure Pattern:**
```php
// DANGEROUS: Taint reaches sink without filtering
$user = User::create($request->all());
```

### 4. Neutralizers (Taint Breaking)
- **Whitelisting (Controller level)**: Using `$request->only([...])` or `$request->validated()`.
- **Whitelisting (Model level)**: Defining the `$fillable` array.
- **Blacklisting (Model level)**: Defining the `$guarded` array.

**Secure Pattern:**
```php
// SAFE: Taint is neutralized by explicit whitelisting
$validated = $request->validate([
    'name' => 'required|string',
    'email' => 'required|email',
]);
$user = User::create($validated);
```

## Auditing Workflow
1. **Find Sinks**: Grep for `create(`, `update(`, `fill(` in controllers.
2. **Trace Backwards**: Check the array being passed to the sink.
3. **Verify Neutralization**: 
   - Is it `$request->all()`? $\to$ **Check Model `$fillable`**.
   - Is it `$request->validated()`? $\to$ **Secure**.
   - Is it `$request->only([...])`? $\to$ **Secure**.
4. **Construct Proof**: Identify a sensitive field (e.g., `is_admin`, `balance`, `role`) that is not in `$guarded` or is accidentally in `$fillable`.

## Severity: CRITICAL
Mass Assignment can allow attackers to escalate privileges, grant admin access, or modify any database field.
