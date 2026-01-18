# MCP Catalog - Project Documentation Index

> **Comprehensive documentation for the MCP (Model Context Protocol) Catalog repository**

## 📚 Documentation Navigation

### Core Documentation

- [Project Overview](PROJECT_OVERVIEW.md) - Purpose, architecture, and tech stack
- [Getting Started Guide](GETTING_STARTED.md) - Quick start and setup instructions
- [Development Workflow](DEVELOPMENT_WORKFLOW.md) - Day-to-day development practices

### Technical Reference

- [MCP Catalog Schema](MCP_CATALOG_SCHEMA.md) - YAML schema specification for MCP servers
- [API Reference](API_REFERENCE.md) - TypeScript/Python API documentation
- [Automation Framework](AUTOMATION_FRAMEWORK.md) - WebdriverIO testing architecture

### Operations & CI/CD

- [CI/CD Workflows](CICD_WORKFLOWS.md) - GitHub Actions pipelines and automation
- [Security Scanning](SECURITY_SCANNING.md) - mcpwn and vulnerability scanning processes
- [Deployment Guide](DEPLOYMENT.md) - Container deployment and OBOT integration

### Contributing

- [Code Style Guide](CODE_STYLE.md) - TypeScript, Python, and YAML conventions
- [Task Completion Checklist](TASK_COMPLETION.md) - Pre-commit and PR requirements
- [Contributing Guidelines](CONTRIBUTING.md) - How to contribute to the catalog

---

## 🎯 Quick Reference

### Project Statistics

- **Total MCP Servers**: 100+ catalog entries
- **Primary Language**: TypeScript (automation), YAML (catalog)
- **Test Framework**: WebdriverIO + Cucumber
- **CI/CD**: GitHub Actions
- **Platform**: macOS/Darwin (primary development)

### Key Directories

```
mcp-catalog/
├── *.yaml                              # MCP server catalog entries
├── mcp-server-validation-automation/   # WebdriverIO test automation
│   ├── src/                           # Test source code
│   │   ├── core/                      # Core utilities and selectors
│   │   ├── features/                  # Cucumber feature files
│   │   ├── steps/                     # Step definitions
│   │   ├── data/                      # Test data (JSON)
│   │   └── utils/                     # Helper utilities
│   ├── wdio.conf.ts                   # WebdriverIO configuration
│   └── auto_eval.ts                   # AI evaluation script
├── scripts/                            # Utility scripts
│   ├── mcp_tool_linter/               # Python security linter
│   ├── upstream_sync/                 # Auto-sync workflow
│   └── validation/                    # Validation utilities
├── .github/workflows/                  # CI/CD pipeline definitions
└── docs/                              # This documentation
```

### Essential Commands

#### Testing & Validation

```bash
# Run all automation tests
cd mcp-server-validation-automation && npm run wdio:all

# Run specific MCP server test
npx wdio run wdio.conf.ts --cucumberOpts.tagExpression='@gitlab'

# Run AI evaluation
npm run eval

# Security linting
cd scripts/mcp_tool_linter && python mcp_tool_linter.py ../..
```

#### Development

```bash
# Install dependencies
cd mcp-server-validation-automation && npm ci

# Run linting
npx eslint src/**/*.ts

# Generate test report
npm run test:report
```

---

## 🔧 MCP Catalog Components

### 1. **Catalog Entries** (Root YAML files)

Each YAML file defines an MCP server with:

- Server metadata (name, description, icon, repository)
- Runtime configuration (remote, containerized, or local)
- Tool definitions and parameters
- Environment variables and authentication requirements

**Example MCP Servers**: GitHub, Slack, Notion, AWS, PostgreSQL, Redis, Supabase, etc.

### 2. **Validation Automation** (mcp-server-validation-automation/)

WebdriverIO-based testing framework that:

- Tests MCP server integrations with OBOT AI platform
- Validates sequential prompt handling
- Performs AI-powered evaluation of responses
- Generates comprehensive test reports

### 3. **Utility Scripts** (scripts/)

#### MCP Tool Linter (`mcp_tool_linter.py`)

- AI-powered security analysis using GPT-4/5
- Detects malicious tool definitions
- Identifies prompt injection risks
- Validates parameter safety

#### AI Tool Analyzer (`ai-analyze-tools.py`)

- Compares tool versions
- Identifies breaking changes
- Assesses impact of updates
- Generates markdown analysis reports

#### Validation Scripts

- Environment requirement extraction
- Tool comparison utilities
- Automated testing helpers

### 4. **CI/CD Workflows** (.github/workflows/)

#### Security Scanning (`mcpwn.yml`)

- Scans containerized MCP servers for vulnerabilities
- Uses mcpwn tool in safe mode
- Generates SARIF reports
- Posts results to PRs

#### Validation Automation (`trigger-automation.yml`)

- Runs WebdriverIO tests on changed catalog entries
- Deploys OBOT container for integration testing
- Validates MCP server functionality
- Reports test results

---

## 📖 Documentation Sections

### [Project Overview](PROJECT_OVERVIEW.md)

Deep dive into:

- Project purpose and goals
- Architecture and design decisions
- Technology stack rationale
- Repository organization

### [MCP Catalog Schema](MCP_CATALOG_SCHEMA.md)

Complete reference for:

- YAML structure and fields
- Runtime configurations (remote, containerized, local)
- Tool preview format
- Environment variable specification
- Validation rules and best practices

### [Automation Framework](AUTOMATION_FRAMEWORK.md)

Technical documentation for:

- WebdriverIO configuration
- Cucumber feature structure
- Page Object Model implementation
- Test data management
- AI evaluation system

### [API Reference](API_REFERENCE.md)

Code-level documentation:

- TypeScript types and interfaces
- Core utility functions
- MCP-specific helpers
- Python script APIs

### [CI/CD Workflows](CICD_WORKFLOWS.md)

Operational guide:

- Workflow triggers and permissions
- Matrix strategy for parallel testing
- Docker container orchestration
- Secret management
- Debugging workflows

### [Code Style Guide](CODE_STYLE.md)

Standards and conventions:

- TypeScript/JavaScript patterns
- Python PEP 8 compliance
- YAML formatting rules
- Git commit conventions
- Documentation requirements

### [Task Completion Checklist](TASK_COMPLETION.md)

Quality assurance:

- Pre-commit verification
- Testing requirements
- Security checks
- PR submission guidelines

---

## 🚀 Getting Started

### Prerequisites

- **Node.js 22+** for automation tests
- **Python 3.13+** for utility scripts
- **Docker** for containerized testing
- **Git** for version control

### Quick Start

```bash
# 1. Clone repository
git clone <repo-url>
cd mcp-catalog

# 2. Install automation dependencies
cd mcp-server-validation-automation
npm ci

# 3. Install Python dependencies (for linting/validation)
cd ../scripts/mcp_tool_linter
pip install -r requirements.txt

# 4. Run tests
cd ../../mcp-server-validation-automation
npm run wdio:all
```

### Adding a New MCP Server

1. Create YAML file in root directory (see [MCP_CATALOG_SCHEMA.md](MCP_CATALOG_SCHEMA.md))
2. Validate YAML syntax and structure
3. Run security linting: `python scripts/mcp_tool_linter/mcp_tool_linter.py <file>.yaml`
4. Test integration (if containerized)
5. Submit PR - CI/CD will validate automatically

---

## 🔗 External Resources

- [Model Context Protocol Specification](https://modelcontextprotocol.io/)
- [OBOT Platform Documentation](https://github.com/obot-platform/obot)
- [WebdriverIO Documentation](https://webdriver.io/)
- [Cucumber BDD Framework](https://cucumber.io/)
- [GitHub Actions Documentation](https://docs.github.com/actions)

---

## 📝 Maintenance Notes

This documentation is maintained alongside the codebase. When making significant changes:

1. Update relevant documentation files
2. Keep INDEX.md synchronized with new sections
3. Update quick reference statistics
4. Maintain cross-references between documents

**Last Updated**: January 2026  
**Documentation Version**: 1.0  
**Maintained by**: MCP Catalog Contributors
