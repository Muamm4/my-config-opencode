# Verification Process - Review Workflows

This reference defines the two review workflows: Quick Review and Full Review.

## Quick Review (Trivial Tasks)

For single-file changes, typo fixes, or minimal edits.

### Steps:
1. Run `lsp_diagnostics` on changed files
2. Run build command (if applicable)
3. One-line verdict: "Looks good" or "Found issue: X"

### When to use:
- Single file changed
- Typo or comment fix
- Minor documentation update

## Full Review (Complex Tasks)

For multi-file implementations, new features, or significant changes.

### Steps:
1. **LSP Diagnostics**
   - Run `lsp_diagnostics` on ALL changed files
   - Zero errors required (warnings acceptable with justification)

2. **Build Verification**
   - Run build command (npm build, composer install, etc.)
   - Exit code 0 required

3. **Test Execution**
   - Run available tests
   - All tests must pass

4. **Implementation Verification**
   - Read original request/goal
   - Verify implementation matches
   - Check for scope creep

5. **Security & Quality Checks**
   - Apply quality-checks.md standards
   - Run parallel review agents

6. **Memory Integration**
   - Save summary to `ctx_note`
   - Document: files changed, test results, patterns used

7. **Final Report**
   - Pass/Fail with evidence
   - If fail: specific issues and remediation

## Tools to Use

| Step | Tool |
|------|------|
| Diagnostics | `lsp_diagnostics` |
| Build | `bash` (npm build, composer install, etc.) |
| Tests | `bash` (npm test, pest, etc.) |
| Code reading | `read`, `grep` |
| Context | `call_omo_agent` (Oracle, context mining) |

## Verification Checklist

- [ ] lsp_diagnostics: 0 errors
- [ ] Build: exit code 0
- [ ] Tests: all passing
- [ ] Implementation matches request
- [ ] No scope creep
- [ ] Quality checks passed
- [ ] Memory saved to ctx_note
- [ ] Report delivered

## Failure Handling

If ANY step fails:
1. Stop immediately
2. Report specific failure with evidence
3. Do NOT proceed to next steps
4. Do NOT present to user until resolved