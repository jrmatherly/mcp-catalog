# Project Index: mcp-catalog

**Generated**: 2026-01-15 19:57:00 UTC  
**Last Commit**: 28ed2cc (2026-01-15 19:51:48)  
**Purpose**: MCP (Model Context Protocol) Catalog - Centralized registry of 78 MCP server configurations

---

## 📊 Repository Statistics

| Metric | Count |
| -------- | ------- |
| **MCP Catalog Entries** | 78 YAML files |
| **TypeScript Files** | 13 files |
| **Python Scripts** | 5 files |
| **CI/CD Workflows** | 7 workflows |
| **Test Data Files** | 37 JSON files |
| **Documentation** | 11 MD files |

---

## 📁 Project Structure

```
mcp-catalog/
├── *.yaml (78 files)              # MCP server catalog entries
│   ├── Developer Tools: github, gitlab, postman, playwright
│   ├── Databases: postgresql, mongodb, redis, mysql, supabase
│   ├── Cloud: aws, azure, digitalocean, cloudflare, render
│   ├── AI/Search: brave_search, tavily_search, context7, exa_search
│   ├── Productivity: slack, notion, asana, todoist, linear
│   └── Analytics: datadog, grafana, dynatrace, tableau
│
├── mcp-server-validation-automation/    # WebdriverIO test suite
│   ├── src/
│   │   ├── core/           # 7 files - Core utilities
│   │   ├── features/       # Cucumber BDD scenarios
│   │   ├── steps/          # 2 files - Step definitions
│   │   ├── support/        # Test hooks
│   │   ├── utils/          # 2 files - Helper utilities
│   │   └── data/           # 37 JSON test data files
│   ├── auto_eval.ts        # AI evaluation engine (GPT-4)
│   └── wdio.conf.ts        # WebdriverIO configuration
│
├── scripts/
│   ├── mcp_tool_linter/              # AI-powered security linter
│   │   └── mcp_tool_linter.py        # GPT-5 security analysis
│   ├── upstream_sync/                # Catalog sync automation
│   │   └── auto_sync_workflow.py     # Upstream registry sync
│   └── validation/                   # Validation utilities
│       ├── ai-analyze-tools.py       # GPT-4 tool comparison
│       └── get-env-requirements.py   # Env var extraction
│
├── .github/
│   ├── workflows/          # 7 CI/CD pipeline definitions
│   │   ├── mcpwn.yml              # Security scanning
│   │   ├── trigger-automation.yml  # Integration tests
│   │   └── sync-mcp-registry.yml  # Upstream sync
│   └── scripts/
│       └── sarif_to_md.py         # SARIF report converter
│
└── docs/                   # Comprehensive documentation
    ├── INDEX.md            # Documentation hub
    ├── PROJECT_OVERVIEW.md # Architecture guide
    └── API_REFERENCE.md    # API documentation
```

---

## 🚀 Entry Points

### Test Automation

- **Main Config**: `mcp-server-validation-automation/wdio.conf.ts`
  - WebdriverIO configuration with Cucumber framework
  - Runs: `npm run wdio:all`
  
- **AI Evaluation**: `mcp-server-validation-automation/auto_eval.ts`
  - GPT-4o-mini powered test response evaluation
  - Runs: `npm run eval`

### Security & Validation

- **Security Linter**: `scripts/mcp_tool_linter/mcp_tool_linter.py`
  - AI-powered malicious tool detection
  - Uses GPT-5 for prompt injection analysis
  - Runs: `python mcp_tool_linter.py <yaml_file>`

- **Tool Analyzer**: `scripts/validation/ai-analyze-tools.py`
  - GPT-4 based version comparison
  - Impact assessment and breaking change detection
  - Runs: `python ai-analyze-tools.py <old> <new> <name> <v1> <v2>`

### CI/CD Pipelines

- **mcpwn Security Scan**: `.github/workflows/mcpwn.yml`
  - Scans containerized MCP servers for vulnerabilities
  - Matrix execution for parallel scanning
  
- **Integration Testing**: `.github/workflows/trigger-automation.yml`
  - WebdriverIO tests with OBOT platform
  - Validates MCP server functionality

---

## 📦 Core Modules

### TypeScript Modules

#### `src/core/` - Test Framework Core (7 modules)

- **func.ts**: Common test utilities
  - `waitForElement()`, `clickElement()`, `typeText()`
  - Element interaction with retry logic
  
- **mcpFunc.ts**: MCP-specific operations
  - `createCatalogEntry()`, `selectMCPServer()`, `sendPrompt()`
  - OBOT API integration
  
- **selectors.ts**: UI element selectors
  - Navigation, chat, MCP server selection
  - Data-testid based selectors
  
- **types.ts**: TypeScript interfaces
  - `MCPServer`, `RemoteConfig`, `ContainerizedConfig`
  - Test configuration types
  
- **timeouts.ts**: Wait time constants
  - Element, page, API, MCP operation timeouts
  
- **softAssert.ts**: Soft assertion framework
  - Collect multiple assertion failures
  
#### `src/steps/` - Cucumber Steps (2 modules)

- **common.ts**: Shared step definitions
- **mcpServer.step.ts**: MCP-specific Gherkin steps
  - Given/When/Then patterns for MCP testing

#### `src/utils/` - Utilities (2 modules)

- **context.ts**: Test context management
- **projectName.model.ts**: Project name utilities

#### `src/support/` - Test Lifecycle

- **hooks.ts**: Before/After hooks
  - Setup, teardown, screenshot on failure

### Python Modules

#### Security & Analysis

- **mcp_tool_linter.py** (400+ lines)
  - `convert_tool_to_openai_format()`: MCP → OpenAI format
  - `check_tool_with_gpt5()`: AI security analysis
  - `lint_file()`: File-level security scanning
  - Exit codes: 0 (safe), 2 (severity), 3 (malicious)

- **ai-analyze-tools.py** (150+ lines)
  - `analyze_with_openai()`: Version comparison analysis
  - `extract_tools_info()`: Tool metadata extraction
  - Markdown report generation

- **auto_sync_workflow.py**: Upstream catalog synchronization
- **get-env-requirements.py**: Environment variable extraction
- **sarif_to_md.py**: SARIF security report converter

---

## 🔧 Configuration Files

### Build & Dependencies

- **package.json**: Node.js dependencies
  - WebdriverIO 9.20.0
  - Cucumber framework
  - OpenAI SDK 6.7.0
  - TypeScript 5.9.3

- **tsconfig.json**: TypeScript configuration
  - Target: ESNext
  - Module: CommonJS
  - Strict mode: Disabled (test flexibility)

- **requirements.txt** (3 locations): Python dependencies
  - `openai>=1.0.0`
  - `pyyaml>=6.0`

### CI/CD Configuration

- **mcpwn.yml**: Security scanning workflow
  - Triggers: PR with YAML changes
  - Matrix: Parallel container scanning
  - Output: SARIF reports to PR comments

- **trigger-automation.yml**: Integration validation
  - Deploys OBOT container
  - Runs WebdriverIO tests
  - Node.js 22, Docker-in-Docker

- **sync-mcp-registry.yml**: Upstream synchronization
- **vuln-scan.yml**: Vulnerability scanning
- **update-image-tag.yml**: Docker image updates

### Project Configuration

- **renovate.json**: Automated dependency updates
- **.gitignore**: Excludes `.kiro/`, `.idea/`, `__pycache__/`
- **wdio.conf.ts**: WebdriverIO framework config
  - Cucumber integration
  - Allure reporting
  - Retry strategies

---

## 📚 Documentation

### Primary Documentation (`docs/`)

- **INDEX.md** (3.5 KB): Documentation navigation hub
  - Quick reference, project statistics
  - Command guide, getting started
  
- **PROJECT_OVERVIEW.md** (15 KB): Architecture deep-dive
  - Component diagrams, data flows
  - Technology stack, design decisions
  - Integration points (OBOT, OpenAI, GitHub)
  
- **API_REFERENCE.md** (20 KB): Complete API documentation
  - TypeScript interfaces and functions
  - Python module APIs
  - Code examples and patterns

### Serena Memory Files (`.serena/memories/`)

- **project_overview.md**: Quick reference
- **suggested_commands.md**: Development commands
- **code_style_and_conventions.md**: Coding standards
- **task_completion_checklist.md**: QA checklist
- **cicd_workflows.md**: CI/CD guide
- **mcp_catalog_schema.md**: YAML schema spec
- **documentation_index.md**: Doc catalog

### README

- **mcp-server-validation-automation/README.md**: Test suite guide
  - Prerequisites, quick start
  - Running tests, contributing

---

## 🧪 Test Coverage

### Test Organization

- **Features**: Cucumber BDD scenarios (`.feature` files)
- **Step Definitions**: Gherkin step implementations
- **Test Data**: 37 MCP server test configs (JSON)
  - Server-specific prompt scenarios
  - Expected tool calls and responses

### Test Types

- **Integration Tests**: E2E OBOT platform testing
- **Security Tests**: mcpwn vulnerability scanning
- **AI Evaluation**: GPT-4 response grading

### Test Data Files (37 JSON files)

Example servers with test data:

- `github.MCP.json`, `gitlab.MCP.json`
- `postgresql.MCP.json`, `redis.MCP.json`
- `aws_eks.MCP.json`, `aws_cdk.MCP.json`
- `slack.MCP.json`, `datadog.MCP.json`

---

## 🔗 Key Dependencies

### TypeScript/Node.js

- **webdriverio** ^9.20.0 - Browser automation
- **@wdio/cucumber-framework** ^9.20.0 - BDD testing
- **openai** ^6.7.0 - AI evaluation
- **axios** ^1.13.0 - HTTP client
- **chai** ^6.2.0 - Assertions
- **allure-commandline** ^2.34.1 - Test reporting

### Python

- **openai** >=1.0.0 - GPT-4/5 API access
- **pyyaml** >=6.0 - YAML processing
- **requests** - HTTP client

### Infrastructure

- **Docker**: Container runtime for testing
- **GitHub Actions**: CI/CD automation
- **OBOT Platform**: AI chatbot test environment
  - Container: `ghcr.io/obot-platform/obot:latest`

---

## 📝 Quick Start

### 1. Install Dependencies

```bash
# Node.js dependencies (automation)
cd mcp-server-validation-automation
npm ci

# Python dependencies (linting)
cd ../scripts/mcp_tool_linter
pip install -r requirements.txt
```

### 2. Run Tests

```bash
# All WebdriverIO tests
cd mcp-server-validation-automation
npm run wdio:all

# Specific MCP server
npx wdio run wdio.conf.ts --cucumberOpts.tagExpression='@gitlab'

# AI evaluation
npm run eval
```

### 3. Security Scanning

```bash
# Lint single YAML file
cd scripts/mcp_tool_linter
python mcp_tool_linter.py ../../github.yaml

# Lint entire catalog
python mcp_tool_linter.py ../.. --json report.json
```

### 4. Add New MCP Server

1. Create `<server>.yaml` in root directory
2. Follow schema: `docs/mcp_catalog_schema.md`
3. Run security lint: `python scripts/mcp_tool_linter/mcp_tool_linter.py <server>.yaml`
4. Create PR - CI/CD validates automatically

---

## 🎯 MCP Catalog Categories

### Developer Tools (12 servers)

github, gitlab, postman, playwright, gitmcp, atlassian, linear, asana, todoist, mondaycom, browserbase, filescom

### Databases (10 servers)

postgresql, mongodb_database, mongodb_atlas_management, mysql, redis, supabase, snowflake, bigquery, elasticsearch, neon, chroma

### Cloud Platforms (8 servers)

aws, aws_eks, aws_cdk, aws_knowledge, aws_documentation, azure, digitalocean, cloudflare, render, cloud_run, terraform

### AI & Search (6 servers)

brave_search, tavily_search, duckduckgo_search, exa_search, context7, deepwiki, ref

### Analytics & Monitoring (5 servers)

datadog, grafana, dynatrace, pagerduty, morningstar, tableau

### Productivity (10 servers)

slack, notion, gmail, outlook, calendar, google-calendar, google-drive, google-sheets, onedrive, excel, word

### Business & Finance (6 servers)

salesforce, hubspot, stripe, square, paypal, wix, wordpress, zapier

### Data & Documents (5 servers)

databricks_genie, databricks_uc_functions, databricks_vector_search, markitdown, firecrawl, antv_charts, microsoft-docs, google-maps-grounding-lite

---

## 🔍 Key File Locations

### Frequently Modified

- **Catalog entries**: `/<server>.yaml`
- **Test data**: `mcp-server-validation-automation/src/data/*.MCP.json`
- **CI/CD workflows**: `.github/workflows/*.yml`

### Configuration

- **TypeScript config**: `mcp-server-validation-automation/tsconfig.json`
- **WDIO config**: `mcp-server-validation-automation/wdio.conf.ts`
- **Python requirements**: `scripts/*/requirements.txt`

### Documentation

- **Main docs**: `docs/*.md`
- **Serena memories**: `.serena/memories/*.md`
- **Automation README**: `mcp-server-validation-automation/README.md`

---

## 📐 Architecture Patterns

### Test Architecture

- **Pattern**: Page Object Model (POM)
- **Framework**: WebdriverIO + Cucumber (BDD)
- **AI Integration**: OpenAI GPT-4 for evaluation
- **Reporting**: Allure test reports

### CI/CD Patterns

- **Matrix Strategy**: Parallel job execution
- **Changed Files**: Only test modified YAML files
- **Docker-in-Docker**: Nested container testing
- **SARIF Reports**: Security findings as PR comments

### Code Organization

- **Separation of Concerns**: Core/Steps/Support/Utils
- **Reusable Functions**: Common utilities in `core/`
- **Test Data**: External JSON files
- **Configuration**: Centralized in config files

---

## 🚨 Important Notes

### Security

- All MCP tools scanned by GPT-5 for malicious intent
- SARIF reports on every PR with YAML changes
- mcpwn scanner for containerized servers
- Sensitive data marked in YAML configs

### Performance

- **CI Optimization**: Only changed files tested
- **Parallel Execution**: Matrix strategies for speed
- **Caching**: NPM dependencies cached
- **Timeouts**: Configured per operation type

### Maintenance

- **Renovate**: Automated dependency updates
- **Git**: Clean commit history on main branch
- **Documentation**: Keep docs/ and memories/ in sync

---

## 📊 Token Efficiency

**This Index**: ~4.5 KB (≈3,000 tokens)  
**Full Codebase Read**: ≈58,000 tokens  
**Savings per Session**: 55,000 tokens (94% reduction)  
**100 Sessions**: 5.5M tokens saved

---

## 🔗 Related Resources

- **MCP Specification**: https://modelcontextprotocol.io/
- **OBOT Platform**: https://github.com/obot-platform/obot
- **WebdriverIO Docs**: https://webdriver.io/
- **Documentation Hub**: `docs/INDEX.md`

---

**Index Version**: 1.0  
**Maintained by**: MCP Catalog Contributors  
**Index Type**: Human + Machine Readable
