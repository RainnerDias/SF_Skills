---
name: build
description: Deploy force-app source to the connected Salesforce org and surface any errors
allowed-tools: Bash(sf project deploy start *) Bash(sf org display *)
argument-hint: [--target-org <alias>] [--dry-run]
---

## Pre-flight: Current Org Status
```!
sf org display --json 2>$null
```

## Deploy Instructions

Deploy the Salesforce source in `force-app` to the connected org.

**Step 1 — Determine target org**
- If `$ARGUMENTS` contains `--target-org <alias>`, extract the alias and use it in all `sf` commands
- Otherwise use the project default org (no `--target-org` flag needed)

**Step 2 — Build the deploy command**

If `$ARGUMENTS` contains `--dry-run`, run validation only (no save):
```
sf project deploy start --source-dir force-app --dry-run --wait 30 --json
```

Otherwise, run a full deploy:
```
sf project deploy start --source-dir force-app --wait 30 --json
```

**Step 3 — Parse the JSON result**

On **success** (`result.status === "Succeeded"`):
- Count deployed components by type (Apex classes, LWC bundles, flows, objects, etc.)
- Report: "Deployed X components to <orgAlias>: Y Apex classes, Z LWC bundles, ..."
- List any warnings from `result.details.componentWarnings[]`

On **failure** (`result.status === "Failed"`):
- For each entry in `result.details.componentFailures[]`:
  - Show: `[ERROR] <fileName>:<lineNumber>:<columnNumber> — <problem>`
  - Use markdown link format so lines are clickable: `[FileName.cls:42](force-app/main/default/classes/FileName.cls#L42)`
  - Explain the most likely cause of the error in one sentence
  - Suggest a concrete fix (e.g., "Add missing `return` statement", "Declare variable before use")
- Group multiple errors in the same file together
- End with a summary: "X of Y components failed. Fix the errors above and run `/build` again."

**Step 4 — If this was a dry-run**, end with:
"Validation passed. Run `/build` without `--dry-run` to deploy."
