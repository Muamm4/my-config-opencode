# Security Audit Reporting & Vulnerability Proofs

This guide defines how to report security findings. To eliminate AI slop and false positives, every finding MUST be accompanied by a Vulnerability Proof.

## Severity Levels

| Level | Description | Examples |
|-------|-------------|----------|
| **CRITICAL** | Immediate exploitation likely, high impact (RCE, Full DB Leak, Admin Takeover) | SQL Injection, Unauthenticated RCE, Critical BAC |
| **HIGH** | Requires specific conditions or user interaction, significant impact | XSS, CSRF, Authenticated BAC, Insecure File Upload |
| **MEDIUM** | Limited impact or requires complex preconditions | Information Disclosure, Verbose Errors, Weak Session Mgmt |
| **LOW** | Best practice violations, minimal impact | Missing Security Headers, Low-impact Info Leak |

## The Vulnerability Proof (MANDATORY)

A finding without a proof is a "suggestion," not a "vulnerability." A proof must demonstrate the path from Source to Sink.

### Proof Requirements:
1. **The Source**: Identify the exact entry point (e.g., `GET /api/user/{id}`).
2. **The Taint Path**: Trace how the input moves through the code (e.g., `UserController@show` $\to$ `UserService@getUser` $\to$ `User::whereRaw`).
3. **The Sink**: Identify the dangerous function where the taint is executed (e.g., `whereRaw` with concatenated string).
4. **The Exploit Scenario**: A concrete example of a payload that would trigger the vulnerability.

## Report Structure

### 1. Finding Header
```
## [SEVERITY] Short Descriptive Title
**File**: path/to/file.php:line
**Category**: Vulnerability Type (e.g., BAC/IDOR)
```

### 2. Vulnerability Proof (The "How")
**Source**: `[HTTP Method] [Endpoint]`
**Taint Path**: `[Method A] -> [Method B] -> [Sink]`
**Exploit Scenario**: 
> "An attacker can send a request to `/api/user/1` and change the ID to `2` to view another user's private data because the controller lacks an ownership check."

### 3. Impact (The "Why")
Explain what an attacker can actually achieve (e.g., "Full access to all user emails and passwords").

### 4. Remediation (The "Fix")
Provide the secure code implementation.

```php
// SECURE IMPLEMENTATION
$user = User::where('id', $id)->where('user_id', auth()->id())->firstOrFail();
```

## Example Report

---

### [CRITICAL] SQL Injection in Search Endpoint

**File**: `app/Http/Controllers/SearchController.php:15`
**Category**: SQL Injection

**Vulnerability Proof**:
- **Source**: `GET /search?q=keyword`
- **Taint Path**: `SearchController@index` $\to$ `DB::table('products')->whereRaw("name LIKE '%$q%'")`
- **Exploit Scenario**: An attacker can provide `q=' OR 1=1 --` to bypass filters and dump the entire products table.

**Impact**: Complete exfiltration of the products table, including hidden or internal-only items.

**Remediation**:
Use parameterized bindings:
```php
$products = DB::table('products')
    ->where('name', 'LIKE', "%{$request->q}%")
    ->get();
```
---

## Output Format

Present findings with:
- Severity badge
- Clear Source $\to$ Sink path
- Concrete Exploit Scenario
- Secure remediation code
