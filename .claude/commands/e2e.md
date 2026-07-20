---
name: e2e
description: Test Salesforce functionality end-to-end using a real browser via Playwright MCP — no manual login required
allowed-tools: Bash(sf org open *) Bash(sf org display *)
argument-hint: <describe what to test> [--target-org <alias>]
---

## Pre-flight: Org Info
```!
sf org display --json 2>$null
```

## E2E Test Instructions

Drive a real browser against the live Salesforce org to test `$ARGUMENTS`.

**Step 1 — Get an authenticated org URL**

Run:
```
sf org open --url-only --json
```
(Add `--target-org <alias>` if `$ARGUMENTS` contains `--target-org <alias>`.)

Extract `result.url` from the JSON — this is a `frontdoor.jsp?sid=...` URL that logs in the browser session automatically without needing credentials.

**Step 2 — Open the org in the browser**

Call `browser_navigate` with the `frontdoor.jsp` URL.

Then call `browser_snapshot` to read the accessibility tree and confirm the page loaded successfully (look for the Salesforce navigation bar or App Launcher icon). If the snapshot shows a login page instead, report: "Authentication failed — run `sf org login web` to re-authenticate."

**Step 3 — Execute the test scenario**

Based on what the user asked to test in `$ARGUMENTS`, navigate and interact with the org using these Playwright MCP tools:

| Tool | When to use |
|---|---|
| `browser_navigate` | Go to a specific Salesforce URL or Lightning page |
| `browser_snapshot` | Read the current page state (accessibility tree) — use after every action |
| `browser_click` | Click buttons, links, tabs, checkboxes |
| `browser_type` | Type into search boxes and text inputs |
| `browser_fill_form` | Fill multiple form fields at once |
| `browser_select_option` | Choose a picklist or dropdown value |
| `browser_wait_for` | Wait for text to appear (use after Salesforce async operations like saves, agent responses) |
| `browser_take_screenshot` | Capture evidence at key steps (before/after critical actions) |
| `browser_console_messages` | Check for JavaScript errors on the page |

**Salesforce-specific patterns:**

- Lightning Experience navigation: `browser_navigate` to `<instanceUrl>/lightning/n/<pageName>`
- App Launcher: click the 9-dot grid icon, then type the app name
- Record pages: `<instanceUrl>/lightning/r/<ObjectName>/<recordId>/view`
- Setup: `<instanceUrl>/lightning/setup/Home/home`
- After clicking "Save" or "Submit": always call `browser_wait_for` with expected success text before reading the result
- Agentforce chat: wait for the agent typing indicator to disappear before reading the response

**Step 4 — Verify outcomes**

After each major action:
1. Call `browser_snapshot` to read the updated page state
2. Check for expected text, elements, or states
3. Call `browser_take_screenshot` to capture evidence
4. Note any unexpected errors, toast messages, or console warnings from `browser_console_messages`

**Step 5 — Report results**

For each scenario tested:
- **PASS** — what was verified and how (include screenshot reference)
- **FAIL** — what was expected vs. what actually happened, screenshot, and console errors
- **BLOCKED** — if a prerequisite was missing (e.g., missing test data, feature not enabled)

End with a summary: "X scenarios tested: Y passed, Z failed." and a recommended next step for any failures.
