---
name: analyze
description: Run Salesforce Code Analyzer over force-app and triage findings by severity with fix suggestions
allowed-tools: Bash(sf code-analyzer run *) Read
---

## Analyze Instructions

Run Salesforce Code Analyzer over `force-app` and present findings grouped by severity with actionable fix guidance.

**Step 1 — Run the analyzer**

```
sf code-analyzer run --workspace ./force-app --include-fixes --include-suggestions --output-file ca-results.json
```

If the command fails because `sf code-analyzer` is not installed, tell the user:
"Install the Code Analyzer plugin first: `sf plugins install @salesforce/plugin-code-analyzer`"

**Step 2 — Parse and group findings**

Read `ca-results.json`. Group all violations into four buckets:
- **Critical** — security vulnerabilities, data loss risks
- **High** — governor limit violations, performance killers, SOQL/DML in loops
- **Moderate** — code quality, maintainability issues
- **Low** — style, naming conventions, minor improvements

**Step 3 — Detail Critical and High findings**

For each Critical or High finding:

```
[CRITICAL|HIGH] <RuleName>
File: <filePath>:<lineNumber>
```

Then:
1. **Offending code** — Read the file; quote the 3–5 lines around the violation
2. **Why it matters** — One sentence: governor limit impact, security risk, or data integrity issue
3. **How to fix it** — Concrete code change or pattern to apply (show the fixed code if short)
4. If the JSON includes a `fixes[]` or `suggestions[]` entry, show it

Common rules to explain clearly:
- `AvoidDmlStatementsInLoops` → move DML outside the loop into a List/Map, bulk operation
- `AvoidSoqlInLoops` → collect IDs first, then query once outside the loop
- `ApexSOQLInjection` → use bind variables (`:variable`) instead of string concatenation
- `ApexCRUDViolation` → add `Schema.sObjectType.Account.isAccessible()` checks
- `NullPointerException` (PMD) → add null checks before dereferencing

**Step 4 — Summarize Moderate and Low**

Show counts only:
```
Moderate: X findings across Y files
Low: X findings across Y files
```
Do not enumerate each one. Offer: "Run `/analyze` with a focus on Moderate findings to drill in."

**Step 5 — Recommendation**

End with a prioritized action list:
1. Fix all Critical findings before any deployment
2. Fix High findings before production deployment
3. Address Moderate findings in the next sprint
4. Clean up Low findings opportunistically

Delete `ca-results.json` after reading it (temp output file).
