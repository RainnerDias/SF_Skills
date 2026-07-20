# [Project Name]

<!-- Replace with a one-liner describing this project, e.g.: "Agentforce-powered service portal for ACME Corp on Experience Cloud" -->

## Org Context

- **API Version:** 62.0
- **Target Org Alias:** <!-- replace: e.g. moonshot-sandbox -->
- **Scratch Org Def:** `config/project-scratch-def.json`
- **Instance URL:** <!-- replace: e.g. https://yourorg.sandbox.my.salesforce.com -->
- Personal overrides → `CLAUDE.local.md` (gitignored)

## Directory Layout

```
force-app/main/default/
  classes/       Apex classes + test classes (*_Test.cls)
  lwc/           Lightning Web Components
  flows/         Flows and subflows
  objects/       Custom objects, fields, relationships
  permissionsets/ Permission sets
  triggers/      Apex triggers (one per object)
config/
  project-scratch-def.json
```

## Commands

| What | Command |
|---|---|
| Deploy source | `sf project deploy start -d force-app -w 30` |
| Validate (no save) | `sf project deploy start -d force-app --dry-run` |
| Run all tests | `sf apex run test -l RunLocalTests -c -w 10` |
| Run one class | `sf apex run test -n MyClass -c -s` |
| Code analysis | `sf code-analyzer run --workspace ./force-app` |
| Open org | `sf org open` |
| Switch org | `sf config set target-org <alias>` |

## Claude Agents (type `/` to see all)

| Command | What it does |
|---|---|
| `/build` | Deploy `force-app` to org; parse errors with file/line context |
| `/test` | Run Apex tests; surface failures + classes below 75% coverage |
| `/analyze` | Code Analyzer; triage Critical/High findings with fix suggestions |
| `/e2e <scenario>` | Playwright browser test against the live org (no manual login) |

Plus 125+ Salesforce skills: `/platform-apex-generate`, `/agentforce-generate`, `/data360-segment`, `/omnistudio-omniscript-generate`, `/dx-code-analyzer-run`, and more.

## Apex Conventions

- Every class needs a test class; minimum **75% coverage** per class
- **No SOQL or DML inside loops** — bulkify all trigger and batch logic
- No hardcoded IDs — use Custom Labels, Custom Settings, or Custom Metadata
- Trigger pattern: one trigger per object → handler class (`AccountTriggerHandler`)
- `@AuraEnabled(cacheable=true)` for read-only controller methods; omit `cacheable` for mutations
- No `System.debug` in production code

## LWC Conventions

- Component names: **kebab-case** (`my-component`, not `MyComponent`)
- Event names: **camelCase** (`statusChange`, not `status-change`)
- Prefer SLDS utility classes over custom CSS
- Use `@wire` for read-only data; imperative Apex for any DML
- Always handle `isLoading`, `error`, and empty-state in templates

## What to Avoid

- SOQL/DML in loops
- Hardcoded Salesforce IDs
- Unhandled async errors in LWC
- `without sharing` unless explicitly required
- Triggers with business logic (use handler classes)
