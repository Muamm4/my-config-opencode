# Dependency Audit (composer.lock)

Modern applications rely on hundreds of third-party packages. A vulnerability in a dependency is a vulnerability in your application.

## 1. The Audit Process

The primary goal is to identify packages with known CVEs (Common Vulnerabilities and Exposures).

### Automated Scanning
The first step is always to use the built-in Composer audit tool.

```bash
composer audit
```

### Manual Review of `composer.lock`
If automated tools are unavailable, review the `composer.lock` file for:
- **Outdated Versions**: Packages that are several major versions behind.
- **Abandoned Packages**: Packages that are no longer maintained.
- **Suspicious Packages**: Packages with names that mimic popular ones (Typosquatting).

## 2. High-Risk Dependency Categories

Pay extra attention to packages that handle:
- **Authentication/Authorization**: (e.g., JWT libraries, OAuth providers).
- **Data Parsing**: (e.g., XML, YAML, JSON parsers - prone to XXE or Injection).
- **Template Engines**: (e.g., Twig, Blade extensions - prone to SSTI).
- **Database Drivers**: (e.g., custom PDO wrappers).

## 3. Remediation Steps

1. **Update**: Run `composer update [package-name]` to move to a patched version.
2. **Replace**: If a package is abandoned or fundamentally insecure, find a maintained alternative.
3. **Mitigate**: If an update is impossible, implement a wrapper to sanitize the input before it reaches the vulnerable dependency.

## Severity: HIGH
A critical vulnerability in a core dependency (like a framework or a security library) can lead to full system compromise.
