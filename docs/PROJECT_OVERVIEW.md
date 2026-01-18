# MCP Catalog - Project Overview

## Purpose

The **MCP Catalog** is a comprehensive registry of Model Context Protocol (MCP) servers, providing a centralized repository of configuration files, metadata, and integration specifications for various MCP-compatible services and tools.

### What is MCP?

Model Context Protocol (MCP) is a standardized protocol for AI assistants to interact with external tools, services, and data sources. It enables seamless integration between Large Language Models (LLMs) and:

- Developer tools (GitHub, GitLab, Postman)
- Cloud platforms (AWS, Azure, Google Cloud)
- Databases (PostgreSQL, MongoDB, Redis, Supabase)
- AI services (OpenAI, Databricks, search engines)
- Productivity tools (Slack, Notion, Asana, Todoist)
- And many more services

### Project Goals

1. **Centralized Catalog**: Maintain a single source of truth for MCP server configurations
2. **Quality Assurance**: Automated validation and testing of MCP integrations
3. **Security**: AI-powered security scanning for malicious tool definitions
4. **Documentation**: Comprehensive metadata and usage examples for each server
5. **Integration Testing**: Validate MCP servers work correctly with OBOT AI platform

---

## Architecture

### High-Level Components

```
┌─────────────────────────────────────────────────────────────┐
│                    MCP Catalog Repository                    │
├─────────────────────────────────────────────────────────────┤
│                                                               │
│  ┌───────────────┐  ┌──────────────┐  ┌─────────────────┐  │
│  │ YAML Catalog  │  │  Validation  │  │   Utility       │  │
│  │   Entries     │  │  Automation  │  │   Scripts       │  │
│  │  (100+ files) │  │  (WDIO/TS)   │  │   (Python)      │  │
│  └───────┬───────┘  └──────┬───────┘  └────────┬────────┘  │
│          │                  │                    │           │
│          └──────────────────┼────────────────────┘           │
│                             │                                │
├─────────────────────────────┼────────────────────────────────┤
│                    CI/CD Pipeline (GitHub Actions)           │
│                             │                                │
│  ┌────────────┐   ┌────────▼─────────┐   ┌──────────────┐  │
│  │   mcpwn    │   │   Integration    │   │   Upstream   │  │
│  │  Security  │   │    Validation    │   │     Sync     │  │
│  │   Scan     │   │   (OBOT Tests)   │   │              │  │
│  └────────────┘   └──────────────────┘   └──────────────┘  │
└─────────────────────────────────────────────────────────────┘
```

### Component Details

#### 1. YAML Catalog Entries (Root Directory)

- **Purpose**: Define MCP server configurations
- **Count**: 100+ individual server definitions
- **Format**: YAML with strict schema validation
- **Contents**: Server metadata, runtime config, tools, authentication

**Example servers**:

- `github.yaml` - GitHub API integration
- `postgresql.yaml` - PostgreSQL database access
- `slack.yaml` - Slack messaging platform
- `aws.yaml` - Amazon Web Services
- `notion.yaml` - Notion workspace integration

#### 2. Validation Automation (`mcp-server-validation-automation/`)

- **Framework**: WebdriverIO 9.x + Cucumber (BDD)
- **Language**: TypeScript
- **Purpose**: End-to-end integration testing
- **Test Target**: OBOT AI platform (containerized)
- **AI Evaluation**: OpenAI-powered response grading

**Architecture**:

```
src/
├── core/           # Core utilities, selectors, types
│   ├── func.ts           # Common test functions
│   ├── mcpFunc.ts        # MCP-specific helpers
│   ├── selectors.ts      # UI element selectors
│   ├── types.ts          # TypeScript interfaces
│   └── timeouts.ts       # Wait configurations
├── features/       # Cucumber feature files (Gherkin)
│   └── mcpServer.feature
├── steps/          # Step definitions
│   ├── common.ts         # Shared steps
│   └── mcpServer.step.ts # MCP-specific steps
├── support/        # Test hooks and setup
│   └── hooks.ts
├── utils/          # Utility functions
│   ├── context.ts        # Test context management
│   └── projectName.model.ts
└── data/           # Test data files (JSON)
    └── *.MCP.json        # Server-specific test data
```

#### 3. Utility Scripts (`scripts/`)

##### MCP Tool Linter (`mcp_tool_linter/`)

- **Language**: Python 3
- **Purpose**: AI-powered security analysis
- **AI Model**: GPT-4/GPT-5 via OpenAI API
- **Features**:
  - Detects malicious tool definitions
  - Identifies prompt injection vulnerabilities
  - Analyzes parameter safety
  - Risk assessment (low/medium/high/critical)
  - OpenAI function calling format conversion

##### Validation Scripts (`validation/`)

- **get-env-requirements.py**: Extracts required environment variables
- **ai-analyze-tools.py**: Compares tool versions using GPT-4
- **compare-mcp-tools.sh**: Shell-based tool comparison

##### Upstream Sync (`upstream_sync/`)

- **Purpose**: Synchronize with official MCP registry
- **Features**: AI categorization, conflict resolution

#### 4. CI/CD Workflows (`.github/workflows/`)

##### Security Scanning (`mcpwn.yml`)

- **Trigger**: Pull requests with YAML changes
- **Tool**: mcpwn security scanner
- **Process**:
  1. Extract changed YAML files with `containerizedConfig`
  2. Start MCP server containers
  3. Run mcpwn scan in safe mode
  4. Generate SARIF security report
  5. Post results to PR as comment

##### Integration Validation (`trigger-automation.yml`)

- **Trigger**: Pull requests with YAML changes
- **Infrastructure**:
  - OBOT container (AI chatbot platform)
  - Docker-in-Docker for MCP servers
  - Node.js 22 runtime
- **Process**:
  1. Deploy OBOT platform
  2. Create catalog entry via API
  3. Run WebdriverIO tests
  4. Validate MCP server functionality

---

## Technology Stack

### Core Technologies

#### TypeScript/JavaScript Stack

- **Runtime**: Node.js 22
- **Language**: TypeScript 5.x (target: ESNext)
- **Testing**: WebdriverIO 9.20+ (E2E automation)
- **BDD**: Cucumber framework (@wdio/cucumber-framework)
- **Assertion**: Chai 6.x
- **Reporting**: Allure (test reports)

#### Python Stack

- **Version**: Python 3.13+
- **Core Libraries**:
  - `openai>=1.0.0` - AI-powered analysis
  - `pyyaml>=6.0` - YAML processing
  - `requests` - HTTP client
  - `glob` - File pattern matching

#### Infrastructure

- **Containerization**: Docker
- **CI/CD**: GitHub Actions
- **Version Control**: Git
- **Platform**: macOS/Darwin (primary), Linux (CI)

### Development Tools

#### Linting & Code Quality

- **TypeScript**: ESLint with Airbnb base config
- **Plugins**:
  - `@typescript-eslint/eslint-plugin`
  - `@typescript-eslint/parser`
  - `eslint-plugin-wdio`
  - `eslint-plugin-import`
- **Python**: PEP 8 compliance

#### Test Automation Tools

- **Browser Automation**: WebdriverIO (Selenium WebDriver)
- **Test Data**: JSON files, Faker.js for generation
- **Parallel Execution**: WDIO local runner with multiple workers
- **Debugging**: WDIO DevTools service

#### AI Integration

- **OpenAI API**: GPT-4o-mini, GPT-4, GPT-5
- **Use Cases**:
  - Security analysis of tool definitions
  - Test response evaluation
  - Version comparison and impact analysis

---

## Data Flow

### MCP Server Addition Workflow

```
Developer creates YAML file
         │
         ▼
   Git commit & push
         │
         ▼
   GitHub PR created
         │
         ├─────────────────┬─────────────────┐
         ▼                 ▼                 ▼
    Changed files    Matrix generation  PR checks
    detection             │
         │                ▼
         │          Parallel jobs
         │          (per server)
         │                │
         ├────────────────┴────────────────┐
         ▼                                  ▼
   mcpwn security scan          Integration validation
         │                                  │
         ▼                                  ▼
   SARIF report                    WDIO test results
         │                                  │
         └──────────────┬───────────────────┘
                        ▼
                  PR comment with
                  test results
                        │
                        ▼
                  Manual review
                        │
                        ▼
                   Merge to main
```

### Test Execution Flow

```
WDIO test starts
         │
         ▼
   OBOT container deployed
         │
         ▼
   Create catalog entry via API
         │
         ▼
   Navigate to OBOT UI
         │
         ▼
   Select MCP server
         │
         ▼
   Execute test prompts
         │
         ▼
   Capture responses
         │
         ▼
   AI evaluation (GPT-4)
         │
         ▼
   Generate report
         │
         ▼
   Cleanup containers
```

---

## Repository Structure

```
mcp-catalog/
├── *.yaml                              # MCP server catalog entries (100+)
│
├── mcp-server-validation-automation/   # Test automation suite
│   ├── src/
│   │   ├── core/                      # Core utilities
│   │   ├── features/                  # Cucumber features
│   │   ├── steps/                     # Step definitions
│   │   ├── support/                   # Hooks and setup
│   │   ├── utils/                     # Helper functions
│   │   └── data/                      # Test data (JSON)
│   ├── wdio.conf.ts                   # WebdriverIO config
│   ├── auto_eval.ts                   # AI evaluation
│   ├── package.json                   # Dependencies
│   └── tsconfig.json                  # TypeScript config
│
├── scripts/                            # Utility scripts
│   ├── mcp_tool_linter/
│   │   ├── mcp_tool_linter.py         # Security linter
│   │   └── requirements.txt
│   ├── upstream_sync/
│   │   ├── auto_sync_workflow.py      # Sync automation
│   │   └── requirements.txt
│   └── validation/
│       ├── get-env-requirements.py    # Env var extraction
│       ├── ai-analyze-tools.py        # Version comparison
│       └── requirements.txt
│
├── .github/
│   ├── workflows/                     # CI/CD pipelines
│   │   ├── mcpwn.yml                 # Security scanning
│   │   ├── trigger-automation.yml     # Integration tests
│   │   ├── sync-mcp-registry.yml      # Upstream sync
│   │   └── vuln-scan.yml             # Vulnerability scan
│   └── scripts/
│       └── sarif_to_md.py            # SARIF converter
│
├── docs/                               # Documentation
│   ├── INDEX.md                       # This documentation
│   ├── PROJECT_OVERVIEW.md
│   ├── MCP_CATALOG_SCHEMA.md
│   └── ...
│
├── .serena/                           # Serena project config
├── .gitignore
└── renovate.json                      # Dependency updates
```

---

## Development Platform

### Primary Development Environment

- **OS**: macOS (Darwin 24.6.0)
- **Shell**: Bash/Zsh
- **Container Runtime**: Docker Desktop
- **IDE**: Any (VS Code recommended)

### CI/CD Environment

- **Runners**: Ubuntu 22.04 (depot-ubuntu-22.04)
- **Node.js**: Version 22
- **Python**: Version 3.13
- **Container Registry**: GitHub Container Registry (ghcr.io)

---

## Integration Points

### OBOT AI Platform

- **Purpose**: AI chatbot platform for testing MCP integrations
- **Container**: `ghcr.io/obot-platform/obot:latest`
- **API**: REST API at `http://localhost:8080/api`
- **Endpoints**:
  - `/api/me` - Authentication check
  - `/api/mcp-catalogs/default/entries` - Catalog management

### OpenAI API

- **Models Used**:
  - GPT-4o-mini (cost-effective, tool analysis)
  - GPT-4 (comprehensive analysis)
  - GPT-5 (advanced security scanning)
- **Use Cases**:
  - Security vulnerability detection
  - Test response evaluation
  - Version impact analysis

### GitHub Container Registry

- **Images Pulled**:
  - OBOT platform container
  - MCP server containers for testing
- **Authentication**: GitHub token (automatically provided)

---

## Key Design Decisions

### Why YAML for Catalog?

- **Human-readable**: Easy to edit and review
- **Structured**: Schema validation ensures consistency
- **Git-friendly**: Clear diffs, easy conflict resolution
- **Industry standard**: Wide tooling support

### Why WebdriverIO + Cucumber?

- **BDD**: Behavior-driven tests readable by non-developers
- **Cross-browser**: Chrome, Firefox, Edge support
- **Mature**: Stable, well-documented framework
- **Integration**: Works well with Docker, CI/CD

### Why AI-Powered Security Scanning?

- **Advanced Detection**: LLMs identify subtle prompt injection risks
- **Context Understanding**: Analyzes intent, not just patterns
- **Evolving Threats**: Adapts to new attack vectors
- **Automated**: Scales to 100+ catalog entries

### Why Containerization?

- **Isolation**: Each MCP server runs in clean environment
- **Consistency**: Same behavior local and CI
- **Security**: Sandboxed execution
- **Scalability**: Parallel testing of multiple servers

---

## Performance Characteristics

### Catalog Size

- **Current**: 100+ MCP servers
- **Growth Rate**: ~5-10 new servers per month
- **File Size**: Average 5-15 KB per YAML file

### Test Execution

- **Single Test Duration**: 2-5 minutes per MCP server
- **Parallel Execution**: Up to 10 concurrent jobs in CI
- **Total Suite Duration**: ~30-45 minutes for full catalog

### CI/CD Performance

- **PR Validation**: Only changed files tested (optimized)
- **Matrix Strategy**: Parallel execution reduces wait time
- **Caching**: NPM dependencies cached for faster builds

---

## Future Roadmap

### Planned Enhancements

1. **Expanded Catalog**: Add 50+ more MCP servers
2. **Enhanced Testing**: More comprehensive prompt scenarios
3. **Performance Monitoring**: Track MCP server response times
4. **Documentation**: Auto-generated API docs from YAML
5. **Community**: Open contribution process

### Technical Improvements

1. **Type Safety**: Stricter TypeScript configurations
2. **Test Coverage**: Unit tests for utility functions
3. **Monitoring**: Real-time dashboard for catalog health
4. **Versioning**: Support multiple MCP protocol versions

---

## Related Documentation

- [MCP Catalog Schema Reference](MCP_CATALOG_SCHEMA.md)
- [Automation Framework Guide](AUTOMATION_FRAMEWORK.md)
- [CI/CD Workflows Documentation](CICD_WORKFLOWS.md)
- [Contributing Guidelines](CONTRIBUTING.md)
