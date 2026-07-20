---
name: test
description: Run all local Apex tests with code coverage and report failures and coverage gaps
allowed-tools: Bash(sf apex run test *) Bash(sf apex get test *) Read
argument-hint: [ClassName] [--target-org <alias>]
---

## Pre-flight: Current Org Status
```!
sf org display --json 2>$null
```

## Test Instructions

Run the Apex test suite for this project and report results, failures, and coverage.

**Step 1 — Build the test command**

- If `$ARGUMENTS` contains a class name (no `--` prefix), run only that class:
  ```
  sf apex run test --class-names <ClassName> --synchronous --code-coverage --result-format json --wait 10 --json
  ```
- If `$ARGUMENTS` contains `--target-org <alias>`, add `--target-org <alias>` to the command
- Otherwise run all local tests:
  ```
  sf apex run test --synchronous --code-coverage --result-format json --wait 10 --json
  ```

**Step 2 — Parse `result.summary`**

Report a header line:
```
Tests: <passing> passed, <failing> failed, <skipped> skipped | Coverage: <orgWideCoverage>%
```
Flag with a warning if `orgWideCoverage` < 75%.

**Step 3 — Report failures**

For each entry in `result.tests[]` where `outcome === "Fail"`:
- Show: `FAIL <ApexClass>.<methodName>`
- Show the assertion message from `result.message`
- Show the stack trace line from `result.stackTrace` (first frame only)
- Read the referenced Apex file; find the failing assertion line
- Explain in plain English what the test is checking and why it likely failed
- Suggest the most probable fix (wrong expected value, missing setup data, async timing issue, etc.)

If all tests pass: "All tests passed."

**Step 4 — Report coverage gaps**

For each entry in `result.coverage.coverage[]`:
- Calculate coverage: `coveredLines / (coveredLines + uncoveredLines) * 100`
- If coverage < 75%, report:
  ```
  LOW COVERAGE: <ClassName> — <X>% (needs <Y>% more)
  Uncovered lines: <list of line numbers>
  ```
- Read the class file and identify what the uncovered lines do
- Suggest which test scenarios would cover those lines

**Step 5 — Summary**

End with a prioritized list:
1. Fix failing tests (blocks deployment to production)
2. Increase coverage on classes below 75% (blocks deployment to production)
3. Classes between 75–85% (below the recommended 85% best practice)
