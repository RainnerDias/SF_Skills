# SF_Skills

A Claude Code template for Salesforce development. Clone this repo when starting a new Salesforce project to get a pre-configured environment with 125+ AI skills, custom agents, and project conventions ready to go.

## What's Included

### 125+ Salesforce Skills (`.claude/skills/`)

Slash commands for generation tasks — type `/` in Claude Code to browse them all:

| Category | Example Skills |
|---|---|
| **Platform** | `/platform-apex-generate`, `/platform-apex-test-generate`, `/platform-soql-query`, `/platform-metadata-deploy`, `/platform-custom-object-generate` |
| **Agentforce** | `/agentforce-generate`, `/agentforce-architecture-analyze`, `/agentforce-test`, `/agentforce-observe` |
| **Data Cloud** | `/data360-segment`, `/data360-activate`, `/data360-harmonize`, `/data360-query`, `/data360-schema-get` |
| **OmniStudio** | `/omnistudio-omniscript-generate`, `/omnistudio-flexcard-generate`, `/omnistudio-datamapper-generate` |
| **Experience / LWC** | `/experience-lwc-generate`, `/experience-ui-bundle-frontend-generate`, `/experience-cms-brand-apply` |
| **DX / DevOps** | `/dx-code-analyzer-run`, `/dx-devops-test-pipeline-configure`, `/dx-org-manage` |
| **Design Systems** | `/design-systems-slds-apply`, `/design-systems-slds2-migrate` |
| **Automation** | `/automation-flow-generate` |

### Custom Agents (`.claude/commands/`)

Build-pipeline commands that run real `sf` CLI operations and parse the results:

| Command | Description |
|---|---|
| `/build` | Deploy `force-app` to the connected org. Surfaces errors with file/line references and fix suggestions. Supports `--dry-run` for validation-only runs. |
| `/test` | Run all local Apex tests synchronously with code coverage. Reports failures with root-cause analysis and flags any class below 75% coverage. |
| `/analyze` | Run Salesforce Code Analyzer over `force-app`. Groups findings Critical → Low; explains each Critical/High violation and shows the suggested fix. |
| `/e2e <scenario>` | Drive a real browser against the live Salesforce org via Playwright. Uses `sf org open --url-only` for automatic authenticated login — no manual credentials needed. Describe what to test in natural language. |

### MCP Servers

- **`@salesforce/mcp`** (`.claude/settings.json`) — gives Claude access to your connected Salesforce org: query data, inspect metadata, run Apex
- **`@playwright/mcp`** (`.mcp.json`) — gives Claude a real browser for E2E testing; `--headed` keeps it visible

### `CLAUDE.md`

Project-wide conventions loaded into Claude on every session: org context, directory layout, Apex/LWC rules, command reference, and skill index.

---

## Using This Template

### 1. Create a new repo from this template

Click **"Use this template"** on GitHub, or clone and reinitialize:

```bash
git clone https://github.com/RainnerDias/SF_Skills.git my-new-project
cd my-new-project
git remote set-url origin https://github.com/<you>/my-new-project.git
```

### 2. Configure your org

Edit `CLAUDE.md` — fill in:
- Project name and one-liner
- Target org alias
- Instance URL

Edit `.claude/settings.json` — replace `<your-org-alias>` with your actual alias:
```json
"args": ["@salesforce/mcp", "--orgs", "my-sandbox"]
```

### 3. Authenticate with the Salesforce CLI

```bash
sf org login web --alias my-sandbox --instance-url https://test.salesforce.com
sf config set target-org my-sandbox
```

### 4. Open in Claude Code

```bash
claude .
```

Type `/` to see all available skills and agents.

---

## Requirements

- [Salesforce CLI (`sf`)](https://developer.salesforce.com/tools/salesforcecli)
- [Node.js 18+](https://nodejs.org) (for `npx @salesforce/mcp` and `npx @playwright/mcp`)
- [Claude Code](https://claude.ai/code)
- A connected Salesforce org (sandbox, scratch org, or Developer Edition)

---

## Updating Skills

Skills are locked in `skills-lock.json`. To update to the latest from Salesforce:

```bash
npx skills add forcedotcom/sf-skills --all
```
