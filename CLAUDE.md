# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is the **MCP Catalog** - a centralized registry of 78+ Model Context Protocol (MCP) server configurations. The repository consists of:

1. **YAML catalog entries** (root directory): MCP server definitions organized by category
2. **WebdriverIO test automation** (`mcp-server-validation-automation/`): Integration tests that validate MCP servers work with OBOT AI platform
3. **Security tooling** (`scripts/`): AI-powered linters and analyzers using GPT-4/5
4. **CI/CD workflows** (`.github/workflows/`): Automated security scanning and validation

## Core Architecture

### Catalog Structure

Each YAML file in the root directory represents one MCP server. The schema requires:

- **Basic metadata**: `name`, `shortDescription`, `description`, `icon`, `repoURL`
- **Runtime configuration**: One of `remoteConfig`, `containerizedConfig`, or `localConfig`
- **Optional fields**: `toolPreview` (array of tool definitions), `env` (environment variables), `metadata` (categories, unsupported tools)

Runtime types determine how the server is accessed:

- `remote`: Hosted API (requires `hostname` and authentication `headers`)
- `containerized`: Docker container (requires `image`, `port`, optional `path` and `args`)
- `local`: Runs on local machine

### Test Automation Architecture

Located in `mcp-server-validation-automation/`, this uses WebdriverIO + Cucumber for BDD testing:

**Core structure**:

- `src/core/`: Shared utilities - `func.ts` (element interaction), `mcpFunc.ts` (MCP-specific operations), `selectors.ts` (UI locators), `types.ts` (TypeScript interfaces)
- `src/steps/`: Cucumber step definitions - `mcpServer.step.ts` implements Given/When/Then patterns
- `src/data/`: JSON test data files (one per MCP server) containing prompt scenarios
- `auto_eval.ts`: AI evaluation engine using GPT-4o-mini to grade test responses

**Key functions** (`src/core/mcpFunc.ts`):

- `sendPromptAndWaitForReply()`: Sends prompt to OBOT, waits for bot reply to stabilize
- `sendPromptValidateAndCollect()`: Sends prompt, captures tools called and response text
- `aggregateToolResponses()`: Collects all tool calls and responses into structured report

**Test flow**:

1. OBOT container deployed via CI/CD
2. Test creates catalog entry via API
3. WebdriverIO navigates to OBOT UI and selects MCP server
4. Test sends sequential prompts from JSON data file
5. Captures tool calls and responses
6. AI evaluation grades response quality

### Security Scanning

`scripts/mcp_tool_linter/mcp_tool_linter.py` analyzes YAML files for malicious tool definitions:

- Converts MCP tools to OpenAI function format
- Uses GPT-5 to detect prompt injection, data exfiltration, unsafe operations
- Returns severity (low/medium/high/critical) and malicious flag
- Exit codes: 0 (safe), 2 (severity threshold), 3 (malicious detected)

### CI/CD Pipeline Architecture

**mcpwn.yml**: Security scanning workflow

- Triggered on PR with YAML changes
- Matrix strategy: extracts changed files with `containerizedConfig`, runs parallel scans
- Starts each MCP server in Docker, runs mcpwn security scan, posts SARIF results to PR

**trigger-automation.yml**: Integration validation

- Deploys OBOT container, creates catalog entry, runs WebdriverIO tests
- Only processes changed YAML files (optimized for speed)
- Node.js 22 environment with Docker-in-Docker support

## Common Commands

### Test Automation

```bash
# Install dependencies
cd mcp-server-validation-automation && npm ci

# Run all WebdriverIO tests
npm run wdio:all

# Run specific MCP server test by tag
npx wdio run wdio.conf.ts --cucumberOpts.tagExpression='@gitlab'

# Run specific scenario by name
npx wdio run wdio.conf.ts --cucumberOpts.name="^Validate test-github MCP Server"

# Run AI evaluation on test results
npm run eval

# Generate Allure test report
npm run test:report
```

### Security Linting

```bash
# Lint single YAML file
cd scripts/mcp_tool_linter
python mcp_tool_linter.py ../../github.yaml

# Lint all catalog entries
python mcp_tool_linter.py ../.. --json report.json

# Set custom failure threshold
python mcp_tool_linter.py ../../github.yaml --fail-on medium

# Output OpenAI format only (no analysis)
python mcp_tool_linter.py ../../github.yaml --openai-format
```

### Tool Version Comparison

```bash
cd scripts/validation
python ai-analyze-tools.py old_tools.json new_tools.json package-name v1.0.0 v2.0.0
```

## Adding a New MCP Server

1. **Create YAML file** in root directory (`<server-name>.yaml`)
   - Follow schema in `.serena/memories/mcp_catalog_schema.md`
   - Use 2-space indentation, mark sensitive fields as `sensitive: true`
   - Include `toolPreview` with snake_case tool names

2. **Run security linter**:

   ```bash
   python scripts/mcp_tool_linter/mcp_tool_linter.py <server-name>.yaml
   ```

3. **Create test data file** (optional, for integration testing):
   - Add `mcp-server-validation-automation/src/data/<server-name>.MCP.json`
   - Include prompt scenarios and expected tool calls

4. **Submit PR**:
   - CI/CD automatically runs security scan (mcpwn) and integration tests
   - Address any security findings or test failures
   - mcpwn SARIF results posted as PR comment

## Critical Development Notes

### YAML Schema Validation

- **Required fields**: Always include `name`, `shortDescription`, `description`, `icon`, `repoURL`, `runtime`
- **Runtime configs**: Must match runtime type - `remoteConfig` for remote, `containerizedConfig` for containerized
- **Tool parameters**: Mark optional params with "(optional)" suffix in description
- **Environment variables**: Use UPPER_SNAKE_CASE for `env.key`, mark secrets as `sensitive: true`

### Test Data Format

JSON files in `src/data/` structure:

```json
{
  "prompts": [
    {
      "text": "Create an issue titled 'Test Issue'",
      "expectedTools": ["create_issue"],
      "validateResponse": true
    }
  ]
}
```

### WebdriverIO Patterns

- **Selectors**: Use data-testid attributes from `src/core/selectors.ts`
- **Waits**: Bot responses stabilize after 3 consecutive identical texts (1 second apart)
- **Tool detection**: Extract tool names from UI using XPath: `//div[@class="flex flex-col"]/div[@class="mb-1 flex items-center space-x-2"]/span[1]`
- **Response collection**: Wait for new `message-content` div to appear before extracting reply

### Security Best Practices

- All catalog entries scanned by GPT-5 for malicious intent
- Never commit API keys or tokens to YAML files
- Use `env` section for credentials, mark as `sensitive: true`
- Test containerized configs locally before committing

## CI/CD Behavior

### Changed Files Detection

Workflows use `tj-actions/changed-files@v47` with `files: **.yaml` to process only modified catalog entries, optimizing CI time.

### Matrix Execution

Both security and validation workflows build dynamic matrices from changed YAML files:

```bash
yq eval '.containerizedConfig' "file.yaml" -o=json
```

Only files with `containerizedConfig` (not null) are included in security scanning matrix.

### Environment Injection

Docker environment args converted from YAML `env` array:

```bash
DOCKER_ENV_ARGS=$(echo "$ENV_JSON" | jq -r 'map("-e " + .key) | join(" ")')
```

## Documentation References

- **Full index**: `PROJECT_INDEX.md` (3KB, 94% token reduction vs reading full codebase)
- **Architecture guide**: `docs/PROJECT_OVERVIEW.md`
- **API documentation**: `docs/API_REFERENCE.md`
- **MCP schema**: `.serena/memories/mcp_catalog_schema.md`
- **CI/CD workflows**: `.serena/memories/cicd_workflows.md`

## MCP Server Categories

Catalog is organized into 8 categories:

- **Developer Tools** (12): github, gitlab, postman, playwright, atlassian, linear, asana
- **Databases** (11): postgresql, mongodb, redis, mysql, supabase, snowflake, bigquery
- **Cloud Platforms** (13): aws (5 variants), azure, digitalocean, cloudflare, terraform
- **AI/Search** (7): brave_search, tavily_search, duckduckgo_search, context7, exa_search
- **Analytics** (6): datadog, grafana, dynatrace, pagerduty, tableau
- **Productivity** (10): slack, notion, gmail, calendar, google-sheets, onedrive
- **Business** (8): salesforce, hubspot, stripe, square, paypal, zapier, wordpress
- **Data/Documents** (8): databricks (3 variants), markitdown, firecrawl, microsoft-docs

Use these categories in `metadata.categories` field when adding new servers.

## Workspace Integration

This project is part of the AI workspace. Additional resources:

- **Claude Code commands**: `AI/.claude/commands/` (expert-mode, etc.)
- **Shared agents**: `AI/.claude/agents/` (explore, security-audit, etc.)
- **SuperClaude skills**: `/sc:analyze`, `/sc:test`, `/sc:git`, etc.
- **Serena memories**: `AI/.serena/memories/` (task_completion_checklist, mcp_catalog_schema, etc.)
- **GitHub Actions**: Workspace-level PR review and issue triage

For session initialization with full context, run `/expert-mode` from the workspace root.
